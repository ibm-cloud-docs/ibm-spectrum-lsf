---

copyright:
  years: 2023
lastupdated: "2023-08-09"

keywords:
content-type: tutorial
services:
account-plan:
completion-time:
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
{:step: data-tutorial-type='step'}

# Integrating OpenLDAP with IBM Spectrum LSF
{: #integrate-openldap-spectrum-lsf}
{: toc-content-type="tutorial"}

{{site.data.keyword.spectrum_full_notm}} is a workload management platform that provides powerful resource management capabilities to optimize application performance and maximize resource usage. OpenLDAP is an open source implementation of the Lightweight Directory Access Protocol (LDAP) that provides centralized authentication and directory services.

By following the steps outlined in this tutorial, you can integrate OpenLDAP with {{site.data.keyword.spectrum_full_notm}}, which allows you to use your existing LDAP infrastructure for authentication, enabling more secure and streamlined user access to {{site.data.keyword.spectrum_full_notm}}.

This tutorial only covers configuring the OpenLDAP server on a Linux&reg; system (RHEL 7.9 as the host).
{: note}

## Design considerations
{: #design-considerations}

Integrating OpenLDAP with {{site.data.keyword.spectrum_full_notm}} involves modifying the LSF configuration files and the OpenLDAP server configuration files. The design considerations for this integration include:

1. {{site.data.keyword.spectrum_full_notm}} uses the Pluggable Authentication Module (PAM) framework for authentication.
2. The PAM configuration for {{site.data.keyword.spectrum_full_notm}} is located in the `/etc/pam.d/lsf` file.
3. OpenLDAP uses a hierarchical naming structure that is called the Distinguished Name (DN) to identify entries in the directory.
4. The OpenLDAP configuration file is located in `/etc/openldap/slapd.conf` or `/etc/openldap/slapd.d/`.
5. The OpenLDAP server must be configured to use the LDAP protocol over a secure connection (LDAPS) for authentication.

## Before you begin
{: #before-you-begin}

Before you get started, be sure to review the following prerequisites:

### General prerequisites
{: #general-prerequisities}

1. {{site.data.keyword.spectrum_full_notm}} version 10.1 or later is installed and configured.
2. OpenLDAP version 2.4 or later is installed and configured.
3. The OpenLDAP server is accessible from the {{site.data.keyword.spectrum_full_notm}} cluster nodes.
4. The `ldapsearch` command-line tool is installed on each {{site.data.keyword.spectrum_full_notm}} cluster node.
5. The `pam_ldap` and `nss_ldap` LDAP client libraries are installed on each {{site.data.keyword.spectrum_full_notm}} cluster node.

### Network prerequisites
{: #network-prerequisites}

To successfully integrate OpenLDAP with Spectrum LSF cluster nodes, the following network requirements must be met:

1. **Network connectivity between the OpenLDAP server and the Spectrum LSF cluster nodes**: Ensure that the OpenLDAP server can communicate with the Spectrum LSF cluster nodes over the network. This can be achieved by configuring the network settings on both the OpenLDAP server and the Spectrum LSF cluster nodes.
2. **Port requirements**: The OpenLDAP server and the Spectrum LSF cluster nodes must be able to communicate over specific ports. By default, OpenLDAP uses port 389 for unencrypted communication and port 636 for encrypted communication.
3. **Firewall configuration**: If a firewall is present in the network, ensure that the necessary ports are open for communication between the OpenLDAP server and the Spectrum LSF cluster nodes.
4. **DNS configuration**: Ensure that the Spectrum LSF cluster nodes can resolve the hostname or IP address of the OpenLDAP server. If DNS resolution is not available, configure the `/etc/hosts` file on each node to include the OpenLDAP server's hostname and IP address.

## Configure an OpenLDAP server
{: #configure-server}
{: step}

Make sure that you have access to a Linux&reg; system with root privileges.
{: important}

1. Install the OpenLDAP server and client packages by running the following command:

    ```
    yum -y install openldap-servers openldap-clients
    ```
    {: codeblock}

2. Copy the `DB_CONFIG.example` file to the `/var/lib/ldap` directory and change its ownership to the `ldap` user by running the following commands:

    ```
    cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG 
    chown ldap. /var/lib/ldap/DB_CONFIG
    ```
    {: codeblock}

3. Start the `slapd` service and enable it to start automatically at boot time by running the following commands:

    ```
    systemctl start slapd 
    systemctl enable slapd
    ```
    {: codeblock}

4. Generate an admin password by running the `slappasswd` command. You are prompted to enter a password. For example:

    ```
    slappasswd
    ```
    {: codeblock}

    You see output that looks something like the following:

    **Example output:**

    ```
    {SSHA}FUMV8TZ9lZQxABxCBE5UZ+oU/dlwf/d4
    ```
    {: screen}

    Note the password hash that is generated (in this case, `{SSHA}FUMV8TZ9lZQxABxCBE5UZ+oU/dlwf/d4)`) as you need it later.

5. Create a file that is named `chrootpw.ldif` and add the following lines to it:

    ```
    dn: olcDatabase={0}config,cn=config
    changetype: modify
    add: olcRootPW
    olcRootPW: {SSHA}FUMV8TZ9lZQxABxCBE5UZ+oU/dlwf/d4
    ```
    {: codeblock}

    Replace the `olcRootPW` value with the password hash that you generated in the previous step.

6. Import the basic schema by running the following commands:

    ```
    ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif
    ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif
    ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif
    ```
    {: codeblock}

7. Generate a manager password by running the `slappasswd` command again.

    ```
    slappasswd
    ```
    {: codeblock}

    You see output that looks something like the following:

    **Example output:**

    ```
    {SSHA}TVW9z6WLIBC3EXtFHFWnb2EVlK7EZQ3b
    ```
    {: screen}

    Note the password hash that is generated (in this case, `{SSHA}TVW9z6WLIBC3EXtFHFWnb2EVlK7EZQ3b`) as you need it later.

8. Add the manager password and enable the manager account by creating a file that is named `chdomain.ldif` and adding the following lines to it:

    ```
    # DC should be your domain
    # specify the password generated above for "olcRootPW" section
    dn: olcDatabase={1}monitor,cn=config
    changetype: modify
    replace: olcAccess
    olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth"
      read by dn.base="cn=Manager,dc=ibmlsf,dc=com" read by * none
    dn: olcDatabase={2}hdb,cn=config
    changetype: modify
    replace: olcSuffix
    olcSuffix: dc=ibmlsf,dc=com
    dn: olcDatabase={2}hdb,cn=config
    changetype: modify
    replace: olcRootDN
    olcRootDN: cn=Manager,dc=ibmlsf,dc=com
    dn: olcDatabase={2}hdb,cn=config
    changetype: modify
    add: olcRootPW
    olcRootPW: {SSHA}TVW9z6WLIBC3EXtFHFWnb2EVlK7EZQ3b
    dn: olcDatabase={2}hdb,cn=config
    changetype: modify
    add: olcAccess
    olcAccess: {0}to attrs=userPassword,shadowLastChange by
      dn="cn=Manager,dc=ibmlsf,dc=com" write by anonymous auth by self write by * none
    olcAccess: {1}to dn.base="" by * read
    olcAccess: {2}to * by dn="cn=Manager,dc=ibmlsf,dc=com" write by * read
    ```
    {: codeblock}

    Replace the {SSHA} value with the password hash that you generated in the previous step.

9. Apply the changes by running the following command:

    ```
    ldapmodify -Y EXTERNAL -H ldapi:/// -f chdomain.ldif
    ```
    {: codeblock}

10. Create a file that is named `basedomain.ldif` and add the following lines to it:

    ```
    dn: dc=ibmlsf,dc=com
    objectClass: top
    objectClass: dcObject
    objectclass: organization
    o: WES Migration
    dc: ibmlsf
    dn: cn=Manager,dc=ibmlsf,dc=com
    objectClass: organizationalRole
    cn: Manager
    description: Directory Manager
    dn: ou=People,dc=ibmlsf,dc=com
    objectClass: organizationalUnit
    ou: People
    dn: ou=Group,dc=ibmlsf,dc=com
    objectClass: organizationalUnit
    ou: Group
    ```
    {: codeblock}

11. Apply the changes that are made in the previous step by running the following command:

    ```
    ldapadd -x -D cn=Manager,dc=ibmlsf,dc=com -W -f basedomain.ldif
    ```
    {: codeblock}

12. Create a file that is named `ldapuser.ldif` by using an editor, such as VI, and add the following lines to it (replace with your own required domain name in the `"dc=***,dc=***"` section):

    ```
    dn: uid=Lsfclusteruser,ou=People,dc=ibmlsf,dc=com
    objectClass: inetOrgPerson
    objectClass: posixAccount
    objectClass: shadowAccount
    cn: Lsfclusteruser
    sn: Linux
    userPassword: {SSHA}+A6gC87JU5ugW6qthWL2HGYzsQIdN1EN
    loginShell: /bin/bash
    uidNumber: 1003
    gidNumber: 1003
    homeDirectory: /home/Lsfclusteruser
    dn: cn=Lsfclusteruser,ou=Group,dc=ibmlsf,dc=com
    objectClass: posixGroup
    cn: Lsfclusteruser
    gidNumber: 1001
    memberUid: Lsfclusteruser
    ```
    {: codeblock}

    Replace the {SSHA} value with the password hash that you generated earlier.

13. Apply the changes by running the following command:

    ```
    ldapadd -x -D cn=Manager,dc=ibmlsf,dc=com -W -f ldapuser.ldif
    ```
    {: codeblock}

14. To verify whether users have been created as mentioned in the previous steps, use the following command:

    ```
    ldapsearch -x -LLL -b "ou=People,dc=ibmlsf,dc=com" "(objectClass=posixAccount)" uid cn
    ```
    {: codeblock}

The OpenLDAP server is now configured and ready to use. You can add more users and groups by creating more LDIF files and by using the `ldapadd` command to import them into the directory.

## Create a user group in OLDAP directory
{: #create-user-group}
{: step}

Now that the OpenLDAP server is configured, you need to create a user group in the OLDAP directory, which consists of users who are supposed to get access to the LSF cluster.

1. To create a group, use the `ldapadd` command to add an LDIF entry for the group to the LDAP directory. For example, to create groups called "LSFadministrators" and "LSFconsumers", you can create an LDIF file with the following contents:

    ```
    # create an organizational unit for groups
    dn: ou=groups,dc=ibmlsf,dc=com
    objectClass: organizationalUnit
    ou: groups

    # create a group called "LSFadministrators"
    dn: cn=LSFadministrators,ou=groups,dc=ibmlsf,dc=com
    objectClass: top
    objectClass: posixGroup
    gidNumber: 1001
    cn: LSFadministrators
    description: LSFadministrators group

    # create a group called "LSFconsumers"
    dn: cn=LSFconsumers,ou=groups,dc=ibmlsf,dc=com
    objectClass: top
    objectClass: posixGroup
    gidNumber: 1002
    cn: LSFconsumers
    description: LSFconsumers group
    ```
    {: codeblock}

    In this example, the first entry creates an organizational unit called "groups". The next two entries create groups that are called "LSFadministrators" and "LSFconsumers".

    Each group entry specifies the `objectClass` "top" and "posixGroup" to define the group's schema. The `gidNumber` attribute specifies the group's unique identifier, while the `cn` attribute specifies the group's common name. Finally, the `description` attribute provides a brief description of the group.

2. You can save the content from the previous step in a file with a `.ldif` extension, such as `groups.ldif`, and then use the `ldapadd` command to add these group entries to your LDAP directory. See the following `ldapadd` example command:

    ```
    ldapadd -x -D cn=Manager,dc=ibmlsf,dc=com -W -f groups.ldif

    [root@oldapserverlsfcl ~]# ldapsearch -x -D cn=Manager,dc=ibmlsf,dc=com -W -b "ou=groups,dc=ibmlsf,dc=com" "(objectClass=posixGroup)"

    Enter LDAP Password:

    # extended LDIF
    #
    # LDAPv3
    # base <ou=groups,dc=ibmlsf,dc=com> with scope subtree
    # filter: (objectClass=posixGroup)
    # requesting: ALL
    #

    # LSFadministrators, groups, ibmlsf.com
    dn: cn=LSFadministrators,ou=groups,dc=ibmlsf,dc=com
    objectClass: top
    objectClass: posixGroup
    gidNumber: 1001
    cn: LSFadministrators
    description: LSFadministrators group
    memberUid: user2lsf
    memberUid: user1lsf

    # LSFconsumers, groups, ibmlsf.com
    dn: cn=LSFconsumers,ou=groups,dc=ibmlsf,dc=com
    objectClass: top
    objectClass: posixGroup
    gidNumber: 1002
    cn: LSFconsumers
    description: LSFconsumers group
    memberUid: lsfoprt
    memberUid: lsfsupport

    # search result
    search: 2
    result: 0 Success
    ```
    {: codeblock}

This output shows that there are two group entries: `LSFadministrators` and `LSFconsumers`, both in the `ou=groups,dc=ibmlsf,dc=com` organizational unit.

Later on, you can use the `ldapmodify` command to add exiting users to these groups.  In the previous `ldapsearch` output, two users are seen as the member of each of the groups.

## Configure LDAP authentication on client machines
{: #configure-ldap-authentication}
{: step}

Next you need to configure LDAP authentication for the client machines that are running RHEL 8 as the OS and OLDAP server as the authentication provider or identity provider. In this case, the OLDAP client is the LSF cluster, so the following steps apply to the LSF cluster nodes that are running on top of RHEL 8 Linux&reg; machines.

In the following steps, make sure to replace the domain name in the `"dc=***,dc=***"` section with your own domain name.
{: important}

1. Run the following commands:

    ```
    yum -y install openldap-clients nss-pam-ldapd
    ```
    {: codeblock}

    ```
    yum install authselect sssd oddjob oddjob-mkhomedir
    ```
    {: codeblock}

    ```
    authconfig --enablesssd --enablesssdauth --update
    ```
    {: codeblock}

2. Enable the SSSD authentication profile:

    ```
    authselect select sssd
    ```
    {: codeblock}

3. Add the LDAP server URL and the base search DN to the `/etc/openldap/ldap.conf` file as shown in the following example:

    ```
    URI ldap://ibmlsf.com/ <or IP address will also work>
    BASE dc=ibmlsf,dc=com
    ```
    {: codeblock}

4. In the `/etc/sssd` directory, create the file `sssd.conf` with the following contents:

    ```
    [domain/default]
    autofs_provider = ldap
    cache_credentials = True
    ldap_search_base = dc=ibmlsf,dc=com
    id_provider = ldap
    auth_provider = ldap
    chpass_provider = ldap
    ldap_uri = ldap://<ip address of the LDAP Server>

    [sssd]
    services = nss, pam, autofs
    domains = default

    [nss]
    homedir_substring = /home
    ```
    {: codeblock}

    Update the `ldap_search_base` parameter with the base DN, and update `ldap_uri` with the URL to the LDAP server.

5. Change the permissions on the `/etc/sssd/sssd.conf` file:

    ```
    chmod 600 /etc/sssd/sssd.conf
    ```
    {: codeblock}

6. Restart and enable SSSD:

    ```
    systemctl restart sssd
    systemctl enable sssd
    ```
    {: codeblock}

You have now configured SSSD to use LDAP for user authentication on your RHEL 8 system with the LDAP server "ibmlsf.com".

## Configure LSF cluster
{: #configure-lsf-cluster}
{: step}

Now that you've configured the LDAP client authentication, you need to configure the LSF cluster that is hosted on the LDAP to point to the OLDAP server, which ensures that the integration of the LSF cluster with the OLDAP server is possible for user management.

1. Back up the existing `lsf.conf` and `lsf.cluster.name` files:

    ```
    cp/etc/lsf.conf /etc/lsf.conf.backup cp/etc/lsf.cluster.name /etc/lsf.cluster.name.backup
    ```
    {: codeblock}

2. Edit the `lsf.conf` file to add the LDAP authentication settings:

    ```
    AUTHMODE=ldap AUTHLDAPSERVER=<OpenLDAP_server>
    AUTHLDAPBASEDN=<LDAP_base_DN> AUTHLDAPUSER=<bind_DN>
    AUTHLDAPPASS=<bind_password>
    AUTHLDAPUSERTYPE=<user_object_class>
    AUTHLDAPUSERSEARCH=<user_search_filter>
    AUTHLDAPGROUP=<group_object_class>
    AUTHLDAPGROUPSEARCH=<group_search_filter>
    ```
    {: codeblock}

    Replace the values with your relevant information. See the following table for more details:

    | Value | Description |
    | ----- | ------------ |
    | `<OpenLDAP_server>` | The hostname or IP address of the OpenLDAP server |
    | `<LDAP_base_DN>` | The base DN of the OpenLDAP directory |
    | `<bind_DN>` | The distinguished name of the user that {{site.data.keyword.spectrum_full_notm}} should use to bind to the OpenLDAP server for authentication purposes |
    | `<bind_password>` | The password for the bind user |
    | `<user_object_class>` | The object class for user entries in the OpenLDAP directory |
    | `<user_search_filter>` | The search filter for user entries |
    | `<group_object_class>` | The object class for group entries |
    | `<group_search_filter>` | The search filter for group entries |
    {: caption="Table 1. LDAP authentication settings" caption-side="top"}

3. Restart the following three services so that the authentication settings you configured in the `lsf.conf` file can take effect:

    ```
    bctrld restart sbd all
    ```
    {: codeblock}

    ```
    bctrld restart res all
    ```
    {: codeblock}

    ```
    bctrld restart lim all
    ```
    {: codeblock}

4. Edit the `lsf.cluster.name` file to specify the LDAP authentication domain:

    ```
    LSF_CLUSTER_NAME=<cluster_name> LSF_LDAP_DOMAIN=<LDAP_authentication_domain
    ```
    {: codeblock}

    Replace `<cluster_name>` with the name of the {{site.data.keyword.spectrum_full_notm}} cluster and `<LDAP_authentication_domain>` with the name of the LDAP authentication domain.

### Grant access to LSF cluster
{: #grant-access-lsf-cluster}

To grant access to the LSF cluster to the group, you need to edit the LSF configuration files to include the group in the appropriate access control lists (ACLs).

The specific LDAP command-line tools and commands might vary.
{: note}

1. Log in as the LSF administrator to any host in the cluster.
2. Open `lsb.users` (`/opt/ibm/lsf/conf/lsbatch/HPCCLUSTER/configdir`).
3. Edit the "UserGroup" section. The following example shows the "UserGroup" section of `lsb.users`; however, if you see a different "UserGroup" section, then you can create a new one with the values in this example, which is taken from the LSF cluster server:

    ```
    [root@icgen2host-172-19-1-69 configdir]# cat lsb.users
    # $Revision$Date$

    # After editing this file, run "badmin reconfig" to apply your changes.

    # User groups can be referenced by the lsb.hosts and lsb.queues files.

    # All the example definitions here are commented out


    # User group for lsf cluster administration
    Begin UserGroup
    GROUP_NAME   PRIORITY       GROUP_MEMBER                 GROUP_ADMIN
    lsfamitadmin     100         (user1lsf user2lsf)          (user1lsf)
    #groupA          200         (user1 user2 user3 user4)    (user5)
    #groupB          100           (groupA user5)               (groupA)
    #groupC          50            (!)                          ()
    End UserGroup


    # User group for all LSF administrators
    Begin UserGroup
    GROUP_NAME    GROUP_MEMBER      #USER_SHARES # Key words
    lsfadmins     (lsfadmin )
    newlsf        (lsfuser123)
    #ldapusers   (ldapuser01 ldapuser02)
    End UserGroup

    # Perform the following edit in the lsb.params file located in "/opt/ibm/lsf/conf/lsbatch/HPCCLUSTER/configdir"
    Define STRICT_UG_CONTROL=Y in lsb.params
    ```
    {: codeblock}

4. To enable user group administrators, specify users or user groups in the `GROUP_ADMIN` column, separate users and user groups with spaces, and enclose each `GROUP_ADMIN` entry in brackets.
5. Save your changes.
6. Run `badmin ckconfig` to check the new user group definition. If any errors are reported, fix the problem and check the configuration again.
7. Run `badmin reconfig` to reconfigure the cluster.

### Manage the LSF cluster
{: #manage-lsf-cluster}

After you log in, you can use the EGO commands to manage the LSF cluster, such as `egosh resource`, `egosh job`, `egosh host`, and so on (as shown in the following example).

To complete the following steps, you need to use an LSF user account with high privileges. Only an account with high privileges can add the users to LSF cluster and then assign roles to them. However, if the newly added user is assigned `CLUSTER_Admin` role, then that user can also complete the following steps for subsequently added users.
{: tip}

1. To add the existing user, "user1lsf", that was created in the OLDAP server to the LSF cluster, you need to log in as the cluster administrator, and then run the following commands:

    ```
    [root@icgen2host-172-19-1-69 ~]# su Lsfadmin
    bash-4.4$ egosh user logon -u Admin -x Admin
    Admin@HPCCluster> user add
    user account: user1lsf
    password:
    password(type again, please):
    User account < user1lsf > added successfully
    Admin@HPCCluster>@HPCCluster> user logoff
    Logged off successfully
    ```
    {: codeblock}

2. Log in with the user that was added in the previous step to the LSF cluster by running the following commands:

    ```
    [root@icgen2host-172-19-1-69 ~]# su user1lsf
    bash-4.4$ egosh user logon -u user1lsf -x welcome@31!
    Logged on successfully
    bash-4.4$ egosh
    user1lsf@HPCCluster> resource list
    NAME     status       mem    swp    tmp   ut    it    pg   r1m  r15s  r15m  ls
    icgen2h* ok           14G     0M    74G   2%     0     0   0.1     0     0   1
    icgen2h* ok           15G     0M    75G   0% 20339     0     0   0.3     0   0
    user1lsf@HPCCluster> ego info
    Cluster name            : HPCCluster
    EGO master host name    : icgen2host-172-19-1-69
    EGO master version      : 3.4
    user1lsf@HPCCluster>
    ```
    {: codeblock}

3. Assign roles to the new users by running the following commands:

    ```
    lsfadmin@HPCCluster> user assignrole -u user1lsf -r CONSUMER_USER
    Role <Consumer User> is assigned to user <user1lsf>.
    lsfadmin@HPCCluster> user list
    ACCOUNT        PHONE          EMAIL          DESCRIPTION
    ----------------------------------------------------------------
    Admin
    Guest
    user2lsf
    user1lsf
    smith
    ```
    {: codeblock}

## Conclusion
{: #conclusion}

You have now successfully integrated OpenLDAP with {{site.data.keyword.spectrum_full_notm}} to provide centralized authentication and directory services, which enable you to use your existing LDAP infrastructure for authentication. You have also enabled secure and streamlined user access to {{site.data.keyword.spectrum_full_notm}}.
