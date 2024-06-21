---

copyright:
  years: 2021, 2023
lastupdated: "2023-01-30"

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

# Retrieving action logs with Schematics API
{: #retrieve-action-logs}

1. To retrieve the logs for an action, such as generate a plan, apply a plan, and destroy resources using {{site.data.keyword.bplong}} Python APIs, create a Python file and provide a name of your choice, for example, `schematics_logs_api.py`.
2. Copy and paste the [Retrieve {{site.data.keyword.bpshort}} workspace action details using Python API](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-retrieve-action-logs#example-request-retrieve-action-logs) example request into your Python file.
3. Change the following parameters as part of the request:
  * Replace the `authenticator = IAMAuthenticator('<ibm-api-key>')` variable with your {{site.data.keyword.cloud_notm}} API key.
  * Change the API endpoint to the endpoint mentioned in this link according to the location where you want your {{site.data.keyword.bpshort}} workspace to reside, for example, `schematics_service.set_service_url('https://us.schematics.cloud.ibm.com')`.
4. Inside the `schematics_service.get_workspace_activity` function, provide the following parameters:
  * Provide the workspace ID that you generated in the [Creating a workspace](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-creating-workspace) task, for example, `us-south.workspace.Terraform-Schematics-Python-Workspace.b3bbc9f5`.
  * Provide an activity ID that was generated as a successful response for the specific action for which you want to retrieve logs. You received an activity ID when you successfully completed the action in the {{site.data.keyword.bpshort}} workspace.
5. Run the Python script by using `python3 <python-file-name>` to retrieve the logs for an action for your {{site.data.keyword.bpshort}} workspace in the {{site.data.keyword.cloud_notm}}.
6. You get an action ID, name, and action status, such as `COMPLETED` or `FAILED`, in response when it is determined whether the parameters passed as part of the request are valid. You should be able to retrieve the logs for an action for a specific {{site.data.keyword.bpshort}} workspace. If you don’t get a successful response, the error response contains the errors that you need to resolve. Resolve those errors and run the script until you are able to get a valid response and can retrieve the logs.

## Example Python request
{: #example-request-retrieve-action-logs}

```python
# Retrieve Schematics workspace action details using Python API

import json, logging

from ibm_cloud_sdk_core.authenticators import IAMAuthenticator
from ibm_schematics.schematics_v1 import SchematicsV1

logging.basicConfig()
logging.root.setLevel(logging.NOTSET)
logging.basicConfig(level=logging.NOTSET)

authenticator = IAMAuthenticator('<ibmcloud-api-key>')

schematics_service = SchematicsV1(authenticator = authenticator)

schematics_service.set_service_url('https://us.schematics.cloud.ibm.com')

logging.info("Started Retrieving the Schematic logs")

workspace_activity = schematics_service.get_workspace_activity(
    w_id='<workspace-id>',
    activity_id='<activity-ID>'
).get_result()

print(json.dumps(workspace_activity, indent=2))

logging.info("Completed Retrieving the Schematic logs")
```
{: codeblock}

## Example Python response
{: #example-response-retrieve-action-logs}

The Python response is a generic example. Versioning and deployment values are subject to change due to frequent code updates. 
{: note}

```python
INFO:root:Started Retrieving the Schematic logs
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): iam.cloud.ibm.com:443
DEBUG:urllib3.connectionpool:https://iam.cloud.ibm.com:443 "POST /identity/token HTTP/1.1" 200 992
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): us.schematics.cloud.ibm.com:443
DEBUG:urllib3.connectionpool:https://us.schematics.cloud.ibm.com:443 "GET /v1/workspaces/us-east.workspace.sunil-myworkspace-may12.2e0a560f/actions/6242ef71d4003bdae4a7ed7a3cbb0887 HTTP/1.1" 200 None
{
"action_id": "a11223334b11224345",
"name": "PLAN",
"status": "COMPLETED",
"message": [],
"performed_by": "user@us.ibm.com",
"performed_at": "2019-08-21T14:07:25.814722573Z",
"templates": [
{
"template_id": "a1234b-2124",
"template_type": "terraform_v0.12",
"start_time": "2019-08-21T14:07:26.120106199Z",
"end_time": "2019-08-21T14:07:30.088495607Z",
"status": "COMPLETED",
"message": "{"messagekey":"M2001_ActivitySuccessful","parms":{},"requestid":"1234b1234b123-1234n13","timestamp":"2019-08-21T14:07:30.088498532Z"}",
"log_url": "https://schematics.cloud.ibm.com/v1/workspaces/myworkspace-123456/runtime_data/a1234b-2124/log_store/actions/11223334b11224345"
}
]
}
INFO:root:Completed Retrieving the Schematic logs
```
{: screen}


