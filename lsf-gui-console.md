---

copyright:
  years: 2022
lastupdated: "2022-10-26"

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
{:step: data-tutorial-type='step'}
{:table: .aria-labeledby="caption"}

# Accessing the Spectrum LSF GUI console
{: #accessing-lsf-gui-console}

## Configuring an SSH tunnel to the cluster to allow access
{: #configuring-an-SSH-tunnel-to-the-cluster}

Before you can access the {{site.data.keyword.spectrum_short}} cluster management console, you need to configure an SSH tunnel to the cluster to allow access to the management console through a local browser instance. You can do this by configuring port forwarding on your local browser host.
{: shortdesc}

1. Run the following command to create the SSH tunnel:

    ```
    ssh -L 8443:localhost:8443 -J root@{LOGIN_NODE_PUBLIC_IP} lsfadmin@{MANAGEMENT_NODE_PRIVATE_IP}
    ```
    {: codeblock}

    where ``LOGIN_NODE_PUBLIC_IP`` needs to be replaced with the respective IP address of the login node and ``MANAGEMENT_NODE_PRIVATE_IP`` needs to be replaced with the IP address of the management node that is running the **WEBGUI** service.

2. After you start the SSH session and open the designated local host site on your local web browser, it connects to the {{site.data.keyword.spectrum_short}} management node.

## Further considerations
{: #further-considerations}

1. You get an SSL self-signed certificate warning with your browser the first time that you access this URL. 

2. This SSH command logs you into the primary management host and initializes a {{site.data.keyword.spectrum_short}} command-line environment.
