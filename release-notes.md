---

copyright:
  years: 2022

lastupdated: "2022-08-09"

keywords: IBM Spectrum LSF release notes

subcollection: ibm-spectrum-lsf

content-type: release-note

---

{{site.data.keyword.attribute-definition-list}}
{:external: target="_blank" .external}
{:release-note: data-hd-content-type='release-note'}

# Release notes for IBM Spectrum LSF
{: #release-notes}

Use these release notes to learn about the latest updates to {{site.data.keyword.spectrum_full}} that are grouped by date.
{: shortdesc}

## August 2022
{: #ibm-spectrum-lsf-aug22}

### 09 August 2022
{: #ibm-spectrum-lsf-aug0922}
{: release-note}

Dedicated hosts and {{site.data.keyword.scale_short}} support
:   You now have the option to use {{site.data.keyword.scale_short}} for shared storage that's accessible by the Spectrum LSF compute nodes. For more information, see [Using Spectrum Scale storage](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-using-spectrum-scale-storage). With the latest changes, RHEL 8.4 custom images take the place of RHEL 7.7 and CentOS 7.7 custom images with included optimizations for NFS. And, security group rules have been added to provide more secure access to the cluster by allowing SSH from only specific nodes. In addition, you now have the option to use dedicated hosts for static worker nodes. For more information, see [Using dedicated hosts](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-using-dedicated-hosts).

Bug fixes
:   Fixes were applied related to an HTTP data source body deprecation and an Ansible version 2.10 upgrade.

Deployment value removed
:   The `region` deployment value was removed. This was due to the fact that the zone deployment value naming convention allows for the `region` to be easily extracted from it.

## March 2022
{: #ibm-spectrum-lsf-mar22}

### 30 March 2022
{: #ibm-spectrum-lsf-mar3022}
{: release-note}

Bug fix
:   The custom image lookup error has been fixed.

## February 2022
{: #ibm-spectrum-lsf-feb22}

### 02 February 2022
{: #ibm-spectrum-lsf-feb0222}
{: release-note}

Security update
:   Changes were made to the post-provisioning scripts to mitigate the Polkit Local Privilege Escalation Vulnerability (CVE-2021-4034).

## October 2021
{: #ibm-spectrum-lsf-oct21}

### 18 October 2021
{: #ibm-spectrum-lsf-oct1821}
{: release-note}

Bug fix
:   A fix has been provided for the error "No image found with name" if the image name is not found in the image mapping file.

## September 2021
{: #ibm-spectrum-lsf-sep21}

### 22 September 2021
{: #ibm-spectrum-lsf-sep2221}
{: release-note}

LSF enhancements and improvements
:   You can now use an existing VPC. On the back end, Terraform has been updated to version 0.14, hyperthreading is enabled by default, and parallelism is now supported on {{site.data.keyword.bpshort}} `destroy` operations to help overall performance.

## June 2021
{: #ibm-spectrum-lsf-jun21}

### 24 June 2021
{: #ibm-spectrum-lsf-jun2421}
{: release-note}

Introducing {{site.data.keyword.spectrum_full_notm}} for HPC workloads
:   You can now use {{site.data.keyword.spectrum_full_notm}} as a workload manager with automated deployment of HPC clusters. The clusters are configured for auto scaling and use the cluster compute node configuration that you select.
