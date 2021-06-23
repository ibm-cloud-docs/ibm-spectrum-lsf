---

copyright:
  years: 2021
lastupdated: "2021-06-22"

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

# About IBM Spectrum LSF
{: #about-spectrum-lsf}

{{site.data.keyword.spectrum_full}} allows you to deploy high-performance computing (HPC) clusters by using {{site.data.keyword.spectrum_full_notm}} as HPC scheduling software. This offering uses open source Terraform-based automation to provision and configure {{site.data.keyword.cloud_notm}} resources. With simple steps to define configuration properties and use automated deployment, you can build your own HPC clusters in minutes. {{site.data.keyword.spectrum_full_notm}} also enables configuration for auto-scaling, so {{site.data.keyword.spectrum_full_notm}} clusters can automatically add and remove worker nodes based on workload specifications. This allows you to take full advantage of consumption-based pricing and pay for cloud resources only when they are needed. 
{: shortdesc}

The offering enables the initial Spectrum LSF-based HPC cluster creation. Any updates that are needed post-deployment regarding LSF configuration or setup should be performed by using LSF tools and commands. If you use the {{site.data.keyword.bpshort}} interface to make changes to configuration properties and reapply those changes, you can cause disruptions to the running {{site.data.keyword.spectrum_short}} cluster. Restoring it back to a working state might not be easy.
{: important}

## Architecture diagram
{: #architecture-diagram}

![Architecture diagram](images/hpcccluster_schematics_arch.svg){:caption="Figure 1. Architecture diagram of an {{site.data.keyword.spectrum_full_notm}} cluster on {{site.data.keyword.cloud_notm}}" caption-side="bottom"}


