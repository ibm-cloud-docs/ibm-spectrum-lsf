---

copyright:
  years: 2021, 2023
lastupdated: "2023-04-21"

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

# Setting up the IBM Cloud Schematics API
{: #setting-up-api}

Before you begin using the {{site.data.keyword.bplong}} API to deploy {{site.data.keyword.spectrum_full_notm}}, review and complete the following prerequisites: 

1. Make sure that you have access to an {{site.data.keyword.cloud_notm}} account and that you generated an {{site.data.keyword.cloud_notm}} API key to authenticate to {{site.data.keyword.cloud_notm}}.
2. If the GitHub repository that hosts the Terraform code is not public, generate a [personal GitHub access token](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token){: external} for authentication. If you are using the [public {{site.data.keyword.cloud_notm}} GitHub repository](https://github.com/IBM-Cloud/hpc-cluster-lsf){: external}, you do not need to complete this task.
3. Install the [{{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cli-install-ibmcloud-cli) and login to the {{site.data.keyword.cloud_notm}} account through the {{site.data.keyword.cloud_notm}} CLI.
5. Install Python 3 or a newer version.
6. Follow the [{{site.data.keyword.bplong_notm}} SDK for Python instructions](https://cloud.ibm.com/apidocs/schematics?code=python#introduction){: external} to set up and install the {{site.data.keyword.bplong_notm}} API by using `pip`.
8. Install all of the respective Python application dependencies that your Python code will be using, such as `logging`, `os`, `yaml`, and `json`, which are required when you use `pip3` to run the application.

## Next steps
{: #next-steps-api}

After you've reviewed and completed the prerequisites for setting up the API, you are ready to [Create a workspace](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-creating-workspace&interface=api).
