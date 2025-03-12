---

copyright:
  years: 2021, 2022
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

# Destroying resources
{: #deleting-resources}

If you don't need the {{site.data.keyword.spectrum_short}} cluster and want to destroy all of its associated VPC resources, you can remove them from your {{site.data.keyword.cloud}} account.

Before you perform the actions to destroy the resources, you must first ensure that no worker nodes created by the {{site.data.keyword.spectrum_short}} auto scaling feature still exist. The total number of worker nodes in the cluster should be equal to the value you specified for `worker_node_min_count`.
{: important}

## Destroying resources using the UI
{: #destroy-resources-ui}
{: ui}

1. In the {{site.data.keyword.cloud_notm}} console on the Schematics > Workspaces page, select Actions > Destroy to delete all the related VPC resources that were deployed as part of that workspace.
2. If you select the option to destroy resources, decide whether you want to destroy all of them. This action cannot be undone. 
3. Confirm the action by entering the workspace name in the text box and click Destroy.

## Destroying resources using the CLI
{: #deleting-resources-cli}
{: cli}

Run the following command to remove your VPC resources from your workspace:

```
ibmcloud schematics destroy --id <WORKSPACE_ID>
```
{: pre}

You can monitor the log files to view the deletion progress of all {{site.data.keyword.cloud_notm}} resources.
{: note}

## Destroying resources using Schematics Python API
{: #deleting-resources-api}
{: api}

1. To destroy resources by using the {{site.data.keyword.bplong_notm}} Python APIs, create a Python file and provide a name of your choice, for example, `schematics_destroy_resources.py`.
2. Copy and paste the [Destroy resources using Schematics Python API](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-deleting-resources#example-request-destroy) example request to your Python file.
3. Change the following parameters as part of the request:
  * Replace your {{site.data.keyword.cloud_notm}} key to the `authenticator = IAMAuthenticator('<ibm-api-key>')` variable.
  * Change the API endpoint to the endpoint mentioned in [API endpoints](https://cloud.ibm.com/apidocs/schematics?code=python#api-endpoints){: external} according to the location that you want your {{site.data.keyword.bpshort}} workspace to reside, for example, `schematics_service.set_service_url('https://us.schematics.cloud.ibm.com')`.
4. Inside the `schematics_service.destroy_workspace_command` function, provide the following parameters:
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

5. Run the Python script by using `python3 <python-file-name>` to destroy the resources that you created in the {{site.data.keyword.bpshort}} workspace in the {{site.data.keyword.cloud_notm}}.
6. You get an activity ID in the response if the parameters passed as part of the request are valid. You should be able to see that the resources are deleted in the {{site.data.keyword.bpshort}} workspace that you created in the {{site.data.keyword.cloud_notm}} console. If you don’t get a successful response, the error response contains the errors that you need to resolve. Resolve those errors and run the script until you are able to get a valid response and destroy the resources.
7. If you want to check the logs of the action, see [Retrieving action logs with {{site.data.keyword.bpshort}} API](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-retrieve-action-logs) to retrieve the logs. The response contains the status of the action, and it appears in either a `COMPLETED` or `FAILED` state.

### Example Python request
{: #example-request-destroy}
{: api}

```python

# Destroy resources using Schematics Python API

import json, logging

from ibm_cloud_sdk_core.authenticators import IAMAuthenticator
from ibm_schematics.schematics_v1 import SchematicsV1

logging.basicConfig()
logging.root.setLevel(logging.NOTSET)
logging.basicConfig(level=logging.NOTSET)

authenticator = IAMAuthenticator('<ibmcloud-api-key>')

schematics_service = SchematicsV1(authenticator = authenticator)

schematics_service.set_service_url('https://us.schematics.cloud.ibm.com')

logging.info("Started Deletion of Schematic Resources")

workspace_activity_destroy_result = schematics_service.destroy_workspace_command(
    w_id='<workspace-id>',
    refresh_token='<refresh-token>'
).get_result()

print(json.dumps(workspace_activity_destroy_result, indent=2))

logging.info("Completed Deletion of Schematic Resources")
```
{: codeblock}

### Example Python response
{: #example-response-destroy}
{: api}

```python
INFO:root:Started Deletion of Schematic Resources
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): iam.cloud.ibm.com:443
DEBUG:urllib3.connectionpool:https://iam.cloud.ibm.com:443 "POST /identity/token HTTP/1.1" 200 985
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): us.schematics.cloud.ibm.com:443
DEBUG:urllib3.connectionpool:https://us.schematics.cloud.ibm.com:443 "PUT /v1/workspaces/us-south.workspace.Schematic-Sunil-Test-Workspace.5a4cbf11/destroy HTTP/1.1" 202 49
{
  "activityid": "4bc90aac48f7cf9850bddf1ba9407eb5"
}
INFO:root:Completed Deletion of Schematic Resources
```
{: screen}
