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
{:faq: data-hd-content-type='faq'}

# FAQs
{: #spectrum-lsf-faqs}

## What Spectrum LSF packages are included in a cluster deployed with this offering?
{: #my-faq-packages}
{: faq}

The following Spectrum LSF programs are included:
* IBM Spectrum LSF Standard Edition
* IBM Spectrum LSF License Scheduler
* IBM Spectrum LSF Data Manager

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

The maximum number of worker nodes that are supported for the deployment value `worker_node_max_count` is 500 (see [Deployment values](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-deployment-values)). The `worker_node_min_count` variable specifies the number of worker nodes that are provisioned at the time the cluster is created, which will exist throughout the life of the cluster. The delta between those two variables specifies the maximum number of worker nodes that can either be created or destroyed by the LSF resource connector auto scaling feature. In configurations where that delta exceeds 250, it's recommended to take caution if the characteristics of the workload are expected to result in >250 cluster node join or remove operation requests at a single point in time. In those cases, it's recommended to pace the job start and stop requests, if possible. Otherwise, you might see noticeable delays in some subset of the nodes joining or being removed from the cluster.