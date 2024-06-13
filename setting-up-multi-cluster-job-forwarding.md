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

# Setting up multi-cluster and job forwarding using Spectrum LSF
{: #set-up-multi-cluster-job-forwarding}

The following example is a guide on how to set up the multi-cluster and job forwarding by using {{site.data.keyword.spectrum_short}}. This example explains common situations where a cluster is on-premises and another is in the cloud.

This example assumes that the on-premises cluster labeled with "OnPremiseCluster" uses a subnet `192.168.0.0/24` and its management host uses `192.168.0.4` (on-premise-management). The cloud cluster labeled with "HPCCluster" uses a subnet `10.244.128.0/24` and its management host uses `10.244.128.37` (icgen2host-10-244-128-37). Both of the configuration directories are in `/opt/ibm/lsf/conf`, but you can change the directory depending on your cluster configuration.

1. Ensure that the MTU size can enable packets to go through the internet. If you have management host candidates at your cluster, keep a large MTU for the performance and functions of management host communications. The management host and every candidate need to be configured like the following:

    ```
    $ sudo ip link set mtu 1500 dev eth0
    $ sudo ip route add {management host candidate/management host IP} dev eth0 mtu 9000 
    ```
    {: codeblock}

2. The following is an example of the `/etc/hosts` file for the cloud cluster. You need to make sure that the host names for the LSF management hosts are DNS-resolveable.

    ```
    ...
    10.244.128.61 icgen2host-10-244-128-61
    10.244.128.62 icgen2host-10-244-128-62
    10.244.128.63 icgen2host-10-244-128-63

    192.168.0.4 on-premise-management   # added
    ```
    {: codeblock}

    For the on-premises `/etc/hosts` file, make sure to add the information about the management host in the cloud cluster:

    ```
    10.244.128.37 icgen2host-10-244-128-37 #added
    ```
    {: codeblock}

3. Both clusters need to recognize each other, so you need to modify `/opt/ibm/lsf/conf/lsf.shared`. This configuration file should be identical in both clusters.

    ```
    ...
    Begin Cluster
    ClusterName        Servers                       # Keyword             # modified
    HPCCluster         (icgen2host-10-244-128-37)    # modified
    OnPremiseCluster   (on-premise-management)       # modified
    End Cluster
    ...
    ```
    {: codeblock}

4. The two clusters are configured to have different `lsb.queues` files. In the cloud cluster, you need to append the following lines to `/opt/ibm/lsf/conf/lsbatch/HPCCluster/configdir/lsb.queues` to register a receive queue:

    ```
    ...
    Begin Queue
    QUEUE_NAME=recv_q
    RCVJOBS_FROM=OnPremiseCluster
    PRIORITY=30
    NICE=20
    RC_HOSTS=all
    End Queue
    ```
    {: codeblock}

    The on-premises cluster is configured to have a send queue at `/opt/ibm/lsf/conf/lsbatch/OnPremiseCluster/configdir/lsb.queues`:

    ```
    ...
    Begin Queue
    QUEUE_NAME=send_q
    SNDJOBS_TO=recv_q@HPCCluster
    PRIORITY=30
    NICE=20
    End Queue
    ```
    {: codeblock}

5. Restart both clusters by running the following command:

    ```
    $ lsfrestart
    ```
    {: codeblock}

6. After you restart both clusters, you can now forward jobs from on-premises to the cloud. At your on-premises cluster, you can test the following job:

    ```
    $ bsub -q send_q sh -c 'echo $HOSTNAME > /home/lsfadmin/shared/mc-test.txt'
    ```
    {: codeblock}

    You can see that the job comes to the HPCCluster at `10.244.128.37`.

    ```
    $ bjobs -aw
    ```
    {: codeblock}

    ```
    JOBID   USER      STAT   QUEUE    FROM_HOST   EXEC_HOST   JOB_NAME   SUBMIT_TIME
    304     lsfadmin  DONE   recv_q   on-premise-manangement@OnPremiseCluster:911 icgen2host-10-244-128-39 sh -c 'echo $HOSTNAME > /home/lsfadmin/shared/mc-test.txt' Jun 17 02:27
    ```
    {: screen}

## Additional resources
{: #additional-resources-information}

For more information, check out the following {{site.data.keyword.spectrum_full_notm}} documentation:

* [{{site.data.keyword.spectrum_full_notm}} multi-cluster capability](https://www.ibm.com/docs/en/spectrum-lsf/10.1.0?topic=lsf-multicluster-capability){: external}
* [Set common ports](https://www.ibm.com/docs/en/spectrum-lsf/10.1.0?topic=overview-set-common-ports){: external}
* [Enable queues for the LSF multi-cluster capability](https://www.ibm.com/docs/en/spectrum-lsf/10.1.0?topic=queues-enable-multicluster){: external}
* [Using the {{site.data.keyword.spectrum_full_notm}} Resource Connector](https://www.ibm.com/docs/en/spectrum-lsf/10.1.0?topic=lsf-resource-connnector){: external}
* [{{site.data.keyword.spectrum_full_notm}} Data Manager](https://www.ibm.com/docs/en/spectrum-lsf/10.1.0?topic=lsf-data-manager){: external}

