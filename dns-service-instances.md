---

copyright:
  years: 2024
lastupdated: "2024-03-18"

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

# DNS zones and DNS custom resolvers
{: #dns-custom-resolvers}

Automatically configure new DNS zones and DNS custom resolvers for your {{site.data.keyword.spectrum_full_notm}} when you deploy the cluster.

[{{site.data.keyword.cloud}} DNS Services](/docs/dns-svcs?topic=dns-svcs-getting-started) provides private DNS to VPC users. Also, if you have an {{site.data.keyword.cloud}} DNS Services instance ID, you can specify that ID when you create the DNS zones or custom resolvers for your {{site.data.keyword.spectrum_full_notm}} cluster.

## DNS zones for your {{site.data.keyword.spectrum_full_notm}} cluster
{: #dns}

Private DNS zones are resolvable only on {{site.data.keyword.cloud}}, and only from explicitly permitted networks in an account.

### Creating DNS zones without an {{site.data.keyword.cloud_notm}} DNS Services instance ID
{: #dns-clean}

If you do not have an existing {{site.data.keyword.cloud_notm}} DNS Service instance setup and want to automatically create and manage a new DNS instance ID, then leave the `dns_instance_id` deployment input value as null during deployment. In this case, the deployment creates a VPC (as necessary), DNS service instance ID, and the respective DNS zones. This process includes associating the new VPC under the appropriate permitted network.

### Creating DNS zones with an existing {{site.data.keyword.cloud_notm}} DNS Services instance ID
{: #dns-existing}

If you have an existing VPC with {{site.data.keyword.cloud_notm}} DNS Service instance ID, provide that service instance ID as the `dns_instance_id` deployment input value during {{site.data.keyword.spectrum_full_notm}} cluster deployment. In this case, since you already have a DNS service instance ID to use, the {{site.data.keyword.cloud_notm}} cluster deployment skips creating a new DNS service instance, and creates new DNS zones from the `dns_domain_names` value for the existing DNS service instance. This process automatically associates the new DNS zones to your existing VPC under the permitted network.

Furthermore, if you have an existing {{site.data.keyword.cloud_notm}} DNS Service instance setup, but want the {{site.data.keyword.spectrum_full_notm}} deployment to create a new VPC for your {{site.data.keyword.spectrum_full_notm}} cluster, provide the `dns_instance_id` deployment input value during deployment. The deployment automatically creates a new VPC with your existing DNS service instance, and creates new DNS zones for the existing DNS service instance. This process automatically associates the new VPC under the permitted network.

A VPC cannot be a permitted network for domains of the same name. When you use an existing DNS instance ID, make sure that the DNS domains are not duplicated to avoid association issues; otherwise, you can encounter a message similar to: `Error creating PDNS permitted network: The VPC was already associated to the same DNS zone name.`
{: note}

## DNS custom resolvers for your {{site.data.keyword.spectrum_full_notm}} cluster
{: #custom-resolvers}

A private DNS custom resolver extends {{site.data.keyword.cloud_notm}} DNS Service's capability to meet the needs of a hybrid cloud environment. It enables resolution of the {{site.data.keyword.cloud_notm}} VPC hostnames from on-premises DNS resolvers. It also enables the resolution of on-premises hostnames from {{site.data.keyword.cloud_notm}}.

A custom resolver can always be associated with a VPC for domain name resolution. Instead of using the dedicated IBM DNS servers for resolution, you can have a managed resolution through the VPC.

### Creating custom resolvers without an existing VPC
{: #custom-resolvers-clean}

If you want a new custom resolver to be created and associated with the newly created VPC for name resolution, then leave the `dns_custom_resolver_id` deployment input value as null during {{site.data.keyword.spectrum_full_notm}} cluster deployment. The deployment process automatically creates a new VPC and a creates and enables a new custom resolver for your cluster.

### Creating custom resolvers with an existing VPC
{: #custom-resolvers-existing}

If you already have an existing VPC and the VPC already has a custom resolver that is associated with it, then you can provide that resolver ID for the `dns_custom_resolver_id` deployment input value during {{site.data.keyword.spectrum_full_notm}} cluster deployment. In this case, the deployment automatically uses the existing VPC for all infrastructure creation, and skips creating a new custom resolver.

A single VPC can be associated with only one custom resolver. You can have multiple custom resolvers, but you can associate each one to only a single VPC; otherwise, you can encounter a message similar to: `Error reading the custom resolver. The VPC already has a custom resolver`. You can [view your existing DNS resolver IDs](/docs/dns-svcs?topic=dns-svcs-details-cr&interface=ui) in {{site.data.keyword.cloud_notm}} DNS Services.
{: note}
