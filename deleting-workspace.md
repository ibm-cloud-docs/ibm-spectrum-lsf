---

copyright:
  years: 2021
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

# Deleting a workspace
{: #deleting-workspace}

After you destroy all associated VPC resources, if you don't need the workspace anymore, you can also delete the Schematics workspace.

## Deleting a workspace using the UI
{: #delete-workspace-ui}
{: ui}

1. In the {{site.data.keyword.cloud_notm}} console on the **Schematics > Workspaces** page, select **Actions > Delete workspace** to delete the schematics workspace.
2. Confirm the action by entering the workspace name in the text box and click **Delete workspace**.

## Deleting a workspace using the CLI
{: #delete-workspace-cli}
{: cli}

Run the following command to delete your workspace:

```
ibmcloud schematics workspace delete --id <WORKSPACE_ID>
```
{: pre}

You can monitor the log files to view the deletion progress of your workspace.
{: note}

## Deleting a workspace using the API
{: #delete-workspace-api}
{: api}

1. To delete a workspace by using the {{site.data.keyword.bplong_notm}} Python APIs, create a Python file and provide a name of your choice, for example, `schematics_delete_workspace.py`.
2. Copy and paste the [Delete a workspace using Schematics Python API](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-deleting-workspace#example-request-delete-workspace) example request to your Python file.
3. Change the following parameters as part of the request: 
    * Replace your {{site.data.keyword.cloud_notm}} key to the `authenticator = IAMAuthenticator('<ibm-api-key>')` variable.
    * Change the API endpoint to the endpoint mentioned in [API endpoints](https://cloud.ibm.com/apidocs/schematics?code=python#api-endpoints){: external} according to the location that you want your {{site.data.keyword.bpshort}} workspace to reside, for example, `schematics_service.set_service_url('https://us.schematics.cloud.ibm.com')`.
4. Inside the `schematics_service.delete_workspace` function, provide the following parameters:
    * Provide the workspace ID that you generated in the [Creating a workspace](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-creating-workspace) task, for example `us-south.workspace.Terraform-Schematics-Python-Workspace.b3bbc9f5`.
    * Export your {{site.data.keyword.cloud_notm}} API key by using the following command:
        
        ```
        export IBMCLOUD_API_KEY="<ibm-cloud-api-key>" 
        ```
        {: pre}

    * Run the following curl command to create a refresh token:
    
        ```
        curl -X POST "https://iam.cloud.ibm.com/identity/token" -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=urn:ibm:params:oauth:grant-type:apikey&apikey=$IBMCLOUD_API_KEY" -u bx:bx 
        ```
        {: pre}

    * Note: If you want to destroy the resources as well as delete the workspace, then set the `destroy_resources` parameter value to `True`. In this case, the resources are deleted first, and then the workspace is deleted. 
    
    * Note: If you want to delete the workspace but not the resources, then remove the `destroy_resources` parameter completely or set the `destroy_resources` parameter to `False`. If resources are already deleted, and if the `destroy_resources` parameter value is set to `True`, then the RESOURCE DELETE action is in the FAILED state.

5. Run the Python script by using `python3 <python-file-name>` to delete the workspace.
6. You get the status `Started Deleting Schematic Workspace` and `Completed Deleting Schematic Workspace` in response if the parameters passed as part of the request are valid. You should be able to see that the workspace is deleted in the {{site.data.keyword.bpshort}} that you created. If you don’t get a successful response, the error response contains the errors that you need to resolve. Resolve those errors and run the script until you are able to get a valid response and the workspace is deleted.

## Example Python request
{: #example-request-delete-workspace}
{: api}

```python

# Delete a workspace using Schematics Python API

import json, logging

from ibm_schematics.schematics_v1 import SchematicsV1
from ibm_cloud_sdk_core.authenticators import IAMAuthenticator

logging.basicConfig()
logging.root.setLevel(logging.NOTSET)
logging.basicConfig(level=logging.NOTSET)

authenticator = IAMAuthenticator('<ibmcloud-api-key>')

schematics_service = SchematicsV1(authenticator = authenticator)

schematics_service.set_service_url('https://us.schematics.cloud.ibm.com')

logging.info("Started Deleting Schematic Workspace")

workspace_delete_response = schematics_service.delete_workspace(
    w_id='<workspace id>',
    refresh_token='<refresh-token>',
    destroy_resources= True,
).get_result()

print(json.dumps(workspace_delete_response, indent=2))

logging.info("Completed Deleting Schematic Workspace")
```
{: codeblock}

## Example Python response
{: #example-response-delete-workspace}
{: api}

```python
INFO:root:Started Deleting Schematic Workspace
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): iam.cloud.ibm.com:443
DEBUG:urllib3.connectionpool:https://iam.cloud.ibm.com:443 "POST /identity/token HTTP/1.1" 200 987
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): us.schematics.cloud.ibm.com:443
DEBUG:urllib3.connectionpool:https://us.schematics.cloud.ibm.com:443 "DELETE /v1/workspaces/us-south.workspace.Schematic-Sunil-Test-Workspace.5a4cbf11?destroy_resources=true HTTP/1.1" 200 2
""
INFO:root:Completed Deleting Schematic Workspace
```
{: screen}
