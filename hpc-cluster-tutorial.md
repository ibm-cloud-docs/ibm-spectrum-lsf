---

copyright: 
  years: 2021
lastupdated: "2021-12-01"

keywords: architecture overview, cluster access, hpc cluster
content-type: tutorial
services: virtual-servers, vpc, loadbalancer-service
account-plan: paid
completion-time: 60m
subcollection: ibm-spectrum-lsf

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:download: .download}
{:important: .important}
{:note: .note}
{:new_window: target="_blank"}
{:step: data-tutorial-type='step'}

# Setting up an HPC cluster
{: #using-hpc-cluster} 
{: toc-content-type="tutorial"} 
{: toc-services="virtual-servers, vpc, loadbalancer-service"} 
{: toc-completion-time="60m"}

## Objectives
{: #hpc-objectives}

* Deploy an HPC cluster with your choice of configuration properties

## Architecture overview and NFS file system setup 
{: #hpc-cluster-architecture-overview}

The HPC cluster consists of a login node, a storage node where the block storage volume is attached, 1 - 3 LSF management nodes, and a number of LSF worker nodes.

* The login node is served as a jump host and it is the only node that has the public IP address. Other nodes have only private IP addresses and the only way to reach these nodes is through the login node. You can log in to the primary LSF management node (or LSF master) and do most of the operations from the LSF master. By default, `lsfadmin` is the only user ID created on the cluster. The SSH passwordless setup is configured between the LSF master and workers. You can reach any other worker node with the `lsfadmin` user ID from the LSF primary.

* The worker node can be a static resource. In this case, its lifecycle is managed by Schematics. You can request a number of static worker nodes, and these workers remain available in the LSF cluster until a Schematics-destroy action is performed. The LSF resource connector function creates extra workers when there is not enough capacity to run jobs and destroys workers when the demands decrease. The lifecycle of these dynamic workers is managed by the LSF resource connector. Wait until these dynamic resources are returned to the cloud before you destroy the entire VPC cluster through Schematics.

* The storage node is configured as an NFS server and the block storage volume is mounted to `/data`, which is exported to share with LSF cluster nodes. At the NSF client end, the LSF cluster nodes in this case, the remote directory, `/data`, is mounted to `/mnt/data` locally. A soft link, `/home/lsfadmin/shared`, also points to `/mnt/data`. You can use `/home/lsfadmin/shared` as a shared file system for your applications.

The HPC cluster solution provides a base custom image, which includes the LSF installation. You can create your own custom image on top of the base image. For more information, see [Create custom image](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-getting-started-tutorial&interface=ui#create-custom-image). The image service on VPC provides a way for doing this. You can then specify the custom image that you want to use in Schematics for LSF management nodes and worker nodes. The image that is used by the login node and the storage node is not configurable at the moment (CentOS 7 by default).

## Create SSH key
{: #hpc-ssh-key-creation-before}
{: step}

Complete the following steps to create your SSH key:

1. Generate an SSH key on your system by running the following command: 

    ```
    ssh-keygen -t rsa
    ```
    {: pre}

2. Copy and save all of the content from `.ssh/id_rsa.pub`.

## Add SSH key to the VPC infrastructure
{: #hpc-ssh-key-adding}
{: step}

1. Log in to the [{{site.data.keyword.cloud}} console](https://cloud.ibm.com/){: external} by using your unique credentials.
2. From the dashboard, click **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > VPC Infrastructure > SSH keys**.
3. Click **Create**.
4. Enter the SSH key name (for example, `po-ibm-ssh-key`), select the default resource group, add tags, and select the region. 
5. Copy and paste the public key into the _Public key_ field (the contents that you saved from `.ssh/id_rsa.pub`).
6. Click **Add SSH key**.

## Create API key
{: #hpc-api-key}
{: step}

Complete the following steps to create your API key:

1. In the {{site.data.keyword.cloud_notm}} console, go to **Manage > Access (IAM) > API keys**.
2. Click **Create an IBM Cloud API key**.
3. Enter a name and description for your API key.
4. Click **Create**.
5. Then click **Show** to display the API key, **Copy** to copy and save it for later, or click **Download**.

## Create and configure an HPC cluster from the IBM Cloud catalog
{: #hpc-cluster-creation}
{: step}

Complete the following steps to create and configure an HPC cluster from the {{site.data.keyword.cloud_notm}} catalog:

1. In the {{site.data.keyword.cloud_notm}} catalog, search for _HPC_ or _Spectrum LSF_, and then select **IBM Spectrum LSF**. 

    ![HPC Cluster solution page](images/hpc_catalog.png){: caption="HPC cluster solution page"}

2. In the **Set the deployment values** section, supply the required values: `api_key`, `lsf_license_confirmation`, and `ssh_key_name`. 

3. After you confirm with the license agreement, you can use the default values for other parameters and click **Install**. The HPC cluster is created and completed within 5 minutes with the default configuration.

### Parameters for cluster deployment
{: #hpc-cluster-deployment-parameters}

See the following table for a list of parameters that you can configure for your HPC cluster:

| Parameter | Description |
| --------- | ----------- |
| `cluster_prefix` | The prefix used to name the VPC resources that are provisioned to build the HPC cluster. There are resources where their names must be unique in the same cloud account given a single region. Make sure that the name is unique (for example, add your initials to the name: po-hpc-cluster). |
| `hyperthreading_enabled` | You can enable hyper-threading in the worker nodes of the cluster by setting this value to true (default). Otherwise, hyper-threading is disabled. |
| `image_name` | Edit and add the custom image with LSF. You can use the default LSF custom image that is provided by the solution. If you created your own custom image, edit the value to the name of your custom image. |
| `management_node_count` | You can have up to three management nodes in the cluster. If you want failover support that is provided by LSF, you need to specify the value to be larger than one. In this case, when the primary management node is down, one of the candidate management nodes become the primary and your cluster remains functional without interruption. |
| `region` | Edit the region where you want your cluster to be created. To get a full list of regions, see [Creating a VPC in a different region](/docs/vpc?topic=vpc-creating-a-vpc-in-a-different-region). |
| `resource_group` | The resource group name from your {{site.data.keyword.cloud_notm}} account where the VPC resources are deployed. |
| `ssh_allowed_ips` | You can limit access to your cluster by specifying a list of IP addresses (or CIDR blocks) separated by a comma in this parameter. Only the SSH connections from these addresses can access your HPC cluster. | 
| `vpc_name` | You can use an existing VPC in which the cluster resources are provisioned. If no value given, then a new VPC is provisioned for the cluster. |
| `vpn_enabled` | You can deploy a VPN gateway for VPC in the cluster. By default, the value is set to false. |
| `vpn_peer_address` | The peer public IP address to which the VPN is connected. |
| `vpn_peer_cidrs` | Comma-separated list of peer CIDRs (for example, 192.168.0.0/24) to which the VPN is connected. |
| `vpn_preshared_key` | The pre-shared key for the VPN. |
| `zone` | Edit the zone based on the selected region. To get a full list of zones within a region, see [Get zones by using the CLI](/docs/vpc?topic=vpc-creating-a-vpc-in-a-different-region#get-zones-using-the-cli). |
{: caption="Table 1. Parameter values" caption-side="top"} 

### Parameters for auto scaling
{: #hpc-cluster-auto-scaling-parameters}

You can set the following parameters for auto scaling:

* `worker_node_min_count`: The minimum number of worker nodes that are provisioned at the time the cluster is created and remain running regardless of job demands in the cluster.

* `worker_node_max_count`: The maximum number of worker nodes in your HPC cluster, which limits the number of machines that can be added to HPC cluster. LSF auto scaling scales up the cluster to this number of nodes when needed for your workloads and scales back for keeping only`worker_node_min_count` workers when no job is in the queues.

### Parameters for instance profiles
{: #hpc-cluster-instance-profiles-deployment-parameters}

You can control the instance profile for each instance type through the `xxx_node_instance_type` parameters. The management nodes are where the main LSF daemons are running. You need to select ones with more compute power if you plan to run jobs by using 100+ nodes. The worker nodes are the ones where the workload execution takes place and the choice needs to be made according to the characteristic of workloads. The storage node is the one to manage the NFS file system for your HPC cluster. The login instance is served as a jump host, so you can pick the smallest profile. For more information, see [Instance Profiles](/docs/vpc?topic=vpc-profiles).

### Parameters for block volumes
{: #hpc-cluster-block-volume-parameters}

You can configure the storage capacity and throughput by using the `volume_xxx` parameters. The value for the `volume_profile` parameter can be either general purpose or custom. When `general-purpose` is used, IOPS is determined by the cloud infrastructure and the `volume_iops` parameter does not have any effect. If you want to customize the IOPS, you need to use custom for `volume_profiles` and set up the IOPS through `volume_iops` based on the capacity that is specified in `volume_capacity`. For more information, see [Block storage profiles](/docs/vpc?topic=vpc-block-storage-profiles&interface=ui).

## Accessing the HPC cluster
{: #hpc-cluster-access}
{: step}

To access your HPC cluster, complete the following steps:

1. Go to **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > Activity > Plan applied > View log**.

2. Copy `ssh-command` to access your cluster.

    * `ssh -J root@ip-jumphost lsfadmin@ip-masternode`

    * The `ip-jumphost` is `public`, while the `ip-master node`is not.

    * `-J flag`: Connects to the jump-host and establishes a TCP forwarding to the ultimate destination (master node).

## Auto scaling
{: #hpc-cluster-auto-scaling}
{: step}

You have a minimum number of worker nodes (`worker_node_min_count`). This is the number of worker nodes that are provisioned at the time the cluster is created. However, you can use a maximum number of worker nodes that should be added to the {{site.data.keyword.spectrum_short}} cluster defined by `worker_node_max_count`. This is to limit the number of machines that can be added to {{site.data.keyword.spectrum_short}} cluster when the auto scaling configuration is used. This property can be used to manage the cost associated with {{site.data.keyword.spectrum_short}} cluster instance.

The following example shows `worker_node_min_count=2` and `worker_node_max_count=10`.

1. To check the two worker nodes, run the following command: 

    ```
    bhosts -w
    ```
    {: pre}

    **Example output:**

    ![Two worker nodes](images/original_workernodes.png){: caption="Two worker nodes"}

2. To try the auto scaling function, run a job that requires more than two nodes. For example, this job requires five jobs to sleep for 10 seconds:

    ```
    bsub -n 5 -R "span[ptile=1]" sleep 10
    ```
    {: pre}

3. The job is submitted.

4. After a minute, check the nodes by running the following command:

    ```
    bhosts -w
    ```
    {: pre}
     
    You can see that now five nodes were added to your cluster:

    ![Two worker nodes](images/autoscaling.png){: caption="Five worker nodes added"}

5. The difference of nodes that were created by the auto scaling function are destroyed automatically after 10 minutes of not being used.

## Customize your HPC cluster for your workloads
{: #customize-hpc-cluster-workloads}
{: step}

You have two options to add software packages in the cluster for your workload. You can install the additional software in the NFS shared file system (for example, `/home/lsfadmin/shared` from the LSF master), which is visible to all compute nodes. The files that you add to the NSF shared file system are stored in the block storage that is attached to the storage node. The data is lost when the entire HPC cluster is being destroyed. Remember to save the data that you want to keep before you destroy the cluster.

The other option is to build your own custom image on top of the default image used by the HPC cluster solution. The creation of the custom image must be prepared before you create an HPC cluster. When a new custom image is created in VPC, a name is associated with this image. You need to use this name in the `image_name` parameter. For more information, see [Extend base image and create a new custom image](https://github.ibm.com/hybrid-cloud-infrastructure-research/tracker/wiki/Extend-base-image-and-create-a-new-custom-image).

## (Optional) Set up hybrid connectivity
{: #optional-hybrid-connectivity}
{: step}

If you want to set up a hybrid connectivity environment by using VPN, see the instructions [Installing a VPN to an HPC cluster](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-install-vpn-hpc-cluster).

If you would like to use Direct Link, see the instructions for [Installing Direct Link to an HPC cluster](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-installing-direct-link-cluster).