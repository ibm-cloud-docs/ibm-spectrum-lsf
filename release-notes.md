---

copyright:
  years: 2022

lastupdated: "2022-05-06"

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
