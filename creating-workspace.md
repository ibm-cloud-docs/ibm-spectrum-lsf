---

copyright:
  years: 2021
lastupdated: "2021-06-24"

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

1. Log in to the [{{site.data.keyword.cloud_notm}} catalog](https://cloud.ibm.com/catalog){: external} by using your unique credentials.
2. In the _Software_ section, select **Compute** and then select the **{{site.data.keyword.spectrum_full_notm}}** tile. 
3. In the _Configure your workspace_ section:
    * Specify the **Name** for your {{site.data.keyword.bpshort}} workspace
    * Select a **Resource group**
    * Define any **Tags** that you want to associate with the resources provisioned through the offering. The tags can later be used to query the resources in the {{site.data.keyword.cloud_notm}} console.
4. In the _Set the deployment values_ section, specify the values for the three required properties: `api_key`, `ssh_key_name`, and `lsf_license_confirmation`.
5. Expand the _Parameters with default values_ section, and review it to determine whether you need to override any of the default values provided for the configuration properties.
6. Review and accept the **{{site.data.keyword.spectrum_full_notm}}** license terms and conditions in the order summary.
7. Click **Install**. The {{site.data.keyword.bpshort}} workspace is created with the name you specified. You can see the list of workspaces in _View the existing installations_. If the workspace creation is successful, the _Apply Plan_ action is started to trigger the deployment of the respective {{site.data.keyword.vpc_short}} resources in your {{site.data.keyword.cloud_notm}} account that are linked with the `api_key`. 
8. You can also review the status of your deployment process by identifying the workspace name in the _View the existing installations_ section. When you click a record in _View the existing installations_ section, you are taken to the {{site.data.keyword.bpshort}} workspace view. 

## Next steps
{: #next-steps-create-ui}
{: ui}

After you've successfully created a workspace, you can begin [Generating a plan](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-generate-plan#generate-plan-ui) to validate all of the configuration properties.

## Before you begin
{: #before-you-begin-creating-cli}
{: cli}

Before you get started, make sure that you've completed the prerequisites found in [Setting up the {{site.data.keyword.bplong_notm}} CLI](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-setting-up-cli). 

## Creating a workspace using the CLI
{: #create-workspace-cli}
{: cli}

The first step when using {{site.data.keyword.bpshort}} is to create a workspace with the specific configuration parameters defined in the corresponding Terraform source code.

Use the following CLI command to create a workspace with your `config.json` file. Make sure that the `config.json` file exists in the directory where you run the command.

```
ibmcloud schematics workspace new -f hpc_workspace_config.json --github-token GITHUB_TOKEN
```
{: pre}

The `--github-token` parameter is optional and only needed if you are using a private git repository. If you are using the [{{site.data.keyword.cloud_notm}} public GitHub repository](https://github.com/IBM-Cloud/hpc-cluster-lsf){: external}, you do not need to provide it.
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

To provision or modify {{site.data.keyword.cloud_notm}} resources, you can run the command `ibmcloud schematics plan` command. For more information, see the [{{site.data.keyword.bplong_notm}} CLI](/docs/ibm-spectrum-lsf?topic=schematics-schematics-cli-reference#schematics-plan) reference.

## Next steps
{: #next-steps-create-cli}
{: cli}

After you've successfully created a workspace, you can begin [Generating a plan](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-generate-plan#generate-plan-cli) to validate all of the configuration properties. 

## Before you begin
{: #before-you-begin-creating-api}
{: api}

Before you get started, make sure that you've completed the prerequisites found in [Setting up the {{site.data.keyword.bplong_notm}} API](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-setting-up-api).

## Creating a workspace using the API
{: #create-workspace-api}
{: api}

1. To create a workspace by using the {{site.data.keyword.bplong_notm}} Python APIs, create a Python file and provide a name of your choice, for example, `schematics_create_workspace.py`.
2. Copy and paste the [Create a Schematics workspace using Python API](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-creating-workspace#example-request-create) example request to your Python file.
3. Change the following parameters as part of the request:
    * Replace your {{site.data.keyword.cloud_notm}} API key to the `authenticator = IAMAuthenticator('<ibm-api-key>')` variable.
    * Change the API endpoint to the endpoint mentioned in [API endpoints](https://cloud.ibm.com/apidocs/schematics?code=python#api-endpoints){: external} according to the location that you want your {{site.data.keyword.bpshort}} workspace to reside, for example, `schematics_service.set_service_url('https://us.schematics.cloud.ibm.com')`.
    * Provide environment values that you want to have in the form of a key and value pair, such as [{ 'Environment': 'hpc-dev-cluster' }]
    * Change the folder location variable `template_source_data_request_model['folder']` to the folder name inside your GitHub repository where your Terraform files reside. If the Terraform files exist in the base location of your repository, then set this variable to `template_source_data_request_model['folder'] = ' '`; otherwise, mention the folder name in the `template_source_data_request_model['folder']` variable.
    * Change the `template_source_data_request_model['type']` variable to the Terraform version that you are using to create {{site.data.keyword.cloud_notm}} resources such as `terraform_v0.13`. Ensure that your Terraform templates are compatible with the version that you are mentioning in this field.
    * Provide your GitHub or GitLab Repository HTTPS URL where your Terraform files reside in the `template_repo_request_model['url']` variable. If you are using the [public repository](https://github.com/IBM-Cloud/hpc-cluster-lsf){: external} that is provided by {{site.data.keyword.cloud_notm}}, then set this variable as `template_repo_request_model['url'] = 'https://github.com/IBM-Cloud/hpc-cluster-lsf'`; otherwise, set it to the private repository you are using. 
4. Inside the `schematics_service.create_workspace` function, provide the following parameters:
    * Provide an optional description.
    * Provide a name to identify your {{site.data.keyword.bpshort}} workspace, for example, `terraform-dev-workspace`.
    * Change the `type` parameter to the Terraform version that you are using to create {{site.data.keyword.cloud_notm}} resources, for example, `terraform_v0.13`.
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

```python

# Create a Schematics workspace using Python API

import json, logging

from ibm_cloud_sdk_core.authenticators import IAMAuthenticator
from ibm_schematics.schematics_v1 import SchematicsV1

logging.basicConfig()
logging.root.setLevel(logging.NOTSET)
logging.basicConfig(level=logging.NOTSET)

authenticator = IAMAuthenticator('<ibmcloud-api-key>')

schematics_service = SchematicsV1(authenticator = authenticator)

schematics_service.set_service_url('https://us.schematics.cloud.ibm.com')

workspace_variable_request_model = {}
workspace_variable_request_model['name'] = 'variable_name1'
workspace_variable_request_model['value'] = 'variable_value1'

template_source_data_request_model = {}
template_source_data_request_model['env_values'] = [{ 'Environment': 'hpc-dev' }]
template_source_data_request_model['folder'] = ''
template_source_data_request_model['type'] = 'terraform_v0.13'
template_source_data_request_model['variablestore'] = [workspace_variable_request_model]

template_repo_request_model = {}
template_repo_request_model['url'] = '<GitHub repo https URL>'

logging.info("Started Creating Schematic Workspace")

workspace_response = schematics_service.create_workspace(
    description="<optional workspace description>", 
    name="<workspace-name>",
    template_data=[template_source_data_request_model],
    template_repo=template_repo_request_model,
    type=['terraform_v0.13'],
    location="us-south",
    resource_group="Default",
    tags=["<optional tags>"]
).get_result()

print(json.dumps(workspace_response, indent = 2))

logging.info("Completed Creating Schematic Workspace") 
```
{: codeblock}

### Example Python response
{: #example-response-create}
{: api}

The following Python response is a generic example for reference.
{: note}

```python
INFO:root:Started Creating Schematic Workspace
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): iam.cloud.ibm.com:443
DEBUG:urllib3.connectionpool:https://iam.cloud.ibm.com:443 "POST /identity/token HTTP/1.1" 200 986
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): us.schematics.cloud.ibm.com:443
DEBUG:urllib3.connectionpool:https://us.schematics.cloud.ibm.com:443 "POST /v1/workspaces HTTP/1.1" 201 1904
{
  "id": "myworkspace-123456",
  "name": "myworkspace",
  "type": [
    "terraform_v0.13"
  ],
  "description": "This is a workspace created with the API",
  "resource_group": "aa11bb22aa11bb22",
  "location": "us-south",
  "tags": [],
  "created_at": "2019-11-06T16:19:32.352418401Z",
  "created_by": "user@company.com",
  "status": "ACTIVE",
  "workspace_status_msg": {
    "status_code": "",
    "status_msg": ""
  },
  "workspace_status": {
    "frozen": false,
    "locked": false
  },
  "template_repo": {
    "url": "https://github.com/myorg/myrepo"
  },
  "template_data": [
    {
      "id": "a123456",
      "folder": ".",
      "type": "terraform_v0.13",
      "values_url": "https://schematics.cloud.ibm.com/v1/workspaces/myworkspace-123456/template_data/a123456/values",
      "values": "",
      "values_metadata": [
        {
          "name": "variable1",
          "type": "string"
        },
        {
          "name": "variable2",
          "type": "string"
        }
      ],
      "variablestore": [
        {
          "name": "variable1",
          "secure": false,
          "value": "value1",
          "type": "",
          "description": ""
        },
        {
          "name": "variable2",
          "secure": false,
          "value": "value2",
          "type": "",
          "description": ""
        }
      ]
    }
  ],
  "runtime_data": [
    {
      "id": "a123456",
      "engine_name": "terraform",
      "engine_version": "v0.13",
      "state_store_url": "https://schematics.cloud.ibm.com/v1/workspaces/myworkspace-123456/runtime_data/a123456/state_store",
      "log_store_url": "https://schematics.cloud.ibm.com/v1/workspaces/myworkspace-123456/runtime_data/a123456/log_store"
    }
  ],
  "shared_data": {
    "resource_group_id": ""
  },
  "updated_at": "0001-01-01T00:00:00Z",
  "last_health_check_at": "0001-01-01T00:00:00Z"
}
 INFO:root:Completed Creating Schematic Workspace
```
{: screen}

## Next steps
{: #next-steps-create-api}
{: api}

After you've successfully created a workspace, you can begin [Generating a plan](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-generate-plan#generate-plan-api) to validate all of the configuration properties. 
