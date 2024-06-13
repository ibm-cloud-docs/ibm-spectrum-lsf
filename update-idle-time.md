---

copyright:
  years: 2021, 2022
lastupdated: "2022-09-20"

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

# Updating idle time before worker nodes are removed
{: #update-idle-time}

Complete the following steps to adjust the idle time for dynamic hosts that are returning to {{site.data.keyword.cloud}}. By default the value is set to 10 minutes.

1. Find the `lsf.conf` file on your management host. Typically, the file is located in the folder `$LSF_ENVDIR environment variable`. In a typical installation, it would be in the `/opt/ibm/lsf/conf/lsf.conf` folder.
2. Update the `LSB_RC_EXTERNAL_HOST_IDLE_TIME` property. See the following sample content for an example:

    ```
    #enable IDLE time to return the VMs

    LSB_RC_EXTERNAL_HOST_IDLE_TIME=10
    ```
    {: screen}

3. Run the following commands to make the changes:

    ```
    lsadmin reconfig
    ```
    {: pre}

    ```
    badmin mbdrestart
    ```
    {: pre}

