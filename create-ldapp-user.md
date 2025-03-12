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

# Creating an LDAP user
{: #create-ldap-user}

If you do not have an existing LDAP server, the deployment process creates a new LDAP server through which the user can access the {{site.data.keyword.spectrum_full_notm}} cluster to manage the remaining user creation. Create an LDAP user for your {{site.data.keyword.spectrum_full_notm}} LDAP server.

Use this LDAP server to run the LSF commands and submit the LSF jobs with existing authentication credentials, reducing the need to remember multiple login credentials.

If you have an existing LDAP server with the LDAP information you provided during the {{site.data.keyword.spectrum_full_notm}} cluster deployment, then no need to create a new LDAP user.

## Before you begin
{: #prequisites}

Ensure that you have the following LDAP information:

* `BASE_DN`: The base domain name for your LDAP server (for example, **ldap.com**).
* `LDAP_ADMIN_PASSWORD`: The password for the LDAP administrator.
* `OU_NAME`: The organizational unit where users are located (for example, **People**).
* `EXISTING_LDAP_USER`: The username of an existing LDAP user to be verified.
* `LDAP_USER`: The username for the new LDAP user.
* `NEW_LDAP_USER_PASSWORD`: The password for the new LDAP user.

You can export this LDAP information from the LDAP server; for example:

```console
export BASE_DN="ldap.com"
export OU_NAME="People"
export LDAP_ADMIN_PASSWORD="Admin@123"
export LDAP_USER="testuser01"
export UNIQUE_USER_ID="10089"
export NEW_LDAP_USER_PASSWORD="Test@1234"
```
{: pre}

## Creating the user
{: #create-user}

1. Connect to your OpenLDAP server through SSH by using the `ssh_to_ldap_node` command from the {{site.data.keyword.bpshort}} log output.

    For example:

    ```text
    ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -o ServerAliveInterval=5 -o ServerAliveCountMax=1 -J vpcuser@<floating_IP_address> ubuntu@<LDAP_server_IP>
    ```
    {: codeblock}

    where `<floating_IP_address>` is the floating IP address for the bastion node and `<LDAP_server_IP>` is the IP address for the OpenLDAP node.

2.  See whether the LDAP user is an existing one:
    ```text
    ldapsearch -x -D "cn=admin,dc=${BASE_DN%%.*},dc=${BASE_DN#*.}" -w "${LDAP_ADMIN_PASSWORD}" -b "dc=${BASE_DN%%.*},dc=${BASE_DN#*.}" "(uid=${EXISTING_LDAP_USER})"
    ```
    {: codeblock}

3.  Generate a hashed password for the user:
    ```text
    slappasswd -s "${NEW_LDAP_USER_PASSWORD}"
    ```
    {: codeblock}

    Copy this generated hash for the next step.

4.  Create a user configuration file called `${LDAP_USER}.ldif`, with the necessary attributes for the new LDAP user. Replace `<Unique User ID>` with a unique user ID and `<Hash password for the new user>`with the hash that is obtained from the previous step.

    **Example `${LDAP_USER}.ldif` file:**

    ```console
    echo "dn: uid=${LDAP_USER},ou=${OU_NAME},dc=${BASE_DN%%.*},dc=${BASE_DN#*.}
    objectClass: inetOrgPerson
    objectClass: posixAccount
    objectClass: shadowAccount
    uid: ${LDAP_USER}
    sn: ${LDAP_USER}
    givenName: ${LDAP_USER}
    cn: ${LDAP_USER}
    displayName: ${LDAP_USER}
    uidNumber: <Unique User ID>
    gidNumber: 5000
    userPassword: <Hash password for the new user>
    gecos: ${LDAP_USER}
    loginShell: /bin/bash
    homeDirectory: /home/${LDAP_USER}" > ${LDAP_USER}.ldif
    ```
    {: pre}

5.  Create the LDAP user, by using the details from the `${LDAP_USER}.ldif` file:

    ```text
    ldapadd -x -D "cn=admin,dc=${BASE_DN%%.*},dc=${BASE_DN#*.}" -w "${LDAP_ADMIN_PASSWORD}" -f "${LDAP_USER}.ldif"
    ```
    {: codeblock}

To accommodate a potentially large number of LDAP users (where each user has an individual IP address), update the security group for the {{site.data.keyword.spectrum_full_notm}} cluster systematically. Moreover, instead of manually adding each user's IP address, a more scalable approach involves allowing CIDR ranges for the respective users. This way, as new LDAP users are created, their entire IP range is authorized, simplifying the management of security configurations. Consider implementing automation to streamline the process and ensure the security group remains up to date with the dynamic nature of LDAP user IPs. Regular reviews and documentation maintenance are essential to adapt to changes in user access and uphold security protocols effectively.
{: note}
