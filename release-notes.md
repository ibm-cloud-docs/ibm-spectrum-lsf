---

copyright:
  years: 2022, 2023

lastupdated: "2023-05-31"

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

## October 2023
{: #ibm-spectrum-lsf-oct23}

### 16 October 2023
{: #hpc-spectrum-symphony-oct1623}

LSF daemon restart bug fix
:   A fix has been implemented to take care of job submission for creating dynamic hosts post LSF daemon restarted.

LSF failover bug fix
:   A fix has been implemented to take care of the failover case of the management node.


### 4 October 2023
{: #ibm-spectrum-lsf-oct0423}
{: release-note}

LSF Application Center MariaDB installation bugfix
:   A fix has been implemented to resolve a bug that was preventing successful installation of LSF Application Center MariaDB.

## May 2023
{: #ibm-spectrum-lsf-may23}

### 31 May 2023
{: #ibm-spectrum-lsf-may3123}
{: release-note}

Documentation enhancement: Integrating OpenLDAP with {{site.data.keyword.spectrum_full_notm}}
:   The newly added [Integrating OpenLDAP with {{site.data.keyword.spectrum_full_notm}}](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-integrate-openldap-spectrum-lsf) tutorial walks you through how to use your existing LDAP infrastructure to provide centralized authentication and directory services.

## March 2023
{: #ibm-spectrum-lsf-mar23}

### 24 March 2023
{: #ibm-spectrum-lsf-mar2423}
{: release-note}

LSF Application Center support
:   An option has been added to install the LSF Application Center module, which provides cluster users and administrators with an easy-to-use interface for job submission and monitoring.

Added LSF license check
:   The solution now automatically checks for a valid LSF license that is associated with `ibm_customer_number`, which has been added as a required variable.

Updated login node and NFS storage node stock images
:   An update was made to use a RHEL 8.6 stock image instead of a CentOS 7 stock image for the login node and the NFS storage node.

Custom image support
:   Documentation and scripts to make it easier for users to create and use their own custom images for LSF worker and {{site.data.keyword.scale_short}} storage nodes are now included in the solution.

## January 2023
{: #ibm-spectrum-lsf-jan23}

### 20 January 2023
{: #ibm-spectrum-lsf-jan2023}
{: release-note}

Updated {{site.data.keyword.spectrum_short}} and {{site.data.keyword.scale_short}} default images
:   New default images were created for {{site.data.keyword.spectrum_short}} and {{site.data.keyword.scale_short}} along with their corresponding deployment value names. For {{site.data.keyword.spectrum_short}}, the `image_name` deployment value was updated to `hpcc-lsf10-scale5131-rhel84-2-0-5`, which replaced the previous default image `hpcc-lsf10-scale5131-rhel84-092122-v2`. 

:   For {{site.data.keyword.scale_short}}, the `scale_storage_image_name` deployment value was updated to `hpcc-scale5131-rhel84`, which replaced the previous default image `hpcc-scale5131-rhel84-jun0122-v1`. For more information, see [Deployment values](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-deployment-values).

## September 2022
{: #ibm-spectrum-lsf-sept22}

### 29 September 2022
{: #ibm-spectrum-lsf-sept2922}
{: release-note}

Intel&reg; MPI Library for Intel&reg; oneAPI support
:   You now have the option to use the Intel&reg; MPI Library for Intel&reg; oneAPI as an alternative to the use of OpenMPI.

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
