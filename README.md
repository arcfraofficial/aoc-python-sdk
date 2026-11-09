# Aoc Python SDK

Aoc python sdk, require Python 2.7 or 3.4+

- [source code](https://github.com/arcfraofficial/aoc-python-sdk)
- [release](https://github.com/arcfraofficial/aoc-python-sdk/releases)

## Installation

- ### install from whl

  ```shell
  pip install aoc_sdk-1.1.0-py2.py3-none-any.whl
  ```

- ### install from tar.gz

  ```shell
  tar xvzf aoc-sdk-1.1.0.tar.gz
  cd aoc-sdk-1.1.0
  python setup.py install
  ```

- ### install from source code

  ```
  git clone https://github.com/arcfraofficial/aoc-python-sdk.git
  cd aoc-python-sdk
  python setup.py install
  ```

- ### install from source code by pip

  ```shell
  pip install git+https://github.com/arcfraofficial/aoc-python-sdk.git
  ```

- ### install from pypi
  ```shell
  pip install aoc-sdk
  ```

## Usage

### Create instance

#### Create `ApiClient` instance

```python
from aoc.configuration import Configuration
from aoc import ApiClient

configuration = Configuration(host="http://aoc.arcfra.com/v2/api")
client = ApiClient(configuration)
```

> If using self-signed certificate, update configuration to skip ssl check

```python
configuration = Configuration(host="http://aoc.arcfra.com/v2/api")
configuration.verify_ssl = False
client = ApiClient(configuration)
```

#### Create relatd Api instance

> Create related Api instance depends on usage, for example, to use vm related operation, create a `VmApi`。

```python
from aoc.api.vm_api import VmApi
vm_api = VmApi(client)
```

### Authentication

> a `login` function is provided in `utils` package and can use to authenticate an api_client

```python
from aoc.utils import wait_tasks, login
conf = Configuration(host="http://aoc.arcfra.com/v2/api")
api_client = ApiClient(conf)
login(api_client, "<username>", "<password>")
```

> or directly update api_key field of Configuration, token can be retrieved from `login` api from `UserApi`

```python
from aoc.api.user_api import UserApi
from aoc.models import UserSource

user_api = UserApi(client)
login_res = user_api.login({
    "username": "<username>",
    "password": "<password>",
    "source": UserSource.LOCAL
})

configuration.api_key["Authorization"] = login_res.data.token
```

### Sending Request

#### List elements

```python
vms = vm_api.get_vms({
  "where": {
    "id": "vm_id"
  },
  "first":1,
})
```

#### Mutation and async task

> Mostly mutation will response as async task earlier to avoid request hanging when executing long-running operation.

```python
start_res = vm_api.start_vm({
  "where": {
    "id": "stopped_vm_id"
  },
})
'''
start_res = {
    task_id: <task_id>
    data: {
        ...
    }
}
'''
```

> A utils named function `wait_tasks` is export from utils package

```python
from aoc.utils import wait_tasks
try:
 wait_tasks([res.task_id for res in start_res], api_client)
except ApiException as e:
 # handling exception

# handling response
```

##### async request
> Directly call api funtion will block process until response, to make request async, add kwargs async_req=True
> Then function will return an `ApplyResult` and can use get function to retrieve response

```python
vms = vm_api.get_vms(
  {
    "where": {
      "id": "vm_id"
    }
  },
  async_req=True
)
print(vms.get()[0].name)
```

#### Create `ActivePassiveApiClient` instance

AOC introduced multi-management-IP active-passive deployment in version 4.9.0. To access such AOC deployments, you can use `ActivePassiveApiClient` to configure multiple endpoints for the same active-passive cluster. At any given time, it is expected that at most one active endpoint exists. The order of endpoints passed in does not represent the active-passive relationship; the client will select the current active endpoint based on probing results.

```python
from aoc import ActivePassiveApiClient

client = ActivePassiveApiClient(
    endpoints=["https://aoc-a.example.com", "https://aoc-b.example.com"],
    user_config={
        "name": "<username>",
        "password": "<password>",
    },
)
```

#### Failover strategies

`ActivePassiveApiClient` supports the following failover strategies:

- `AUTO_FAILOVER`: The default strategy. When there is no cached active endpoint, it attempts to probe and cache the current active endpoint; after a request returns 307, it automatically re-probes and retries once; after a network I/O exception occurs, it clears the cache but does not automatically retry.
- `MANUAL_FAILOVER`: After a request returns 307, it does not automatically re-probe or retry. Clearing the cache is handled by the caller for failover. The rest of the business logic is consistent with `AUTO_FAILOVER`.
- `ALWAYS_PROBE`: Does not cache the active endpoint. Re-probes the active endpoint before each request; after a request returns 307, it does not automatically retry.

If you need to specify a failover strategy, you can pass it in when creating the instance:

```python
from aoc import ActivePassiveApiClient, FailoverStrategy

client = ActivePassiveApiClient(
    endpoints=["https://aoc-a.example.com", "https://aoc-b.example.com"],
    user_config={
        "name": "<username>",
        "password": "<password>",
    },
    failover_strategy=FailoverStrategy.MANUAL_FAILOVER,
)
```
