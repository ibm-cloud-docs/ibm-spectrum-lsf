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
{:table: .aria-labeledby="caption"}

# Setting up the IBM Cloud Schematics CLI
{: #setting-up-cli}

Before you begin using the {{site.data.keyword.bplong}} CLI to deploy {{site.data.keyword.spectrum_full_notm}}, review and complete the following prerequisites: 

1. Install the [{{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cli-install-ibmcloud-cli).
2. Log in to the {{site.data.keyword.cloud_notm}} CLI with your IBMid. If you have multiple accounts, you are prompted to select which account to use. If you do not specify a region with the `-r` flag, you must also select a region.

  ```
  ibmcloud login
  ```
  {: pre}

  If your credentials are rejected, you might be using a federated ID. To log in with a federated ID, use the `--sso` flag. For more information, see [Logging in with a federated ID](/docs/account?topic=account-federated_id).
  {: tip}

3. Install and set up the [{{site.data.keyword.bplong_notm}} CLI plug-in](/docs/schematics?topic=schematics-setup-cli#install-schematics-plugin).
4. Make sure to generate your {{site.data.keyword.cloud_notm}} API key. For more information, see [Managing user API keys](/docs/account?topic=account-userapikey).
5. Create the `config.json` file that represents the configuration parameters that you would like to use to build the {{site.data.keyword.spectrum_short}} cluster. To learn more about all the properties, see [Deployment values](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-deployment-values). You can view a sample configuration file [here](https://github.com/IBM-Cloud/hpc-cluster-lsf/blob/main/sample/configs/hpc_workspace_config.json){: external}. For more information about the sample JSON file used by the {{site.data.keyword.bpshort}} CLI, see [{{site.data.keyword.bplong_notm}} CLI](/docs/ibm-spectrum-lsf?topic=schematics-schematics-cli-reference). If you need to override any default parameters, you can redefine them in `variablestore`.
6. Update the mandatory properties for `api_key`, `ssh_key_name`, and `lsf_license_confirmation` based on your specific setup. 
7. Review and update any optional properties in the `config.json` file to fit your specific {{site.data.keyword.spectrum_short}} cluster instance. 

## Next steps
{: #next-steps-cli}

After you've reviewed and completed the prerequisites for setting up the CLI, you are ready to [Create a workspace](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-creating-workspace#before-you-begin-creating-cli).
