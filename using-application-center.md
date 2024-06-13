---

copyright:
  years: 2023
lastupdated: "2023-03-16"

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

# Configuring the LSF Application Center
{: #configuring-lsf-application-center}

With the {{site.data.keyword.spectrum_full_notm}} Application Center, you have a flexible, easy-to-use interface for both cluster users and administrators. Available as an add-on module to {{site.data.keyword.spectrum_full_notm}}, LSF Application Center lets you interact with intuitive, self-documenting standardized interfaces. For more information, see the [{{site.data.keyword.spectrum_full_notm}} Application Center product documentation](https://www.ibm.com/docs/en/slac/10.2.0){: external}.
{: shortdesc}

## Before you begin
{: #before-you-begin}

Before you begin, make sure to complete the steps in [Getting started with {{site.data.keyword.spectrum_full_notm}}](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-getting-started-tutorial).

## Configuring LSF Application Center
{: #configuring-application-center}

To configure and use the LSF Application Center, the `enable_app_center` deployment value must be set to "true". The remaining LSF Application Center ("app_center_xx") parameters should be configured to deploy the wanted LSF Application Center cluster configuration. Use `lsfadmin` as the default user for the LSF Application Center.

See the following example deployment values:

* `enable_app_center` = true
* `app_center_gui_pwd` = "Admin@123"
* `app_center_db_pwd` = "Admin@123"

## Checking LSF Application Center status
{: #checking-lsf-application-center-status}

After the cluster is created, wait 5 - 10 minutes to complete the installation of the LSF Application Center, which is initiated by the cloud-init user in the backend.

1. Connect to an LSF management node through SSH. Details are available in the {{site.data.keyword.bpshort}} log output with the following “application_center”.

    ```
    ssh -L 8443:localhost:8443 -J root@149.81.237.236 lsfadmin@10.243.128.41
    ```
    {: codeblock}

2. Verify the LSF Application Center installation by using the LSF Application Center ‘pmcadmin‘ command:

    ```
    sudo -i

    pmcadmin list
    ```
    {: codeblock}

    Example output:

    ```
    [lsfadmin@icgen2host-10-241-128-23 ~]$ sudo -i
    [root@icgen2host-10-241-128-23 ~]# pmcadmin list
    SERVICE        STATUS         PID            PORT           HOST_NAME      
    WEBGUI         STARTED        8966           8443           icgen2host-10-241-128-23
    PNC            STARTED        8823           8081           icgen2host-10-241-128-23
    [root@icgen2host-10-241-128-23 ~]# 
    ```
    {: codeblock}

In this example, the LSF Application Center installation is set up on the Spectrum LSF management node, `<cluster_prefix>-management-host-0`. If this management node fails, then your LSF Application Center will be down.
{: note}

