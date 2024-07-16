---

copyright:
  years: 2023
lastupdated: "2023-12-15"

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

# Integrating Active Directory with IBM Spectrum LSF
{: #integrate-active-directory-spectrum-lsf}
{: toc-content-type="tutorial"}

This tutorial provides detailed steps for system administrators on how to integrate {{site.data.keyword.spectrum_full_notm}} with an Active Directory server as the primary directory service for user authentication. The integration of {{site.data.keyword.spectrum_full_notm}} and Active Directory allows for efficient user management and access control. 

In this tutorial, you learn how to:

* Install and configure Active Directory and DNS server on a Windows Server 2019 by using PowerShell
* Connect a RHEL 8.4 OS-based LSF cluster node directly to Active Directory by using Samba Winbind, ensure encryption compatibility, and join the cluster node to an Active Directory domain

## Before you begin
{: #before-you-begin}

Before you get started, be sure to review the following prerequisites:

### General prerequisites
{: #general-prerequisites}

1. For installing and configuring the Active Directory and DNS server, you need:
    * A Windows Server 2019 machine with administrative privileges
    * Basic familiarity with PowerShell commands and Windows Server management

2. For connecting RHEL systems directly to the Active Directory by using Samba Winbind, you need:
    * A RHEL system capable of running Samba Winbind
    * Familiarity with RHEL command-line interface and system configuration
    * Access to the Active Directory domain and appropriate permissions to join the domain

### Network prerequisites
{: #network-prerequisites}

1. **Network connectivity**: You need stable and reliable network connectivity between the Windows Server 2019 machine (where Active Directory and DNS are installed) and the RHEL systems that are joined to the domain. Verify that there are no network communication issues or firewalls that are blocking essential ports.

2. **Firewall rules**: Review and update firewall rules to allow the necessary communication between the Windows Server 2019 machine, RHEL systems, and the domain controllers. Key ports that are used for Active Directory communication include TCP/UDP 53 (DNS), TCP/UDP 88 (Kerberos), TCP 135 (RPC), TCP/UDP 389 (LDAP), TCP/UDP 445 (SMB), and TCP/UDP 636 (LDAPS).

3. **Domain controller reachability**: Confirm that the RHEL systems can reach the Active Directory domain controllers without any connectivity issues. Use tools like `ping` or `nslookup` to verify the ability to resolve the domain controller's hostname and IP address from the RHEL systems.

4. **Time synchronization**: Ensure that all systems participating in the Active Directory domain, including the Windows Server 2019 machine and RHEL systems, have their clocks synchronized with a reliable time source. Time synchronization is critical for proper authentication and Kerberos ticket validation.

5. **Domain DNS configuration**: The Active Directory domain must have properly configured DNS settings. Set the domain controller's IP address as the primary DNS server on all systems (including the Windows Server 2019 machine and RHEL systems) that are part of the Active Directory domain.

6. **DNS resolution**: Verify that both forward and reverse DNS resolutions are functioning correctly. The domain controller's hostname must be resolvable from the Windows Server 2019 machine and RHEL systems, and the Windows Server 2019 machine's hostname must be resolvable from the RHEL systems.

7. **DNS domain name**: Ensure that the DNS domain name of the Active Directory matches the domain name that is used during the configuration process. In this case, the domain name "POCDOMAIN.LOCAL" used for the Active Directory must be consistent throughout the configuration.

8. **Active Directory user account with administrative privileges**: Ensure that an Active Directory user account with administrative privileges is available for use during the configuration process. This account is used to promote the Windows Server 2019 machine as a domain controller and to join the RHEL systems to the Active Directory domain.

## Install and configure Active Directory and DNS server
{: #install-configure-active-directory-dns-server}
{: step}

Complete the following steps to install and configure the Active Directory service and DNS server on Windows Server 2019: 

1. Press the **Windows + X** keys on your keyboard to access the _Power User_ menu.
2. From the list, choose **Windows PowerShell (Admin)** to start an elevated PowerShell session with administrative privileges.
3. To install the necessary roles for the Active Directory and DNS server on the Windows Server, run the following PowerShell commands:

    ```powershell
    # Install the Active Directory Domain Services role and DNS server role
    Install-WindowsFeature -Name AD-Domain-Services, DNS -IncludeManagementTools
    ```
    {: codeblock}

4. To promote the server to a domain controller with integrated DNS, run the following PowerShell command:

    ```powershell
    # Set the required parameters
    $DomainName = "POCDOMAIN.LOCAL"
    $SafeModePassword = ConvertTo-SecureString -AsPlainText "MySecureDSRMpwd2023!" -Force

    # Configure and promote the server as a domain controller with integrated DNS
    Install-ADDSForest -DomainName $DomainName -SafeModeAdministratorPassword $SafeModePassword -DomainMode Winthreshold -ForestMode Winthreshold -InstallDNS
    ```
    {: codeblock} 

    * Example domain name: `POCDOMAIN.LOCAL`
    * Example DSRM password: `MySecureDSRMpwd2023!`

5. After you run the PowerShell commands, the Active Directory Domain Services and DNS server roles are installed and configured on the server. The server automatically restarts to complete the domain controller promotion process.

### Verify configuration
{: #verify-configuration}

After the server restarts, you can log in by using the domain administrator account that you created during the promotion process.

To verify the proper configuration of Active Directory and DNS, complete the following checks:

1. To verify Active Directory management, open **Server Manager** and in the _Dashboard_ confirm the presence of "Active Directory Users and Computers" and "DNS" listed in the _Tools_ section. This indicates successful installation of the Active Directory and DNS management tools.
2. To verify user and group management, start **Active Directory Users and Computers** to manage user accounts, groups, and organizational units (OUs) within the domain.
3. To verify DNS management, access **DNS Manager** to manage DNS zones and records for the domain.
4. To verify client machine integration, on a client machine within the same network, configure the DNS settings to point to the IP address of the newly promoted domain controller. Then join the client machine to the `POCDOMAIN.LOCAL` domain. A successful connection confirms proper DNS resolution and functional Active Directory domain services.

You have now successfully installed and configured the Active Directory and DNS server on Windows Server 2019 by using PowerShell. Your new domain controller with integrated DNS is now ready to manage user accounts, computers, and other network resources within the `POCDOMAIN.LOCAL` domain.

## Create user group and users in Active Directory
{: #create-user-group-and-users}
{: step}

To create a user group, "LSF-group", and a user, "lsfuser01", in the Active Directory domain `POCDOMAIN.LOCAL`, complete the following steps by using the Active Directory Users and Computers (ADUC) management tool on a Windows server:

Creating user groups and users in the `POCDOMAIN.LOCAL` Active Directory domain requires administrative privileges within that domain. Ensure that you have the necessary permissions to create groups and users. Always set strong passwords and follow security best practices when you create user accounts and groups in Active Directory to maintain a secure network environment.
{: note}

1. Open Active Directory Users and Computers:
    1. Log in to the Windows server with your administrative privileges.
    2. Click start and search for "Active Directory Users and Computers".
    3. Start the "Active Directory Users and Computers" management console.
2. Create a user group that is named "LSF-group":
    1. In the ADUC console, navigate to the Organizational Unit (OU) in the `POCDOMAIN.LOCAL` domain where you want to create the user group.
    2. Click the OU and select "New" and then "Group."
    3. In the "New Object - Group" dialog box, enter "LSF-group" as the Group name.
    4. Choose the Group scope (for example, Global) and Group type (for example, Security).
    5. Click "OK" to create the "LSF-group" user group.
3. Create a user that is named "lsfuser01":
    1. In the ADUC console, navigate to the same or a different Organizational Unit (OU) within the "pocdomain.local" domain where you want to create the user "lsfuser01."
    2. Click the OU and select "New" and then "User".
    3. In the "New Object - User" dialog box, enter the required information for the new user "lsfuser01".
    4. Click "Next" to continue through any extra steps.
    5. Review the information entered, and click "Finish" to create the new user "lsfuser01".
4.	Add "lsfuser01" to the "LSF-group" user group:
    1. In the ADUC console, locate the "LSF-group" user group that you created earlier.
    2. Click the "LSF-group" user group and select "Properties".
    3. In the "Properties" dialog box, go to the "Members" tab.
    4. Click "Add" and then enter "lsfuser01" in the "Enter the object names to select" field.
    5. Click "Check Names" to validate the user name.
    6. Click "OK" to add "lsfuser01" to the "LSF-group" user group.
    7. Click "Apply" and then "OK" to save the changes.

## Integrate LSF cluster to Active Directory by using Samba Winbind
{: #integrate-lsf-active-directory-samba-winbind}
{: step}

To connect a RHEL 8.4-based system to Active Directory, two components are needed: Samba Winbind and `realmd`. Samba Winbind interacts with the Active Directory identity and authentication source, while `realmd` detects available domains and configures the underlying RHEL system services.

### Supported Windows platforms for direct integration
{: #supported-windows-platforms}

Direct integration with Active Directory forests is compatible with the following forest and domain functional levels:
* Forest functional level range: Windows Server 2008 - Windows Server 2016
* Domain functional level range: Windows Server 2008 - Windows Server 2016

Supported operating systems for direct integration:
* Windows Server 2022 (RHEL 8.7 and newer)
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows Server 2019 and Windows Server 2022 do not introduce new functional levels and use the highest functional level of Windows Server 2016.
{: note}

### Ensure support for common encryption types in Active Directory and RHEL
{: #ensure-support-common-encryption-types}

Samba Winbind supports RC4, AES-128, and AES-256 Kerberos encryption types by default. However, RC4 encryption is deprecated and disabled by default due to security considerations. Active Directory user credentials and trusts might still rely on RC4 encryption, leading to authentication issues.
{: important}

To ensure compatibility, you have two options:
1. Enable AES encryption support in Active Directory
2. Enable RC4 support in RHEL

For enabling RC4 support in RHEL, the steps differ depending on the RHEL version. Refer to the official documentation for detailed instructions (RHEL documentation site link).

### Join LSF cluster node to Active Directory domain
{: #join-lsf-cluster-active-directory-domain}

To join an LSF cluster node hosted on RHEL 8.4 to an Active Directory domain by using Samba Winbind and `realmd`, complete the following steps:

Samba Winbind is an alternative to the System Security Services Daemon (SSSD) for connecting a RHEL system with Active Directory. This section describes how to join a RHEL system to an Active Directory domain by using `realmd` to configure Samba Winbind.
{: note}

1. Install the following packages:

    ```bash
    # yum install realmd oddjob-mkhomedir oddjob samba-winbind-clients samba-winbind samba-common-tools samba-winbind-krb5-locator openldap-clients krb5-workstation samba adcli bind-utils
    ```
    {: codeblock}

2. Update the packages:

    ```bash
    # yum update
    ```
    {: codeblock}

3. Update `/etc/hosts` by adding the Active Directory domain IP and name:

    **Example:**

    ```bash
    [root@amit-rhel84 ~]# cat /etc/hosts
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
    10.243.0.39 addc1.POCDomain.local

    Note:- addc1.POCDomain.local is the AD server FQDN name
    ```
    {: screen}

4. Updating the DNS entries in the `/etc/resolv.conf` file by running the following command:

    ```bash
    sudo nmcli connection modify "System eth0" ipv4.dns "10.243.0.39" ipv4.ignore-auto-dns yes
    ```
    {: codeblock}

    The command not only updates the DNS entries in the `/etc/resolv.conf` file but it also ensures that the DNS entries are not automatically updated to cloud-based DNS servers.
    {: note}

5. Confirm the changes in the DNS file:

    ```bash
    [root@amit-rhel84 ~]# cat /etc/resolv.conf
    # Generated by NetworkManager
    nameserver 10.240.0.39
    [root@amit-rhel84 ~]#
    ```
    {: screen}

    And then ping the domain controller with the Active Directory domain, for example:

    ```bash
   [root@amit-rhel84 ~]#
   In addition to the confirmation, ping the Domain Controller with Name : - Ping POCDOMAIN.LOCAL
   [root@amit-rhel84 ~]# ping POCDOMAIN.LOCAL
    PING POCDOMAIN.LOCAL (10.240.0.39) 56(84) bytes of data.
    64 bytes from addc1.POCDomain.local (10.240.0.39): icmp_seq=1 ttl=128 time=0.365 ms
    64 bytes from addc1.POCDomain.local (10.240.0.39): icmp_seq=2 ttl=128 time=0.722 ms
    64 bytes from addc1.POCDomain.local (10.240.0.39): icmp_seq=3 ttl=128 time=0.581 ms
    64 bytes from addc1.POCDomain.local (10.240.0.39): icmp_seq=4 ttl=128 time=0.525 ms
    ```
    {: screen}

6. Run `nslookup` to ensure that the Active Directory domain is resolvable, for example:

    ```bash
    [root@amit-rhel84 ~]#  nslookup pocdomain.local
    Server:         10.240.0.39
    Address:        10.240.0.39#53

    Name:   pocdomain.local
    Address: 10.240.0.39
    ```
    {: screen}

7. If your Active Directory requires the deprecated RC4 encryption type for Kerberos authentication, enable support for these ciphers in RHEL:

    ```bash
    # update-crypto-policies --set DEFAULT:AD-SUPPORT
    ```
    {: codeblock}

    After you run the command, it updates the crypto policies and asks to restart the machine.

8. You now need to back up the existing `/etc/samba/smb.conf` Samba configuration file:

    ```bash
    # mv /etc/samba/smb.conf /etc/samba/smb.conf.bak
    ```
    {: codeblock}

9. Join the RHEL 8.x host to the Active Directory domain. The following example joins a domain that is named `POCDOMAIN.LOCAL`:

    ```bash
    # realm join --membership-software=samba --client-software=winbind POCDOMAIN.LOCAL
    ```
    {: codeblock }

    When you run this command, the `realm` utility automatically: 
      * Creates a `/etc/samba/smb.conf` file for a membership in the `POCDOMAIN.LOCAL` domain 
      * Adds the Winbind module for user and group lookups to the `/etc/nsswitch.conf` file 
      * Updates the Pluggable Authentication Module (PAM) configuration files in the `/etc/pam.d/` directory 
      * Starts the Winbind service and enables the service to start when the system boots 

10. (Optional) Set an alternative ID mapping back end or customized ID mapping settings in the `/etc/samba/smb.conf` file. For more information, see [Understanding and configuring Samba ID mapping](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/deploying_different_types_of_servers/assembly_using-samba-as-a-server_deploying-different-types-of-servers){: external}.
11. Edit the `/etc/krb5.conf` file and add the following section:

    ```bash
    [plugins]
    localauth = {
            module = winbind:/usr/lib64/samba/krb5/winbind_krb5_localauth.so
            enable_only = winbind
        }
    ```
    {: codeblock}

12. Verify that the Winbind service is running: 

    ```bash
    # systemctl status winbind
    ```
    {: codeblock}

    **Example request and response:**

    ```bash
    [root@amit-rhel84 ~]# systemctl status winbind
    winbind.service - Samba Winbind Daemon
    Loaded: loaded (/usr/lib/systemd/system/winbind.service; enabled; vendor preset: disabled)
    Active: active (running) since Wed 2023-08-09 08:16:16 EDT; 1h 42min ago
     Docs: man:winbindd(8)
           man:samba(7)
           man:smb.conf(5)
    Main PID: 4889 (winbindd)
     Status: "winbindd: ready to serve connections..."
     Tasks: 5 (limit: 49264)
     Memory: 10.9M
    CGroup: /system.slice/winbind.service
            ├─4889 /usr/sbin/winbindd --foreground --no-process-group
            ├─4893 /usr/sbin/winbindd --foreground --no-process-group
            ├─4894 /usr/sbin/winbindd --foreground --no-process-group
            ├─4924 /usr/sbin/winbindd --foreground --no-process-group
            └─5997 /usr/sbin/winbindd --foreground --no-process-group Important
    ```
    {: screen}

    To enable Samba to query domain user and group information, the Winbind service must be running before you start `smb`.
    {: important}

13. If you installed the Samba package to share directories and printers, enable and start the `smb` service:

    ```bash
    # systemctl enable --now smb ----tobe noted
    ```
    {: codeblock}
    
14. Ensure that password authentication is set to "yes" in the `/etc/ssh/sshd_config`` file, as shown in the following example:

    ```
    [root@amit-adclnt ~]# cat /etc/ssh/sshd_config | grep "PasswordAuth"
    #PasswordAuthentication yes
    PasswordAuthentication yes
    # PasswordAuthentication.  Depending on your PAM configuration,
    # PAM authentication, then enable this but set PasswordAuthentication
    ```
    {: screen}

### Verify integration
{: #verify-integration}

1. Verify an Active Directory user's details, such as the Active Directory administrator account in the Active Directory domain:

    ```bash
    # getent passwd "POCDOMAIN\administrator"
    ```
    {: codeblock}

    **Example output:**

    ```bash
    POCDOMAIN\administrator:*:2000500:2000513::/home/administrator@POCDOMAIN:/bin/bash
    ```
    {: screen}

2. Verify the members in the domain user group in the Active Directory domain:

    ```bash
    # getent group "POCDOMAIN\Domain Users"
    ```
    {: codeblock}

    **Example output:**

    ```bash
    POCDOMAIN\domain users:x:10000:lsfuser01,user2
    ```
    {: screen}

3. (Optional) Verify that you can use domain users and groups when you set permissions on files and directories. For example, to set the owner of the `/srv/samba/example.txt` file to `AD\administrator` and the group to `AD\Domain Users`: 

    ```bash
    # sudo chown "POCDOMAIN\administrator":"POCDOMAIN\Domain Users" example.txt
    ```
    {: codeblock}

4. Verify that Kerberos authentication works as expected. On the Active Directory domain member, obtain a ticket for the `administrator@POCDOMAIN.LOCAL` principal:

    ```bash
    # kinit administrator@POCDOMAIN.LOCAL
    ```
    {: codeblock}

    Display the cached Kerberos ticket:

    ```bash
    # klist
    ```
    {: codeblock}

    **Example output:**

    ```bash
    Ticket cache: KCM:0
    Default principal: Administrator@POCDOMAIN.LOCAL
    Valid starting       Expires              Service principal
    07/10/2023 16:28:51  07/11/2023 02:28:51  krbtgt/POCDOMAIN.LOCAL@POCDOMAIN.LOCAL
        renew until 07/17/2023 16:28:46
    ```
    {: screen}

5. Display the available domains:

    ```bash 
    [root@rhelad01 ~]# realm list
    pocdomain.local
      type: kerberos
      realm-name: POCDOMAIN.LOCAL
      domain-name: pocdomain.local
      configured: kerberos-member
      server-software: active-directory
      client-software: winbind
      required-package: oddjob-mkhomedir
      required-package: oddjob
      required-package: samba-winbind-clients
      required-package: samba-winbind
      required-package: samba-common-tools
      login-formats: POCDOMAIN\%U
      login-policy: allow-any-login
            [root@rhelad01 ~]# wbinfo --all-domains
            BUILTIN
            RHELAD01
            POCDOMAIN
    ```
    {: screen}

If you do not want to use the deprecated RC4 ciphers, you can enable the AES encryption type in the Active Directory.
{: tip}

Repeat the previous steps on all the cluster nodes to ensure that they all are added to the Active Directory Domain.
{: important}

## Provide root user permissions to Active Directory users
{: #provide-root-user-privileges}
{: step}

To provide root user permissions to Active Directory users of the `POCDOMAIN.LOCAL` domain on a Linux&reg; machine, complete the following steps:

1. Open a terminal or connect to the Linux&reg; machine.
2. Edit the `sudoers` file by using the `visudo` command:

    ```bash
    sudo visudo
    ```
    {: codeblock}

3. Locate the section in the `sudoers` file that configures user privileges:

    ```text
    # Allow root to run any commands anywhere root ALL=(ALL) ALL
    ```
    {: screen}

4. Add the following line after the root user entry to allow Active Directory users in the  `POCDOMAIN\Domain Admins` group to run commands with root privileges: `%POCDOMAIN\\Domain\ Admins ALL=(ALL) ALL`

   This line grants root user privileges to all Active Directory users in the group `POCDOMAIN\Domain Admins`. The double backslashes ("") are used to escape special characters.

5. Save the changes to the `sudoers` file and exit the editor.
6. Active Directory users who are members of the `POCDOMAIN\Domain Admins` group can now run commands with root privileges by using the `sudo command`. For example:

    ```bash
    sudo command_to_execute_as_root
    ```
    {: codeblock}

   When prompted, the Active directory user must enter their own password to authenticate and run the command with elevated privileges.

Use caution when you grant root user permissions to Active Directory users. It's important to grant these privileges only to trusted individuals who require them for specific tasks. In this use case, the root privileges are granted to the Active Directory domain administrator who holds the highest privilege in the Active Directory environment. Regularly reviewing user privileges and following security best practices helps maintain a secure system environment.
{: important}

## Configure LSF cluster
{: #configure-lsf-cluster}
{: step}

After you configure the Active Directory client authentication, you need to configure the LSF cluster to add user groups of Active Directory:

1. Log in as the LSF administrator to any host in the cluster.
2. Open `lsb.users` (`/etc/opt/ibm/lsf/conf/lsbatch/HPCCLUSTER/comfigdir`).
3. Edit the "UserGroup" section. The following example shows the "UserGroup" section of `lsb.users`; however, if you see a different "UserGroup" section, then you can create a new one with the values in this example, which is taken from the LSF cluster server:
    
    ```
    [root@ icgen2host-10-240-0-32 configdir]# cat lsb.users
      # $Revision$Date$

      # After editing this file, run "badmin reconfig" to apply your changes.

      # User groups can be referenced by the lsb.hosts and lsb.queues files.

      # All the example definitions here are commented out


      # User group for lsf cluster administration
      Begin UserGroup
      GROUP_NAME       PRIORITY       GROUPMEMBER             GROUP_ADMIN
      LSF-group       100       (lsfuser01 lsfuser05)          (lsfuser01)
      #groupA            200       (user1 user2 user3 user4)    (user5)
      #groupB            100           (groupA user5)               (groupA)
      #groupC             50            (!)                          ()
      End UserGroup


      # User group for all LSF administrators
      Begin UserGroup
      GROUP_NAME    GROUP_MEMBER      #USER_SHARES # Key words
      lsfadmins     (lsfadmin )
      Administrators  (lsfuser03)
      #ldapusers   (ldapuser01 ldapuser02)
      End UserGroup

      Note2:
      Define STRICT_UG_CONTROL=Y in lsb.params
    ```
    {: screen}

4. To enable user group administrators, specify users or user groups in the `GROUP_ADMIN` column, separate users and user groups with spaces, and enclose each `GROUP_ADMIN` entry in brackets.
5. Save your changes.
6. Run `badmin ckconfig` to check the new user group definition. If any errors are reported, fix the problem and check the configuration again.
7. Run `badmin reconfig` to reconfigure the cluster.

## Manage the LSF cluster
{: #manage-lsf-cluster}
{: step}
 
1. Log in with the Active Directory user `POCDOMAIN\lsfuser01`, and then complete the LSF cluster tasks that are mentioned in following example: 
 
    ``` 
    [POCDOMAIN\lsfuser01@icgen2host-10-240-0-32 ~]$ lshosts
    HOST_NAME      type    model  cpuf ncpus maxmem maxswp server RESOURCES
    icgen2host-  X86_64 Intel_E5  12.5     4  15.4G      -    Yes (mg)
    icgen2host-  X86_64 Intel_E5  12.5     4  15.4G      -    Yes (mg)
    icgen2host-  X86_64 Intel_E5  12.5     4  15.4G      -    Dyn ()

    [POCDOMAIN\lsfuser01@icgen2host-10-240-0-32 ~]$ lsload
    HOST_NAME       status  r15s   r1m  r15m   ut    pg  ls    it   tmp   swp   mem
    icgen2host-10-2     ok   0.0   0.0   0.0   1%   0.0   1     1   63G    0M 14.8G
    icgen2host-10-2     ok   0.0   0.0   0.0   0%   0.0   0 12818   76G    0M 14.8G
    icgen2host-10-2     ok   0.1   0.1   0.0   1%   0.0   0 12820   76G    0M 14.6G

    [POCDOMAIN\lsfuser01@icgen2host-10-240-0-32 root]$ lshosts
    HOST_NAME      type    model  cpuf ncpus maxmem maxswp server RESOURCES
    icgen2host-  X86_64 Intel_E5  12.5     4  15.4G      -    Yes (mg)
    icgen2host-  X86_64 Intel_E5  12.5     4  15.4G      -    Yes (mg)
    icgen2host-  X86_64 Intel_E5  12.5     4  15.4G      -    Dyn ()
    icgen2host-  X86_64 Intel_E5  12.5     -      -      -    Dyn (icgen2host)
    icgen2host-  X86_64 Intel_E5  12.5     -      -      -    Dyn (icgen2host)
    icgen2host-  X86_64 Intel_E5  12.5     -      -      -    Dyn (icgen2host)
    icgen2host-  X86_64 Intel_E5  12.5     -      -      -    Dyn (icgen2host)
    [POCDOMAIN\lsfuser01@icgen2host-10-240-0-32 root]$ bjobs
    No unfinished job found

    [POCDOMAIN\lsfuser01@icgen2host-10-240-0-32 root]$ lsid
    IBM Spectrum LSF Standard 10.1.0.13, Jan 04 2023
    Copyright International Business Machines Corp. 1992, 2016.
    US Government Users Restricted Rights - Use, duplication or disclosure restricted by GSA ADP Schedule Contract with IBM Corp.

    My cluster name is HPCCluster
    My master name is icgen2host-10-240-0-35
    ```
    {: screen}



