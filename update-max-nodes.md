---

copyright:
  years: 2021
lastupdated: "2021-09-24"

keywords: 

subcollection: ibm-spectrum-lsf

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:external: target="_blank" .external}
{:pre: .pre}
{:tip: .tip}
{:note .note}
{:important: .important}

# Updating maximum number of worker nodes
{: #update-max-worker-nodes-count}

Complete the following steps if you want to update the maximum number of worker nodes. 

1. Update the file `ibmcloudgen2_templates.json` in `$LSF_ENDIR/resource_connector/ibmcloudgen2/conf`. A typical HPC cluster that is installed on {{site.data.keyword.cloud}} would have this file in `/opt/ibm/lsf/conf/resource_connector/ibmcloudgen2/conf`.
2. Update the value for field `maxNumber` to the number of your choice. See the following sample code for an example:

    ```
    {
    "templates": [
    {
    "templateId": "<TemplateID>",
    "maxNumber": 200,
    "attributes": {
    "type": ["String", "X86_64"],
    "ncores": ["Numeric", "1"],
    "ncpus": ["Numeric", "1"],
    "mem": ["Numeric", "1024"],
    "icgen2host": ["Boolean", "1"]
    },
    "imageId": "<IMAGE_ID>",
    "subnetId": "<SUBNET_ID>",
    "vpcId": "<VPC_ID">,
    "vmType": "cx2-2x4",
    "securityGroupIds": ["<SECURITY_GROUP_ID>"],
    "sshkey_id": "<SSH_KEY_ID>",
    "region": "us-south",
    "zone": "us-south-2"
    }
    ]
    }  
    ```
    {: screen}

    Make sure that the value for `maxNumber` is within the range that your specific subnet can accommodate. You can check the available IPs in the subnet page in the {{site.data.keyword.cloud_notm}} console, or you can check by running the following command in the {{site.data.keyword.cloud_notm}} CLI: 

    ```
    ic is subnet <SUBNET_ID> --output JSON | jq .available_ipv4_address_count
    ```
    {: pre}

3. Run the following command to apply the changes of the file into the cluster:

    ```
    badmin reconfig
    ```
    {: pre}


