---

copyright:
  years: 2023
lastupdated: "2023-03-17"

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

# Accessing the GUI
{: #accessing-lsf-gui}

After the cluster setup is complete, you can monitor the resources and status of the service directly from the {{site.data.keyword.spectrum_full_notm}} Application Center GUI. For more information about the GUI, see the [LSF Application Center Web Services product documentation](https://www.ibm.com/docs/en/slac/10.2.0?topic=lsf-application-center-web-services){: external}.
{: shortdesc}

## Before you begin
{: #before-you-begin}

Before you access the LSF Application Center GUI, review the following considerations and requirements:

* Initial setup must be done from your local system.
* Access the GUI with the same SSH key that was provided for cluster creation.
* Recommended using the Safari browser to access the GUI.
* If you encounter a delay in loading or accessing the GUI, clear the browsers cache.
* You can open Application Center GUIs on the 8443 port. 

## Gathering IP addresses
{: #gathering-ip-addresses}

To access the GUI, you must collect the IP addresses of the compute nodes and the floating IP address that is attached to the login node.

1. In the {{site.data.keyword.cloud_notm}} console, go to Schematics > Workspaces, and choose your workspace.
2. On the workspace page, click the _Resources_ tab. In the list of Terraform resources, click the `management_host` resource link, which opens a new window with a list of the virtual server instances that were provisioned.
3. On the Virtual server instances for VPC page, locate the IP address for `<cluster_prefix>-management-host-0`, which is the management IP address that you need.
4. In addition to that IP address, pick up the floating IP address that is attached to the login node.

## Accessing the LSF Application Center
{: #accessing-lsf-application-center}

Complete the following steps to access the LSF Application Center:

1. Open a new command-line terminal.

2. Run the following command to access the LSF Application Center GUI:

    ```
    #ssh -L 8443:localhost:8443 -J root@{FLOATING_IP_ADDRESS} lsfadmin@{MANAGEMENT_NODE_IP_ADDRESS}
    ```
    {: pre}

    where `MANAGEMENT_NODE_IP_ADDRESS` needs to be replaced with the management node IP address that is associated with `<cluster_prefix>-management-host-0` and `FLOATING_IP_ADDRESS` needs to be replaced with the login node floating IP address. To find the management and login node IPs, see the instructions for [Gathering IP addresses](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-accessing-lsf-gui#gathering-ip-addresses).

3. Open a browser on your local system and run https://localhost:8443.
4. To access the LSF Application Center GUI, enter the default user as `lsfadmin` and enter the password that you configured when you created your workspace.
