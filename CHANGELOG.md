## release date 2026-11-06

v1.1.0 release (aoc version 4.10.0)

### features

- Adds [BackupPlanApi]: create, update, delete, execute, suspend and resume backup plans, and query backup plans / plan executions (including the without-executions variants)
- Adds [BackupRestorePointApi], [BackupRestoreExecutionApi], [BackupTargetExecutionApi], [BackupServiceApi] and [BackupStoreRepositoryApi] for querying backup resources, and [backup_restore_vm_in_place] / [backup_rebuild_vm] for restoring VMs from restore points
- Adds [ReplicationPlanApi], [ReplicaVmApi], [ReplicationServiceApi] and [SyncReplicationPlanApi] for querying replication plans, replica VMs, replication services and sync replication plans
- [EverouteLicenseApi]: adds [get_v2_everoute_licenses_connection]
- [VmApi]: disk parameters of VM creation, cloning, rebuilding, importing, disk addition/update and CD-ROM addition APIs now support the `storage_config` field to specify the storage cluster (`storage_cluster_id`) or VCFS datastore (`datastore_id`) where the disk resides; `mount_new_create_disks` adds the `provision_policy` field to specify the provisioning policy of datastore volumes (`thin` / `thick_lazy_zeroed` / `thick_eager_zeroed`)
- [VmApi]: [migrate_vm_across_cluster] supports configuring `storage_config` and `provision_policy` for disks, and adds the `migrate_mode` option
- [VmVolumeApi]: [create_vm_volume], [import_vm_volume] and [clone_vm_volume] add `storage_config` and `provision_policy` fields, supporting creating, importing and cloning VM volumes on datastores with specified provisioning policies
- [VmVolume]: adds fields `provision_policy` and `elf_storage_policy_stripe_num`; [VmVolumeType] adds `KVM_VOLUME_VCFS`
- [VmVolumeSnapshot]: adds fields `provision_policy` and `elf_storage_policy_stripe_num`; [VmVolumeSnapshotType] adds `KVM_VOLUME_VCFS_SNAPSHOT`
- [Bus]: adds enum value `SATA`; [EncryptMethod]: adds enum value `SM4_CTR`
- [ContentLibraryImageApi]: adds [import_content_library_image_v2] API, supporting importing images to multiple clusters or datastores; the original [import_content_library_image] is deprecated
- [ContentLibraryImageApi]: [upload_content_library_image] adds `datastores` form field, supporting distribution to datastores during upload
- [ContentLibraryImageApi]: [distribute_content_library_image_clusters] and [remove_content_library_image_clusters] add `datastores` field, supporting distributing to and removing images by datastore
- [ContentLibraryImage]: adds field `datastores`
- [ContentLibraryImageApi]: [import_content_library_image] and image update parameters now accept `datastore`/`datastores` as an alternative to `cluster`/`clusters` (no longer required)
- [ContentLibraryVmTemplateApi]: in [clone_content_library_vm_template_from_vm], `clusters` is no longer required when `storage_configs` is provided
- [ContentLibraryVmTemplateApi]: [clone_content_library_vm_template_from_vm] adds `storage_configs` field to specify the datastores for template configuration files and volume files (VCFS template placement)
- [ContentLibraryVmTemplateApi]: [import_content_library_vm_template] adds `storage_config` and `template_architecture` fields
- [ContentLibraryVmTemplate]: adds fields `datastores`, `usage` and `vm_template_configs`
- [Host]: adds memory overcommit related fields `memory_overcommit_enabled`, `allocated_memory_bytes`, `memory_allocation_rate`, `overcommit_memory_ratio`, `reserved_memory_bytes`, `running_pause_reserved_memory_bytes`, `overcommit_memory_bytes`, `running_pause_overcommit_memory_bytes`, `overcommit_active_memory_bytes`, `total_physical_memory_bytes`
- [Host]: adds fields `dirty_cache_space`, `dirty_cache_usage`, `failure_cache_space`, `total_logical_cpu_cores`
- [Cluster]: adds memory overcommit related fields `allocated_memory_bytes`, `memory_allocation_rate`, `overcommit_memory_ratio`
- [Cluster]: adds fields `dirty_cache_space`, `dirty_cache_usage`, `failure_cache_space`, `overprovision_ratio`, `cap_snapshot_unique_size`, `cap_volume_unique_size`
- [VmNic]: adds fields `guest_info_ip_addresses`, `guest_info_ip_addresses_v6` and `dpi_enabled`
- [Vm]: adds fields `dpi_enabled`, `anti_malware_enabled`, `internal_product`, `internal_product_name` and `backup_plans`; [VmUsage] adds `CLOUDTOWER_PASSIVE`, `CLOUDTOWER_WITNESS`, `EVEROUTE_LB`, `EVEROUTE_VPC_EDGE`
- [VmFolderApi]: [create_vm_folder] and [update_vm_folder] support the `parent_id` field for creating and adjusting nested VM folders; [VmFolder] adds fields `parent`, `path` and `total_vm_num`
- [VmPlacementGroup]: adds field `usage`
- [IscsiLun]: adds field `replication_plans`
- [NetworkType]: adds enum values `CUSTOM`, `ERSPAN`, `INTERNAL`
- [UserApi]: [login] supports logging in via LDAP login sources (`auth_config_id`); [MfaType] adds `Totp` and `TotpRecoveryCode`
- [LabelApi]: adds [get_labels] API to query the label list
- [LabelApi]: [get_labels], [update_label], [add_labels_to_resources] and [remove_labels_from_resources] support the `x-skip-resources` request header to skip loading associated resources for better query performance
- [UserRolePreset]: adds enum value `WORKLOAD_CLUSTER_REGISTRY_USER`
- [VirtualPrivateCloudEdgeGateway]: adds fields `edge_gateway_group` and `vpc_service`; [VirtualPrivateCloudEdgeGatewayGroup]: adds field `vpc_service`
- [EverouteLicensePricingType]: adds enum values `CORE_NUM`, `VCPU_NUM`; [ConnectState]: adds enum value `CONNECTED_ERROR`
- [NestedEverouteControllerInstance]: fields `ip_addr` and `vlan` become optional; [EverouteCluster]: field `controller_template` becomes optional

### bugfix

- [LabelApi]: fix label queries carrying redundant parameters
