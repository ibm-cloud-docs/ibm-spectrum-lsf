---

copyright:
years: 2023, 2024
lastupdated: "2024-03-19"

keywords:
subcollection: ibm-spectrum-lsf

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:download: .download}
{:important: .important}
{:note: .note}
{:new_window: target="_blank"}

# Integrating OpenLDAP with your {{site.data.keyword.spectrum_full_notm}} cluster
{: #integrating-openldap}

You enable OpenLDAP with your {{site.data.keyword.spectrum_full_notm}} cluster, [during deployment](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-using-hpc-cluster) by setting the `enable_ldap`,`ldap_basedns`, `ldap_server`, `ldap_admin_password`, `ldap_user_name`, and `ldap_user_password` deployment input values. If you do not have an existing LDAP server, the deployment process creates one for you and connect it to {{site.data.keyword.spectrum_full_notm}} cluster. If you have an existing LDAP server, you can provide LDAP information during the {{site.data.keyword.spectrum_full_notm}} cluster deployment.

If you have an existing OpenLDAP server, you can use that with your {{site.data.keyword.spectrum_full_notm}} cluster. Before you deploy the {{site.data.keyword.spectrum_full_notm}} cluster with your existing LDAP server input values, ensure you complete the following LDAP requirements:
1. OpenLDAP version 2.4 or later is installed and configured.
2. The OpenLDAP server can communicate with the {{site.data.keyword.spectrum_full_notm}} cluster nodes over the network. This can be achieved by configuring the network settings on both the OpenLDAP server and the {{site.data.keyword.spectrum_full_notm}} cluster nodes.
3. The OpenLDAP server and the {{site.data.keyword.spectrum_full_notm}} cluster nodes can communicate over port 389.

Also, always allow access to the CIDR ranges for the VPC that the {{site.data.keyword.spectrum_full_notm}} cluster deployment creates. Make sure that the security groups for the existing LDAP server are allowlisted with the VPC CIDR range of newly created VPC. This way, the new VPC can connect to the existing your existing OpenLDAP server and that all management and login nodes can access your LDAP server.

If the security groups of the LDAP server are updated with the VPC CIDR ranges, you see a message similar to this:

```text
Emodule.hpc.module.validate_ldap_server_connection.null_resource.validate_ldap_server_connection[0] (remote-exec): The connection to the existing LDAP server 10.241.0.5 was successfully established.
```
{: codeblock}


However, if the connection to the existing LDAP server is not established, you see a message similar to this:

```text
Error: remote-exec provisioner error
│
│ with module.hpc.module.validate_ldap_server_connection.null_resource.
| validate_ldap_server_connection[0],
| on modules/null/ldap_remote_exec/ldap_remote_exec.tf line 16, in resource
| "null_resource" "validate_ldap_server_connection": 16: provisioner
| "remote-exec" {
│
│ error executing "/tmp/terraform_888134906.sh": Process exited with status 1
```
{: codeblock}
