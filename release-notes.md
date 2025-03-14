---

copyright:
years: 2022, 2024

lastupdated: "2024-09-16"

keywords: IBM Spectrum LSF release notes

subcollection: ibm-spectrum-lsf

content-type: release-note

---

{{site.data.keyword.attribute-definition-list}}
{:external: target="_blank" .external}
{:release-note: data-hd-content-type='release-note'}

# Release notes
{: #release-notes}

The release notes describe the brief overview of the changes that are made to {{site.data.keyword.spectrum_full}}, feature enhancements or new features, which are known and fixed issues.
{: shortdesc}

## September 2024
{: #ibm-spectrum-lsf-sep24}

### 16 September 2024
{: #hpc-spectrum-symphony-sep2024}
{: release-note}

IBM Storage Scale version upgraded from 5.1.9.3 to 5.2.0.1
:   New IBM Storage Scale custom images are created with 5.2.0.1 version to accommodate the latest scale features.

Support for Hyperthreading on login LSF client node
:   Hyperthreading feature is added on the LSF login client node to enable the CPU performance.

Upgraded LSF software version
:   Upgraded the LSF 10.1 software version to pick all the latest ifixes and security patches.

## June 2024
{: #ibm-spectrum-lsf-june24}

### 20 June 2024
{: #hpc-spectrum-symphony-june2024}
{: release-note}

IBM Storage Scale version upgraded from 5.1.9.0 to 5.1.9.3
:   New IBM Storage Scale custom images are created with 5.1.9.3 version to accommodate the latest scale features.

IBM Storage Scale Bug Fix
:   IBM Storage Scale has issue that the required `ssh` keys are not getting copied and now the Spectrum Scale have the VPC file share mounts being mounted.

## March 2024
{: #ibm-spectrum-lsf-mar24}

### 22 March 2024
{: #hpc-spectrum-symphony-mar2224}
{: release-note}

Existing DNS service instance and Custom resolver
:   You can now opt to use the existing DNS service instance to create the new DNS zones and also the existing custom resolver that is attached to the VPC.

Dedicated LSF client login node
:   With a dedicated LSF client login node, you can directly manage and monitor the cluster to submit jobs without accessing the management nodes. If LDAP is enabled, an LDAP user can also access the login node directly to manage the cluster. For additional security, you can use a bastion node as a jump (dedicated secure) host to access the login node.

File Storage for VPC support
:   {{site.data.keyword.cloud}} File Storage for VPC is a zonal file storage offering that provides NFS-based file storage services. You can create file shares in an availability zone within a region. You can share them with multiple virtual server instances within the same zone or other zones in your region, across multiple VPCs. You can also limit access to a file share to a specific virtual server instance within a VPC and encrypt the data in transit.

Access the LSF Application center by using LDAP users
:   LDAP users can now directly access the LSF Application Center with only password authentication.

Customer-managed encryption for login or management worker nodes
:   You can now opt to use customer-managed encryption with your login and management nodes.

OpenLDAP integration for authentication
:   OpenLDAP is an open source implementation of the Lightweight Directory Access Protocol (LDAP) that provides centralized authentication and directory services.
When you deploy your {{site.data.keyword.spectrum_full_notm}} cluster, you can integrate OpenLDAP with {{site.data.keyword.spectrum_full_notm}}. This way, you can use your existing LDAP infrastructure for user authentication, enabling more secure and streamlined user access to LSF.

Accessing LSF Application Center through VNC (remote) consoles
:   You can now access a remote console in LSF Application Center by selecting **VNC Consoles** > **Open my console**.

VPC flow logs
:   Use {{site.data.keyword.cloud}} flow logs for VPC to enable the collection, storage, and presentation of information about the IP traffic that travels to and from network interfaces within your VPC. Collecting data for VPC flow logs helps diagnose security group rules and denied flows.

Existing subnet support
:   You can now use the existing subnets for your management and compute nodes from one existing VPC.

DNS functionality support
:   Introduced use of DNS custom resolver for name resolution in place of static hostname to IP address mapping.

## October 2023
{: #ibm-spectrum-lsf-oct23}

### 16 October 2023
{: #hpc-spectrum-symphony-oct1623}
{: release-note}

LSF daemon restart bug fix
:   A fix is implemented to take care of job submission for creating dynamic hosts post LSF daemon restarted.

LSF failover bug fix
:   A fix is implemented to take care of the failover case of the management node.

### 4 October 2023
{: #ibm-spectrum-lsf-oct0423}
{: release-note}

LSF Application Center MariaDB installation bugfix
:   A fix is implemented to resolve a bug that was preventing successful installation of LSF Application Center MariaDB.

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
:   An update was made to use an RHEL 8.6 stock image instead of a CentOS 7 stock image for the login node and the NFS storage node.

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
:   You have the option to use the Intel&reg; MPI Library for Intel&reg; oneAPI as an alternative to the use of OpenMPI.

## August 2022
{: #ibm-spectrum-lsf-aug22}

### 09 August 2022
{: #ibm-spectrum-lsf-aug0922}
{: release-note}

Dedicated hosts and {{site.data.keyword.scale_short}} support
:   You have the option to use {{site.data.keyword.scale_short}} for shared storage that's accessible by the Spectrum LSF compute nodes. For more information, see [Using Spectrum Scale storage](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-using-spectrum-scale-storage). With the latest changes, RHEL 8.4 custom images take the place of RHEL 7.7 and CentOS 7.7 custom images with included optimizations for NFS. And, security group rules have been added to provide more secure access to the cluster by allowing SSH from only specific nodes. In addition, you have the option to use dedicated hosts for static worker nodes. For more information, see [Using dedicated hosts](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-using-dedicated-hosts).

Bug fixes
:   Fixes were applied related to an HTTP data source body deprecation and an Ansible version 2.10 upgrade.

Deployment value removed
:   The `region` deployment value was removed. This was because the zone deployment value naming convention allows for the `region` to be easily extracted from it.

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
:   A fix is provided for the error "No image found with name" if the image name is not found in the image-mapping file.

## September 2021
{: #ibm-spectrum-lsf-sep21}

### 22 September 2021
{: #ibm-spectrum-lsf-sep2221}
{: release-note}

LSF enhancements and improvements
:   You can now use an existing VPC. On the backend, Terraform is updated to version 0.14, hyperthreading is enabled by default, and parallelism is now supported on {{site.data.keyword.bpshort}} `destroy` operations to help overall performance.

## June 2021
{: #ibm-spectrum-lsf-jun21}

### 24 June 2021
{: #ibm-spectrum-lsf-jun2421}
{: release-note}

Introducing {{site.data.keyword.spectrum_full_notm}} for HPC workloads
:   You can now use {{site.data.keyword.spectrum_full_notm}} as a workload manager with automated deployment of HPC clusters. The clusters are configured for auto scaling and use the cluster compute node configuration that you select.
