---

copyright:
  years: 2024
lastupdated: "2024-02-22"

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

# Checking OpenLDAP status
{: #checking-openldap-status}

After {{site.data.keyword.spectrum_full_notm}} cluster deployment, Schematics logs show essential information in the output section. From here, you can check your LDAP server status:

1. Connect to your OpenLDAP server through SSH by using the `ssh_to_ldap_node` command from the {{site.data.keyword.bpshort}} log output.

    For example:

    ```text
    ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -o ServerAliveInterval=5 -o ServerAliveCountMax=1 -J vpcuser@<floatingg_IP_address> ubuntu@<LDAP_server_IP>
    ```
    {: codeblock}

    where `<floating_IP_address>` is the floating IP address for the bastion node and `<LDAP_server_IP>` is the IP address for the OpenLDAP node.

2. Verify the LDAP service status:

    ```text
    systemctl status slapd
    ```
    {: codeblock}

3. Verify the LDAP groups and users created:

    ```text
    ldapsearch -Q -LLL -Y EXTERNAL -H ldapi:///
    ```
    {: codeblock}
