---

copyright:
  years: 2022
lastupdated: "2022-04-20"

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

# Release notes for HPCC topics
{: #release-notes-hpcc}

Use the release notes to learn the latest updates to HPCC topics in IBM Cloud®.
For general information about HPCC topics, see …

## HPCC LSF
{: #hpcc-lsf}


### June 2021 - June 24, 2021
{: #june-june}

•	Automated deployment for HPC clusters with IBM Spectrum LSF as workload manager. The clusters are configured for auto-scaling and use the cluster compute nodes configuration that is selected by the user.

### February 2022 - February 02, 2022
{: #february}


•	Support to use an existing VPC
•	Changes related to VPN support
•	Updated terraform version to 0.14
•	Enable hyperthreading by default
•	Add parallelism to schematics destroy
•	Fix for error "No image found with name" if the image name is not found in the image mapping file
•	Changes to post provisioning scripts to mitigate Polkit Local Privilege Escalation Vulnerability (CVE-2021-4034)

### March 2022 - March 30, 2022
{: #march}

•	Fix for custom image lookup error

## HPCC Symphony
{: #hpcc-symp}

### October 2021 - October 18, 2021
{: #october}

•	Automated deployment for HPC clusters with IBM Spectrum Symphony as workload manager. The clusters are configured for auto-scaling and use the cluster compute nodes configuration that is selected by the user.

### December 2021 - December 14, 2021
{: #december}

•	Support dedicated hosts for static worker nodes
•	Enhance ssh_command output
•	Update stock images used for login and storage nodes to RHEL 8.2
•	Enable hyperthreading by default
•	Clean up symphony entitlements
•	Clean up location(region) input properties
•	Add parallelism to schematics destroy
•	Remove JNDILookup and JMSAppender from classpath to mitigate Log4Shell vulnerability (CVE-2021-44228)
•	Avoid terraform warning regarding interpolation

### December 23, 2021
{: #later-dec}

•	Remove JNDILookup and JMSAppender from classpath to mitigate Log4Shell vulnerability (CVE-2021-44228)

### January 2022 - January 13, 2022
{: #january}

•	New custom image having upgraded log4j version(2.17) to mitigate Log4Shell vulnerability (CVE-2021-44228)

### January 28, 2022
{: #later-jan}

•	Support for Spectrum Scale storage nodes
•	Optimization of NFS for RHEL 8
•	Removed RHEL 7.7 and Centos 7.7 custom images and replaced them with RHEL 8.2 custom image
•	Changes to post provisioning scripts to mitigate Polkit Local Privilege Escalation Vulnerability (CVE-2021-4034)

## HPCC Slurm 

### December 2021 - December 15, 2021
{: #dec-21}

•	Automated deployment for HPC clusters with Slurm as workload manager. The clusters have only static compute nodes and use the cluster compute nodes configuration that is selected by the user.

### February 2022 - February 02, 2022
{: #feb-22}

•	Changes to post provisioning scripts to mitigate Polkit Local Privilege Escalation Vulnerability (CVE-2021-4034)

## HPCC OpenShift 
{: #hpcc-openshft}

### March 2022 - March 24, 2022
{: #march 22}

•	 Automated deployment of an OpenShift cluster along with a Deployer VSI which allows users to easily assemble, compile and deploy the applications to OpenShift cluster.

## HPCC Scale
{: #hpcc-scale}

### May 2022
{: #may-22}

•	 Automated deployment for storage clusters with IBM Spectrum Scale as storage offering. The clusters use the compute & storage nodes configuration that is selected by the user.
