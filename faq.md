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
{:faq: data-hd-content-type='faq'}

# FAQs
{: #spectrum-lsf-faqs}

## What Spectrum LSF packages are included in a cluster deployed with this offering?
{: #my-faq-packages}
{: faq}

The following {{site.data.keyword.spectrum_short}} programs are included:
* {{site.data.keyword.spectrum_full_notm}} Standard Edition
* {{site.data.keyword.spectrum_full_notm}} License Scheduler
* {{site.data.keyword.spectrum_full_notm}} Data Manager

## What locations are available for deploying VPC resources?
{: #locations-vpc-resources}
{: faq}

Available regions and zones for deploying VPC resources, and a mapping of those to city locations and data centers can be found in [Locations for resource deployment](/docs/overview?topic=overview-locations).

## What permissions do I need in order to create a cluster using the offering?
{: #permissions-cluster-offering}
{: faq}

Instructions for setting the appropriate permissions for {{site.data.keyword.cloud_notm}} services that are used by the offering to create a cluster can be found in [Granting user permissions for VPC resources](/docs/vpc?topic=vpc-managing-user-permissions-for-vpc-resources), [Managing user access for Schematics](/docs/schematics?topic=schematics-access), and [Assigning access to Secrets Manager](/docs/secrets-manager?topic=secrets-manager-assign-access).

## How do I SSH among nodes?
{: #ssh-among-nodes}
{: faq}

All of the nodes in the HPC cluster have the same public key that you register at your cluster creation. You can use ssh-agent forwarding, which is a common technique to access remote nodes that have the same public key. It automates to securely forward private keys to remote nodes. Forwarded keys are deleted immediately after a session is closed.

To securely forward private keys to remote nodes, you need to do `ssh-add` and `ssh -A`.

```
[your local PC]~$ ssh-add {id_rsa for lsf cluster}
[your local PC]~# ssh -A -J root@jumpbox_fip root@master_private_ip
...
[root@master]~# ssh -A worker_private_ip
```
{: codeblock}

For Mac OS X, you can persist `ssh-add` by adding the following configuration to `.ssh/config`:

```
Host *
  UseKeychain yes
  AddKeysToAgent yes
```
{: codeblock}

You can even remove `-A` by adding "ForwardAgent yes" to `.ssh/config`.

## How many worker nodes can I deploy in my Spectrum LSF cluster through this offering?
{: #worker-nodes}
{: faq}

Before deploying a cluster, it is important to ensure that the VPC resource quota settings are appropriate for the size of the cluster that you would like to create (see [Quotas and service limits](/docs/vpc?topic=vpc-quotas)).

The maximum number of worker nodes that are supported for the deployment value `worker_node_max_count` is 500 (see [Deployment values](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-deployment-values)). The `worker_node_min_count` variable specifies the number of worker nodes that are provisioned at the time the cluster is created, which will exist throughout the life of the cluster. The delta between those two variables specifies the maximum number of worker nodes that can either be created or destroyed by the LSF resource connector auto scaling feature. In configurations where that delta exceeds 250, it's recommended to take caution if the characteristics of the workload are expected to result in >250 cluster node join or remove operation requests at a single point in time. In those cases, it's recommended to pace the job start and stop requests, if possible. Otherwise, you might see noticeable delays in some subset of the nodes joining or being removed from the cluster.

## Why are there two different resource group parameters that can be specified in the IBM Cloud catalog tile?
{: #resource-group-parameters}
{: faq}

The first resource group parameter entry in the **Configure your workspace** section in the {{site.data.keyword.cloud_notm}} catalog applies to the resource group where the {{site.data.keyword.bpshort}} workspace is provisioned on your {{site.data.keyword.cloud_notm}} account. The value for this parameter can be different than the one used for the second entry in the **Parameters with default values** section in the catalog. The second entry applies to the resource group where VPC resources are provisioned. As specified in the description for this second `resource_group` parameter, note that only the default resource group is supported for use of the LSF Resource Connector auto-scaling feature.

## Where are the Terraform files used by the IBM Spectrum LSF tile located?
{: #terraform-file-location}
{: faq}

The Terraform-based templates can be found in this [GitHub repository](https://github.com/IBM-Cloud/hpc-cluster-lsf){: external}.

## Where can I find the custom image name to image ID mappings for each cloud region?
{: #custom-image-mappings}
{: faq}

The mappings can be found in the `image-map.tf` file in this [GitHub repository](https://github.com/IBM-Cloud/hpc-cluster-lsf){: external}.