---

copyright:
  years: 2021, 2023, 2024
lastupdated: "2024-04-05"

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
{:ui: .ph data-hd-interface='ui'}
{:cli: .ph data-hd-interface='cli'}
{:api: .ph data-hd-interface='api'}
{:table: .aria-labeledby="caption"}

# Creating a workspace
{: #creating-workspace}

With {{site.data.keyword.bplong}} workspaces, you can manage the Terraform-based templates to create and manage {{site.data.keyword.cloud_notm}} resources. A workspace allows you to view and update configuration properties, view the list of resources that are provisioned through the workspace, and clean up resources if and when needed.
{: shortdesc}

## Creating a workspace using the UI
{: #create-workspace-ui}
{: ui}

1. Log in to the [{{site.data.keyword.cloud_notm}} catalog](https://cloud.ibm.com/catalog){: external} by using your credentials.
2. In the Software section, select Compute and then select the **{{site.data.keyword.spectrum_full_notm}}** tile. 
3. In the Configure your workspace section:
    * Specify the Name for your {{site.data.keyword.bpshort}} workspace
    * Select a Resource group.
    * Define any Tags that you want to associate with the resources provisioned through the offering. The tags can later be used to query the resources in the {{site.data.keyword.cloud_notm}} console.
4. In the _Set the deployment values_ section, specify the values for the required properties: `api_key`, `ibm_customer_number`, `remote_allowed_ips`, `ssh_key_name`, and `zone`.
5. Expand the _Parameters with default values_ section, and review it to determine whether you need to override any of the default values provided for the configuration properties.
6. Review and accept the **{{site.data.keyword.spectrum_full_notm}}** license terms and conditions in the order summary.
7. Click Install. The {{site.data.keyword.bpshort}} workspace is created with the name that you specified. You can see the list of workspaces in _View the existing installations_. If the workspace creation is successful, the _Apply Plan_ action is started to trigger the deployment of the respective {{site.data.keyword.vpc_short}} resources in your {{site.data.keyword.cloud_notm}} account that are linked with the `api_key`. 
8. You can also review the status of your deployment process by identifying the workspace name in the _View the existing installations_ section. When you click a record in _View the existing installations_ section, you are taken to the {{site.data.keyword.bpshort}} workspace view. 

## Next steps
{: #next-steps-create-ui}
{: ui}

After you have successfully created a workspace, you can begin [Generating a plan](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-generate-plan&interface=ui) to validate all of the configuration properties.

## Before you begin
{: #before-you-begin-creating-cli}
{: cli}

Before you get started, make sure that you have completed the prerequisites found in [Setting up the {{site.data.keyword.bplong_notm}} CLI](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-setting-up-cli). 

## Creating a workspace using the CLI
{: #create-workspace-cli}
{: cli}

The first step when using {{site.data.keyword.bpshort}} is to create a workspace with the specific configuration parameters defined in the corresponding Terraform source code.

Use the following CLI command to create a workspace with your `config.json` file. Make sure that the `config.json` file exists in the directory where you run the command.

```
ibmcloud schematics workspace new -f hpc_workspace_config.json --github-token GITHUB_TOKEN
```
{: pre}

The `--github-token` parameter is optional and only needed if you are using a private Git repository. If you are using the [{{site.data.keyword.cloud_notm}} public GitHub repository](https://github.com/IBM-Cloud/hpc-cluster-lsf){: external}, you do not need to provide it.
{: note}

### Listing available workspaces
{: #list-available-workspaces-cli}
{: cli}

You can list the workspaces in your account by using the following command:

```
ibmcloud schematics workspace list
```
{: pre}

Example response with workspace details:

```
Name                ID                                           Description           Status         Frozen
spectrum-lsf-test   us-east.workspace.bcc-lsf-test.7cbc3f6b      Sample workspace      INACTIVE       False
```
{: screen}

### Retrieving workspace details
{: #retrieve-workspace-details-cli}
{: cli}

You can retrieve the details of an existing workspace, including the values of all input variables, by running the following command:

```
ibmcloud schematics workspace get --id WORKSPACE_ID [--output OUTPUT][--json]
```
{: pre}

### Updating a workspace
{: #update-workspace-cli}
{: cli}

You can update the details for an existing workspace, such as the workspace name, variables, or source control URL by running the following command:

```
ibmcloud schematics workspace update --id WORKSPACE_ID --file FILE_NAME [--github-token GITHUB_TOKEN]
```
{: pre}

To provision or modify {{site.data.keyword.cloud_notm}} resources, you can run the command `ibmcloud schematics plan` command. For more information, see the [{{site.data.keyword.bplong_notm}} CLI](/docs/schematics?topic=schematics-schematics-cli-reference) reference.

## Next steps
{: #next-steps-create-cli}
{: cli}

After you have successfully created a workspace, you can begin [Generating a plan](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-generate-plan&interface=cli) to validate all the configuration properties. 

## Before you begin
{: #before-you-begin-creating-api}
{: api}

Before you get started, make sure that you have completed the prerequisites found in [Setting up the {{site.data.keyword.bplong_notm}} API](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-setting-up-api).

## Creating a workspace using the API
{: #create-workspace-api}
{: api}

1. To create a workspace by using the {{site.data.keyword.bplong_notm}} Python APIs, create a Python file and provide a name of your choice, for example, `schematics_create_workspace.py`.
2. Copy and paste the [Create a Schematics workspace using Python API](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-creating-workspace&interface=api#example-request-create) example request to your Python file.
3. Change the following parameters as part of the request:
    * Replace your {{site.data.keyword.cloud_notm}} API key to the `authenticator = IAMAuthenticator('<ibm-api-key>')` variable.
    * Change the API endpoint to the endpoint mentioned in [API endpoints](https://cloud.ibm.com/apidocs/schematics?code=python#api-endpoints){: external} according to the location that you want your {{site.data.keyword.bpshort}} workspace to reside, for example, `schematics_service.set_service_url('https://us.schematics.cloud.ibm.com')`.
    * Provide environment values that you want to have in the form of a key and value pair, such as [{ 'Environment': 'hpc-dev-cluster' }]
    * Change the folder location variable `template_source_data_request_model['folder']` to the folder name inside your GitHub repository where your Terraform files reside. If the Terraform files exist in the base location of your repository, then set this variable to `template_source_data_request_model['folder'] = ' '`; otherwise, mention the folder name in the `template_source_data_request_model['folder']` variable.
    * Change the `template_source_data_request_model['type']` variable to the Terraform version that you are using to create {{site.data.keyword.cloud_notm}} resources such as `terraform_v1.5`. Ensure that your Terraform templates are compatible with the version that you are mentioning in this field.
    * Provide your GitHub or GitLab Repository HTTPS URL where your Terraform files reside in the `template_repo_request_model['url']` variable. If you are using the [public repository](https://github.com/IBM-Cloud/hpc-cluster-lsf){: external} that is provided by {{site.data.keyword.cloud_notm}}, then set this variable as `template_repo_request_model['url'] = 'https://github.com/IBM-Cloud/hpc-cluster-lsf'`; otherwise, set it to the private repository you are using. 
4. Inside the `schematics_service.create_workspace` function, provide the following parameters:
    * Provide an optional description.
    * Provide a name to identify your {{site.data.keyword.bpshort}} workspace, for example `terraform-dev-workspace`.
    * Change the `type` parameter to the Terraform version that you are using to create {{site.data.keyword.cloud_notm}} resources, for example, `terraform_v1.5`.
    * Change the location to a region where your {{site.data.keyword.bpshort}} workspace needs to be created, for example, `us-south`.
    * Change the resource group to the resource group where your resources should be grouped, for example, `Default` for a default resource group.
    * If you are using a private GitHub repository, provide your personal GitHub access token that you set up in [Setting up the {{site.data.keyword.bplong_notm}}](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-setting-up-api) prerequisites in the `x_github_token= "<github-api-token>"` parameter. If you are using the public repository that is provided by {{site.data.keyword.cloud_notm}}, you do not need to specify this parameter.
    * **Optional**: Provide the tags if you want to filter resources by using the tag.
5. Run the Python script by using `python3 <python-file-name>` to create a {{site.data.keyword.bpshort}} workspace in the {{site.data.keyword.cloud_notm}}.
6. You get a successful response if the parameters passed as part of the request are valid and you should be able to see the {{site.data.keyword.bpshort}} workspace that you created in the {{site.data.keyword.cloud_notm}} console. If you don’t get a successful response, the error response contains the errors that you need to resolve. Resolve those errors and run the script until you are able to get a valid response and create a workspace.
7. Optional: If you want to add one of the variables such as `base_name` with the value `lsf-test` when you create a workspace, provide the values in the following parameters:
    * `workspace_variable_request_model = {}`
    * `workspace_variable_request_model['name'] = 'base_name'` 
    * `workspace_variable_request_model['value'] = 'lsf-test'`
8. If you want to update multiple values at the same time, follow the steps for [Updating variables with {{site.data.keyword.bpshort}} API](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-update-variables).

### Example Python request
{: #example-request-create}
{: api}

The Python request is a generic example. Versioning and deployment values are subject to change due to frequent code updates. 
{: note}

```python

# Create a Schematics workspace using Python API

import json, logging

from ibm_cloud_sdk_core.authenticators import IAMAuthenticator
from ibm_schematics.schematics_v1 import SchematicsV1

logging.basicConfig()
logging.root.setLevel(logging.NOTSET)
logging.basicConfig(level=logging.NOTSET)

authenticator = IAMAuthenticator('')

schematics_service = SchematicsV1(authenticator = authenticator)

schematics_service.set_service_url('https://us.schematics.cloud.ibm.com')

template_source_data_request_model = {}
template_source_data_request_model['env_values'] = [
{
"name": "TF_CLI_ARGS_plan",
"value": "-parallelism=250",
},
{
"name": "TF_CLI_ARGS_apply",
"value": "-parallelism=250"
}
]
template_source_data_request_model['folder'] = ''
template_source_data_request_model['type'] = 'terraform_v1.5'
template_source_data_request_model['variablestore'] = [
{
"name": "ssh_key_name",
"value": "lsf-bcc-key",
},
{
"name": "ibm_customer_number",
"value": ""
}]

template_repo_request_model = {}
template_repo_request_model['url'] = 'https://github.com/IBM-Cloud/hpc-cluster-lsf'
template_repo_request_model['branch'] = ""

logging.info("Started Creating Schematic Workspace")

workspace_response = schematics_service.create_workspace(
description="HPC Cluster schematic workspace using API",
name="Sample Schematic API workspace",
template_data=[template_source_data_request_model],
template_repo=template_repo_request_model,
type=['terraform_v1.5'],
location="us-south",
resource_group="Default",
tags=[""]
).get_result()

print(json.dumps(workspace_response, indent = 2))
```
{: codeblock}

### Example Python response
{: #example-response-create}
{: api}

The Python response is a generic example. Versioning and deployment values are subject to change due to frequent code updates.
{: note}

```python
INFO:root:Started Creating Schematic Workspace
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): iam.cloud.ibm.com:443
DEBUG:urllib3.connectionpool:https://iam.cloud.ibm.com:443 "POST /identity/token HTTP/1.1" 200 1086
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): us.schematics.cloud.ibm.com:443
DEBUG:urllib3.connectionpool:https://us.schematics.cloud.ibm.com:443 "POST /v1/workspaces HTTP/1.1" 201 1974
INFO:root:Completed Creating Schematic Workspace
{
"id": "us-south.workspace.Sample-Schematic-API-workspace.341cedd5",
"name": "Sample Schematic API workspace",
"crn": "crn:v1:bluemix:public:schematics:us-south:a/77efe1030c00b5c89cfd08648d3480bf:6f457fa4-4c59-4a8d-be69-483f16f8f200:workspace:us-south.workspace.Sample-Schematic-API-workspace.341cedd5",
"type": [
"terraform_v1.5"
],
"description": "HPC Cluster schematic workspace using API",
"resource_group": "Default",
"location": "us-south",
"tags": [
""
],
"created_at": "2021-09-15T13:01:19.944980389Z",
"created_by": "xxxxxx",
"status": "DRAFT",
"failure_reason": "",
"workspace_status_msg": {
"status_code": "",
"status_msg": ""
},
"workspace_status": {
"frozen": false,
"locked": false
},
"template_repo": {
"url": "https://github.com/IBM-Cloud/hpc-cluster-lsf",
"commit_id": "",
"full_url": "https://github.com/IBM-Cloud/hpc-cluster-lsf",
"has_uploadedgitrepotar": false
},
"template_data": [
{
"id": "3e68db7f-0bea-4d",
"folder": "",
"compact": false,
"type": "terraform_v1.5",
"values_url": "https://us.schematics.cloud.ibm.com/v1/workspaces/us-south.workspace.Sample-Schematic-API-workspace.341cedd5/template_data/3e68db7f-0bea-4d/values",
"values": "",
"variablestore": [
{
"name": "ssh_key_name",
"secure": false,
"value": "lsf-bcc-key",
"type": "",
"description": ""
},
{
"name": "ibm_customer_number",
"secure": true,
"value": "",
"type": "",
"description": ""
}
],
"has_githubtoken": false
}
],
"runtime_data": [
{
"id": "3e68db7f-0bea-4d",
"engine_name": "terraform",
"engine_version": "v0.14.11",
"state_store_url": "https://us.schematics.cloud.ibm.com/v1/workspaces/us-south.workspace.Sample-Schematic-API-workspace.341cedd5/runtime_data/3e68db7f-0bea-4d/state_store",
"log_store_url": "https://us.schematics.cloud.ibm.com/v1/workspaces/us-south.workspace.Sample-Schematic-API-workspace.341cedd5/runtime_data/3e68db7f-0bea-4d/log_store"
}
],
"shared_data": {
"resource_group_id": ""
},
"applied_shareddata_ids": null,
"updated_at": "0001-01-01T00:00:00Z",
"last_health_check_at": "0001-01-01T00:00:00Z",
"cart_id": ""
}
 INFO:root:Completed Creating Schematic Workspace
```
{: screen}

## Next steps
{: #next-steps-create-api}
{: api}

After you have successfully created a workspace, you can begin [Generating a plan](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-generate-plan&interface=api) to validate all the configuration properties. 
