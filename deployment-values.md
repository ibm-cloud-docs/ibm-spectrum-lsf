---

copyright:
  years: 2021
lastupdated: "2021-09-24"

keywords: 

subcollection: ibm-spectrum-lsf

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:external: target="_blank" .external}
{:pre: .pre}
{:tip: .tip}
{:note .note}
{:important: .important}
{:table: .aria-labeledby="caption"}

# Deployment values
{: #deployment-values}

The following deployment values can be used to configure the {{site.data.keyword.spectrum_short}} cluster instance on {{site.data.keyword.cloud}}:

NOT_SET refers to empty value in catalog tile. If you are using other modes like CLI, API or schematic UI directly to provision the hpc cluster, leave the values empty.
{:note .note}

| Value | Description | Is it required? | Default value |
| ----- | ----------- | --------------- | ------------ |
| `api_key` | This is the {{site.data.keyword.cloud_notm}} API key for the {{site.data.keyword.cloud_notm}} account where the {{site.data.keyword.spectrum_short}} cluster needs to be deployed. For more information on how to create an API key, see [Managing user API keys](/docs/account?topic=account-userapikey). | Yes | None |
| `ssh_key_name` | Comma-separated list of names of the SSH key that is configured in your IBM Cloud account and is used to establish a connection to the LSF master node. Ensure that the SSH key is present in the same resource group and region where the cluster is being provisioned. If you do not have an SSH key in your IBM Cloud account, create one by using the instructions given at [SSH keys](/docs/vpc?topic=vpc-ssh-keys). | Yes | None |
| `lsf_license_confirmation` | If you have confirmed the availability of the {{site.data.keyword.spectrum_short}} license for a production cluster on {{site.data.keyword.cloud_notm}} or if you are deploying a non-production cluster, enter `true`. **Note:** Failure to comply with licenses for production use of software is a violation of the [IBM International Program License Agreement](https://www.ibm.com/software/passportadvantage/programlicense.html){: external} | Yes | None |
| `cluster_id` | Unique ID of the cluster used by Symphony for configuration of resources. This must be up to 39 alphanumeric characters including the underscore (_), the hyphen (-), and the period (.). Other special characters and spaces are not allowed. Do not use the name of any host or user as the name of your cluster. You cannot change it after installation. | false | `HPCCluster` |
| `cluster_prefix` | Prefix that is used to name the hpcc-lsf cluster and {{site.data.keyword.cloud_notm}} resources that are provisioned to build the hpcc-lsf cluster instance. You cannot create more than one instance of the lsf cluster with the same name. Make sure that the name is unique. | No | `hpcc-lsf` |
| `hyperthreading_enabled` | Setting this to true will enable hyper-threading in the worker nodes of the cluster (default). Otherwise, hyper-threading will be disabled. | false | true |
| `image_name` | Name of the custom image that you want to use to create virtual server instances in your {{site.data.keyword.cloud_notm}} account to deploy the hpcc-lsfXXX cluster. By default, the automation uses a base image with following HPC-related packages. If you would like to include your application-specific binary files, follow the instructions in [Planning for custom images](/docs/vpc?topic=vpc-planning-custom-images) to create your own custom image and use that to build the hpcc-lsfXXX cluster through this offering. | No | `ibm_cloud_spectrum_lsf_XXX` |
| `login_node_instance_type` | Specifies the virtual server instance profile type to be used to create the login node for the {{site.data.keyword.spectrum_short}} cluster. For choices on profile types, see [Instance profiles](/docs/vpc?topic=vpc-profiles). | No | bx2-2x8 |
| `management_node_count` | Total number of master and master candidate nodes. Enter a value in the range 1 - 3.| No | 2 |
| `management_node_instance_type` | Specify the virtual server instance profile type to be used to create the management nodes for the {{site.data.keyword.spectrum_short}} cluster. For choices on profile types, see [Instance profiles](/docs/vpc?topic=vpc-profiles). | No | bx2-4x16 |
| `region` | {{site.data.keyword.cloud_notm}} region name where the {{site.data.keyword.spectrum_short}} cluster should be deployed. To get a full list of regions, see [Creating a VPC in a different region](/docs/vpc?topic=vpc-creating-a-vpc-in-a-different-region). | No | `us-south` |
| `resource_group` | Resource group name from your {{site.data.keyword.cloud_notm}} account where the VPC resources should be deployed. **Note:** Do not modify the "Default" value if you would like to use the auto-scaling capability. For additional information on resource groups, see [Managing resource groups](/docs/account?topic=account-rgs). | No | Default |
| `ssh_allowed_ips` | Comma-separated list of IP addresses that can access the {{site.data.keyword.spectrum_short}} instance through an SSH interface. The default value allows any IP address to access the cluster. | No | 0.0.0.0/0 |
| `storage_node_instance_type` | Specify the virtual server instance profile type to be used to create the storage nodes for the {{site.data.keyword.spectrum_short}} cluster. The storage nodes are the ones that would be used to create an NFS instance to manage the data for HPC workloads. For choices on profile types, see [Instance profiles](/docs/vpc?topic=vpc-profiles). | No | bx2-2x8 |
| `volume_capacity` | Size in GB for the block storage that would be used to build the NFS instance and would be available as a mount on the {{site.data.keyword.spectrum_short}} master node. Enter a value in the range 10 - 16,000. | No | 100 GB |
| `volume_iops` | Number to represent the IOPS configuration for block storage to be used for the NFS instance (valid only for `volume_profile=custom`, dependent on `volume_capacity`). Enter a value in the range 100 - 48,000. For possible options of IOPS, see [Custom IOPS profile](/docs/vpc?topic=vpc-block-storage-profiles#custom). | No | 300 |
| `volume_profile` | Name of the block storage volume type to be used for NFS instance. For possible options, see [Block storage profiles](/docs/vpc?topic=vpc-block-storage-profiles). | No | `general_purpose` |
| `vpc_name` | Name of an existing VPC in which the cluster resources will be deployed. If no value is given, then a new VPC will be provisioned for the cluster.  [Learn more](/docs/vpc?topic=vpc-creating-a-vpc-using-cli). | false | _NOT_SET_ |
| `vpn_enabled` | Set the value as true to deploy a VPN gateway for VPC in the cluster.| false | false |
| `vpn_peer_address` | The peer public IP address to which the VPN will be connected. | false | _NOT_SET_ |
| `vpn_peer_cidrs` | Comma separated list of peer CIDRs (e.g., 192.168.0.0/24) to which the VPN will be connected. | false | _NOT_SET_ |
| `vpn_preshared_key` | The pre-shared key for the VPN | false | _NOT_SET_ |
| `worker_node_instance_type` | Specify the virtual server instance profile type to be used to create the worker nodes for the {{site.data.keyword.spectrum_short}} cluster. The worker nodes are the ones where the workload execution takes place and the choice should be made according to the characteristics of the workloads. For choices on profile types, see [Instance profiles](/docs/vpc?topic=vpc-profiles). | No | bx2-4x16 |
| `worker_node_max_count` | The maximum number of worker nodes that should be added to the {{site.data.keyword.spectrum_short}} cluster. This limits the number of machines that can be added to the {{site.data.keyword.spectrum_short}} cluster when auto scaling configuration is used. This property can be used to manage the cost associated with the {{site.data.keyword.spectrum_short}} cluster instance. Enter a value in the range 1 - 500. | No | 10 |
| `worker_node_min_count` | The minimum number of worker nodes. This is the number of worker nodes that are provisioned at the time the cluster is created. Enter a value in the range 0 - 500. | No | 0 | 
| `zone` | {{site.data.keyword.cloud_notm}} zone name within the selected region where the {{site.data.keyword.spectrum_short}} cluster should be deployed. To get a full list of zones within a region, see [Get zones by using the CLI](/docs/vpc?topic=vpc-creating-a-vpc-in-a-different-region#get-zones-using-the-cli). | No | `us-south-3` |
{: caption="Table 1. Deployment values" caption-side="top"} 

