---

copyright:
  years: 2021, 2022
lastupdated: "2022-03-08"

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

# Managing data with Spectrum LSF on IBM Cloud
{: #managing-data-spectrum-lsf}

When you work with HPC workloads in a cloud environment, a critical challenge to address is how best to manage the data that is needed for running the workloads, as well as the output that might need to be analyzed for further processing and decision making. With {{site.data.keyword.spectrum_full}} clusters deployed on {{site.data.keyword.cloud}}, you can use the following methods to manage your data.
{: shortdesc}

## Hybrid setup with IBM Cloud
{: #hybrid-setup}

If your setup is using a VPN or direct link to connect the on-premises {{site.data.keyword.spectrum_short}} environment to the {{site.data.keyword.spectrum_short}} cluster on {{site.data.keyword.cloud_notm}}, you can configure the LSF multicluster capability and use the {{site.data.keyword.spectrum_short}} Data Manager component to stage the data from on-premises to your {{site.data.keyword.spectrum_short}} cluster on {{site.data.keyword.cloud_notm}}. 

With an on-premises cluster, data typically resides on one or more file systems that are mounted onto every compute node. In general, you don't need to consider data location or locality (certain workloads do benefit from being "closer" to the filer and {{site.data.keyword.spectrum_full_notm}} does have few capabilities that allow you to consider data locality).   

In a hybrid setup, where work can run on-premises and in the cloud, how and when data is moved to and from the cloud is important. Unfortunately, there is not a one-size-fits-all solution for this, and your data movement strategy depends on multiple factors:

* Frequency and volume of workload being sent to the cloud - are you sending a handful of jobs or thousands?
* Frequency of change of that data - is the data set static or constantly changing? 
* Size of the data - how much needs to be moved?
* Uniqueness of data per job - does every job require unique data inputs or are most of the jobs reusing or sharing common data?
* Runtime of the workload - if the ratio of compute time to time taken to get the data to the cloud is low, it is probably not cost-effective to run that workload on the cloud

For data requirements that are static or that have small incremental changes, mirroring the data set between on-premises and the cloud can be cost-effective by using solutions such as IBM Aspera. Data can be moved in bulk when the cluster is created and resynced on a schedule or a per-job-basis to ensure that the latest changes are available.  

For jobs that have unique requirements, the data requirements can be specified as part of the job submission (`bsub -f`) and the files are transferred to the compute node when the job runs and the results are transferred back upon completion. In this case, the data is purely transient and is erased when the compute node is de-provisioned. 

As data transfer times increase, the example solution becomes less efficient as the compute node is provisioned before the data is transferred and the results are transferred back before the node is de-provisioned.

LSF's Data Manager component addresses this by scheduling data movement independently of the job. Input files are transferred to cloud storage before any nodes are provisioned, and results are transferred back after the node is de-provisioned. Data Manager also de-duplicates transfers avoiding the same files that are being transferred time and time again. This is important when running conducting design of experiment type analyses or regression and verification runs where most of the data is common between thousands or hundreds of thousands of jobs.

To further reduce data movement, pre- and post-processing can also be conducted in the cloud. LSF's Application Center provides a web portal and restful API, which allows jobs to be submitted directly to the cloud cluster and starts remote visualization tools on the job or data in the cloud. The Application Center built in support for visualization of common output formats. It also has a client component that can be used to upload or download data directly from your laptop to the cloud cluster.

The NFS instance that is deployed with the {{site.data.keyword.spectrum_full_notm}} cluster in the cloud can be used as a destination with Data Manager configuration. When the data is available on NFS, it is visible to the management and worker nodes of your {{site.data.keyword.spectrum_short}} cluster.

## Stand-alone cluster on IBM Cloud
{: #stand-alone-cluster}

With a dedicated or stand-alone cluster on the cloud, your data is resident in the cloud. You can use {{site.data.keyword.cos_full_notm}} to bring the data into your {{site.data.keyword.cloud_notm}} account. {{site.data.keyword.cos_short}} provides a cheap and reliable option to manage your data in {{site.data.keyword.cloud_notm}}. When the data is available in {{site.data.keyword.cos_short}}, you can mount the {{site.data.keyword.cos_short}} bucket onto the management node or copy the data from the {{site.data.keyword.cos_short}} bucket to the NFS instance to make the data visible to the management and worker nodes of your {{site.data.keyword.spectrum_short}} cluster. 

## Additional resources
{: #additional-resources}

* For more information on how to configure and handle issues with data management, see [Four Hybrid Cloud Data Management Challenges](https://www.datacenterknowledge.com/cloud/four-hybrid-cloud-data-management-challenges){: external}.
* For more information on setting up a {{site.data.keyword.spectrum_short}} multicluster configuration, see [{{site.data.keyword.spectrum_full_notm}} multicluster capability](https://www.ibm.com/docs/en/spectrum-lsf/10.1.0?topic=lsf-multicluster-capability){: external}.
* For more information on setting up a {{site.data.keyword.spectrum_short}} data manager configuration, see [{{site.data.keyword.spectrum_full_notm}} Data Manager](https://www.ibm.com/docs/en/spectrum-lsf/10.1.0?topic=lsf-data-manager){: external}.
* For more information on how to use the IBM Aspera plug-in with {{site.data.keyword.spectrum_short}}, see [Configuring IBM Aspera as a data transfer tool](https://www.ibm.com/docs/en/spectrum-lsf/10.1.0?topic=transfer-configuring-aspera-as-data-tool){: external}.
* For more information about using {{site.data.keyword.cos_full_notm}}, see [Getting started with {{site.data.keyword.cos_full_notm}}](/docs/cloud-object-storage?topic=cloud-object-storage-getting-started-cloud-object-storage).
* For more information on how to use `s3fs` interface with {{site.data.keyword.cos_full_notm}}, see [Mounting a bucket using `s3fs`](/docs/cloud-object-storage?topic=cloud-object-storage-s3fs). 
