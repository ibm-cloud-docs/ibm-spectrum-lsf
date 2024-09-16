---

copyright:
  years: 2022
lastupdated: "2022-09-20"

keywords: 

subcollection: ibm-spectrum-lsf

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:external: target="_blank" .external}
{:pre: .pre}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:step: data-tutorial-type='step'}
{:table: .aria-labeledby="caption"}

# Using IBM Storage Scale
{: #using-spectrum-scale-storage}

{{site.data.keyword.scale_full}} is a cluster file system that provides simultaneous access to a single file system from multiple compute nodes. In the {{site.data.keyword.spectrum_full_notm}} offering, {{site.data.keyword.scale_short}} applications running in static compute cluster nodes with high-performance access to a shared data space. For more information, see the [{{site.data.keyword.scale_full_notm}}](https://www.ibm.com/docs/en/spectrum-scale/5.1.2){: external} product documentation.
{: shortdesc}

The {{site.data.keyword.spectrum_full_notm}} offering uses {{site.data.keyword.vpc_short}} virtual server instance profiles that are provisioned with [instance storage](/docs/vpc?topic=vpc-instance-storage) for the {{site.data.keyword.scale_short}} storage nodes. By virtue of being on instance storage, this storage option can be used for scratch data use cases. When a storage node virtual server instance is rebooted, the data is preserved. However, when the instance is deleted, the instance storage data is lost. There is no automatic replication to persistent storage, for example, {{site.data.keyword.cos_full_notm}}, that is provided in the current implementation, but you can add it on your own if required.

![Architecture diagram](images/hpccluster_lsf_scale_architecture.svg){:caption="Figure 1. Architecture diagram of an {{site.data.keyword.spectrum_full_notm}} cluster using {{site.data.keyword.scale_short}} storage on {{site.data.keyword.cloud_notm}}" caption-side="bottom"}

## Before you begin
{: #before-you-begin}

Before you begin, make sure to complete the steps for [Getting started with {{site.data.keyword.spectrum_full_notm}}](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-getting-started-tutorial).

## Configuring Spectrum Scale storage
{: #configure-scale-storage}

To configure and use {{site.data.keyword.scale_short}} storage, the `spectrum_scale_enabled` [deployment value](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-deployment-values) must be set to "true". The remaining {{site.data.keyword.scale_short}} storage parameters ("scale_xx") must be set to deploy the wanted storage cluster configuration.

## Checking the Spectrum Scale file system
{: #check-scale-file-system}

Complete the following steps to check the {{site.data.keyword.scale_short}} file system:

1. Log in to a Scale storage node by using SSH. Details are available in the logs output with the following `spectrum_scale_storage_ssh_command`:

    ```
    ssh -J root@52.116.122.64 root@10.240.128.37
    ```
    {: codeblock}

2. Display the `gpfs` cluster setup on the Scale storage node:

    ```
    /usr/lpp/mmfs/bin/mmlscluster
    ```
    {: codeblock}

3. Display the file system size:

    ```
    /usr/lpp/mmfs/bin/mmlsfs all -i
    ```
    {: codeblock}

4. Display the file server details. This command can be used to validate file block size (node size in bytes):

    ```
    /usr/lpp/mmfs/bin/mmlsfs fs1
    ```
    {: codeblock}

5. Log in to the LSF management host by using SSH:

    ```
    ssh -J root@52.116.122.64 root@10.240.128.41
    ```
    {: codeblock}

6. Display the `gpfs` cluster setup on the compute nodes, which include the management host, management host candidate, and worker nodes:

    ```
    /usr/lpp/mmfs/bin/mmlscluster
    ```
    {: codeblock}

7. Create a file on the mount point path, for example, /gpfs/fs1, and verify on the other compute nodes that the file can be accessed.
