---

copyright:
  years: 2024
lastupdated: "2024-09-03"
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

# Best practices for using {{site.data.keyword.spectrum_full_notm}} through an LDAP user
{: #submit-jobs-lsf-ldap}

After you integrate OpenLDAP with your {{site.data.keyword.spectrum_short}} cluster, refer to the following guidance for best practices when running LSF commands and submitting LSF jobs as an LDAP user.

All the following commands can also be run from the login node.
{: note}

1. Log in to the management node by using the `ssh_to_management_node` value as shown as part of output section of {{site.data.keyword.bpshort}} job log:

    * Primary management node:

    ```text
    ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -J vpcuser@<floating_IP_address> lsfadmin@<management_node_IP_address>
    ```
    {: codeblock}

    Where `<floating_IP_address>` is the floating IP address for the bastion node and `<management_node_IP_address>` is the IP address for the management node.

    * Login node:

    ```text
    ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -J vpcuser@<floating_IP_address> lsfadmin@<login_node_IP_address>
    ```
    {: codeblock}

    Where `<floating_IP_address>` is the floating IP address for the bastion node and `<login_node_IP_address>` is the IP address for the login node.

2. Switch to the LDAP user (for example, switch to lsfuser05):

    ```text
    $ [lsfadmin@lsfuser05-ubuntu-two-mgmt-1 ~]$ su lsfuser05
    Password:
    $ [lsfuser05@lsfuser05-ubuntu-two-mgmt-1 lsfadmin]#
    ```
    {: codeblock}

3. Run LSF commands as the LDAP user:

    ```text
    $ [lsfuser05@lsfuser05-ubuntu-two-mgmt-1 lsfadmin]$ lshosts -w
    HOST_NAME                        type   model    cpuf ncpus maxmem maxswp server RESOURCES
    lsfuser05-ubuntu-two-mgmt-1 X86_64 Intel_E5 12.5 2     7.5G   -      Yes    (mg)
    lsfuser05-ubuntu-two-mgmt-2 X86_64 Intel_E5 12.5 2     7.5G   -      Yes    (mg)
    lsfuser05-ubuntu-two-mgmt-3 X86_64 Intel_E5 12.5 2     7.5G   -      Yes    (mg)

    $ [lsfuser05@lsfuser05-ubuntu-two-mgmt-1 lsfadmin]$ bhosts -w
    HOST_NAME                        STATUS      JL/U MAX NJOBS RUN SSUSP USUSP RSV
    lsfuser05-ubuntu-two-mgmt-1 closed_Full -    0   0     0   0     0     0
    lsfuser05-ubuntu-two-mgmt-2 closed_Full -    0   0     0   0     0     0
    lsfuser05-ubuntu-two-mgmt-3 closed_Full -    0   0     0   0     0     0

    $ [lsfuser05@lsfuser05-ubuntu-two-mgmt-1 lsfadmin]$ lsload
    HOST_NAME            status r15s r1m r15m ut pg  ls it tmp swp mem
    lsfuser05-ubuntu-tes ok     0.0  0.0 0.0  0% 0.0 0  58 89G 0M  6.8G
    lsfuser05-ubuntu-tes ok     0.0  0.0 0.0  0% 0.0 0  58 89G 0M  6.8G
    lsfuser05-ubuntu-tes ok     0.0  0.4 0.1  4% 0.0 1  0  89G 0M  4.7G

    $ [lsfuser05@lsfuser05-ubuntu-two-mgmt-1 lsfadmin]$ lsclusters
    CLUSTER_NAME STATUS MASTER_HOST                  ADMIN    HOSTS SERVERS
    HPC-LSF-2    ok     lsfuser05-ubuntu-two-mgmmt-1 lsfadmin 3     3

    $ [lsfuser05@lsfuser05-ubuntu-two-mgmt-1 lsfadmin]$ bjobs
    No unfinished job found

    $ [lsfuser05@lsfuser05-ubuntu-two-mgmt-1 lsfadmin]$ lsid
    IBM Spectrum LSF Standard 10.1.0.14, Dec 18 2023
    Copyright International Business Machines Corp. 1992, 2016.
    US Government Users Restricted Rights - Use, duplication or disclosure restricted by GSA ADP Schedule Contract with IBM Corp.

    My cluster name is HPC-LSF-2
    My master name is lsfuser05-ubuntu-two-mgmt-1
    ```
    {: codeblock}

4.  Submit an LSF job as the LDAP user:

    ```text
    $ bsub -n 8 sleep 30
    ```
    {: codeblock}
