---

copyright:
  years: 2021, 2023
lastupdated: "2024-02-14"

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

# About IBM Spectrum LSF
{: #about-spectrum-lsf}

{{site.data.keyword.spectrum_full}} allows you to deploy high-performance computing (HPC) clusters by using {{site.data.keyword.spectrum_full_notm}} as HPC scheduling software. This offering uses open source Terraform-based automation to provision and configure {{site.data.keyword.cloud_notm}} resources. With simple steps to define configuration properties and use automated deployment, you can build your own HPC clusters in minutes by using your choice of an Intel x86 based [VPC virtual server instance profile type](/docs/vpc?topic=vpc-profiles&interface=ui) for the worker nodes in the cluster. {{site.data.keyword.spectrum_full_notm}} also enables configuration for auto scaling, so {{site.data.keyword.spectrum_full_notm}} clusters can automatically add and remove worker nodes based on workload specifications. This allows you to take full advantage of consumption-based pricing and pay for cloud resources only when they are needed. 
{: shortdesc}

{{site.data.keyword.spectrum_full_notm}} offers the option of a public virtual machine, or virtual machines that are deployed on dedicated hosts, for static compute nodes only. The management nodes and dynamic compute nodes use public virtual machines only. The dedicated host option allows you to have systems that are assigned just for your workloads and avoids issues like a noisy neighbor. The deployment properties allow you to either "pack" or "spread". You can pack a dedicated host to full capacity before spilling to another instance or spread the virtual server instances evenly across all dedicated hosts.

In addition, {{site.data.keyword.spectrum_full_notm}} provides two shared storage options for you to manage your application data: File storage for VPC or {{site.data.keyword.scale_short}}. The {{site.data.keyword.scale_short}} option specifically works with static compute nodes only and allows you to deploy a high-performance file system with your HPC cluster.

The offering supports the bring-your-own-license (BYOL) model for [{{site.data.keyword.spectrum_full_notm}}](https://www.ibm.com/products/hpc-workload-management){: external} to deploy an HPC cluster on {{site.data.keyword.cloud_notm}}. Make sure that you have sufficient software licenses to deploy the required capacity on the {{site.data.keyword.cloud_notm}} cluster. For evaluation purposes, {{site.data.keyword.cloud_notm}} does enable limited access. Contact your {{site.data.keyword.cloud_notm}} sales or support team for evaluation licenses.

{{site.data.keyword.spectrum_short}} enables all three interfaces: UI, API, and CLI. To use the API and CLI interfaces, the Terraform-based automation code is available in this [public GitHub repository](https://github.com/IBM-Cloud/hpc-cluster-lsf){: external}.

{{site.data.keyword.spectrum_full_notm}} also offers the [LSF Application Center](https://www.ibm.com/docs/en/slac/10.2.0){: external}, which provides a flexible, easy-to-use interface for cluster users and administrators. Available as an add-on module to {{site.data.keyword.spectrum_full_notm}}, the LSF Application Center enables users to interact with intuitive, self-documenting, standardized interfaces. You can access the LSF Application Center through the [GUI](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-accessing-lsf-gui), and you can also access the API calls with [Python](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-access-rest-api-calls-pacclient) and [`curl`](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-access-rest-api-calls-curl).

The offering enables the initial Spectrum LSF-based HPC cluster creation. Any updates that are needed post-deployment regarding LSF configuration or setup should be performed by using LSF tools and commands. If you use the {{site.data.keyword.bpshort}} interface to change configuration properties and reapply those changes, you can cause disruptions to the running {{site.data.keyword.spectrum_short}} cluster. Restoring it back to a working state might not be easy.
{: important}

## Architecture diagram
{: #architecture-diagram}

![Figure 1. Architecture diagram](images/hpccluster_lsf_schematics_architecture_09-22-21.svg "Architecture diagram"){: caption="Figure 1. Architecture diagram" caption-side="bottom"}


