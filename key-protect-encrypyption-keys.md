---

copyright:
  years: 2024
lastupdated: "2024-03-21"

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
{:table: .aria-labeledby="caption"}

# {{site.data.keyword.keymanagementservicelong}} and encryption keys
{: #key-protect}

The [{{site.data.keyword.keymanagementservicefull}} ({{site.data.keyword.keymanagementservicelong_notm}})](/docs/key-protect) service helps you provision and store encrypted keys for applications across {{site.data.keyword.cloud_notm}} services, so you can see and manage data encryption and the entire key lifecycle from one central location.

With customer-managed encryption, you can bring your own custom root key (CRK) to the cloud or have a key management service (KMS) generate a key for you. You use root keys to encrypt resources across regions. You can encrypt resources with a key that is stored in your regional KMS instance, and you can use root keys from another region.

## {{site.data.keyword.keymanagementservicelong_notm}} instances for your {{site.data.keyword.spectrum_full_notm}} cluster
{: #keys}

Use an {{site.data.keyword.keymanagementservicelong_notm}} instance regardless of whether you have the {{site.data.keyword.spectrum_full_notm}} cluster deployment process create one for you or integrate an existing one.

### Creating an {{site.data.keyword.keymanagementservicelong_notm}} instance and key
{: #new-key}

Automatically encrypt infrastructure resources through {{site.data.keyword.keymanagementservicelong_notm}} for your {{site.data.keyword.spectrum_full_notm}} cluster. To enable this feature for your cluster, always keep the `enable_customer_managed_encryption` deployment input value as **true**. The deployment process creates an {{site.data.keyword.keymanagementservicelong_notm}} instance and a specific key to encrypt these resources:

* {{site.data.keyword.block_storage_is_full}} (Cloud Block Storage)
* {{site.data.keyword.filestorage_vpc_full}}
* {{site.data.keyword.cos_full}}

If the value for `enable_customer_managed_encryption` is set as **false**, then the deployment process does not automatically create {{site.data.keyword.keymanagementservicelong_notm}} instances or keys and all infrastructure resources are encrypted through provider-managed encryption.

### Integrating an existing {{site.data.keyword.keymanagementservicelong_notm}} instance and key
{: #existing-key}

If you have an existing {{site.data.keyword.keymanagementservicelong_notm}} instance and an encryption key, set the `enable_customer_managed_encryption` deployment input value as **true** and then provide the instance ID for the `kms_instance_id` and the encryption key name for the `kms_key_name` deployment input variables instead. This way, the deployment process uses these values to encrypt all infrastructure resources for your {{site.data.keyword.spectrum_full_notm}} cluster. If you are providing an existing {{site.data.keyword.keymanagementservicelong_notm}} instance, then the user should create the required authorization policy for this instance.
