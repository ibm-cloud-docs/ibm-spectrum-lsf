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

# Generating a plan
{: #generate-plan}

After you have created your workspace, you need to generate a plan to validate all the configuration properties.

## Generating a plan using the UI
{: #generate-plan-ui}
{: ui}

1. In the {{site.data.keyword.cloud}} console, after the workspace is created, you can review the properties and the variables that are associated with that workspace by using the _Settings_ tab. You can review all the values, make changes if applicable, and click Generate plan. 
2. When you click **Generate plan**, a new log is generated that can be viewed in the Jobs tab by clicking Jobs. 
3. Review the log file for any errors, fix the properties, and regenerate the plan by clicking **Generate plan** again.

## Next steps
{: #next-steps-generate-ui}
{: ui}

After you have successfully generated a plan, you can begin [Applying a plan](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-applying-plan&interface=ui) to deploy your {{site.data.keyword.cloud_notm}} resources to build a {{site.data.keyword.spectrum_short}} cluster instance.

## Generating a plan using the CLI
{: #generate-plan-cli}
{: cli}

Run the following command to generate the plan for your workspace.

```
ibmcloud schematics plan --id <WORKSPACE_ID>
```
{: pre}

You can view the log file to look for errors or confirm that the action was completed successfully. You might need to run this command multiple times to track the outcome of the command until it is completed.

```
ibmcloud schematics logs --id <WORKSPACE_ID>
```
{: pre}

## Next steps
{: #next-steps-generate-plan-cli}
{: cli}

After you have successfully generated a plan, you can begin [Applying a plan](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-applying-plan&interface=cli) to deploy your {{site.data.keyword.cloud_notm}} resources to build a {{site.data.keyword.spectrum_short}} cluster instance.

## Generating a plan using the API
{: #generate-plan-api}
{: api}

1. To generate a plan by using {{site.data.keyword.bplong_notm}} Python APIs, create a Python file and provide a name of your choice, for example, `schematics_generate_plan.py`.
2. Copy and paste the [Generate a plan using {{site.data.keyword.bpshort}} Python API](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-generate-plan&interface=api#example-request-generate) example request to your Python file.
3. Change the following parameters as part of the request:
  * Replace your {{site.data.keyword.cloud_notm}} API key to the `authenticator = IAMAuthenticator('<ibm-api-key>')` variable.
  * Change the API endpoint to the endpoint mentioned in [API endpoints](https://cloud.ibm.com/apidocs/schematics?code=python#api-endpoints){: external} according to the location that you want your {{site.data.keyword.bpshort}} workspace to reside, for example, `schematics_service.set_service_url('https://us.schematics.cloud.ibm.com')`.
4. Inside the `schematics_service.plan_workspace_command` function, provide the following parameters:
  * Provide the workspace ID that you created in the [Create a workspace](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-creating-workspace) task or from the {{site.data.keyword.cloud_notm}} console for {{site.data.keyword.bpshort}}, for example, `us-south.workspace.Terraform-Schematics-Python-Workspace.b3bbc9f5`.
  * Export your {{site.data.keyword.cloud_notm}} API key by using the following command:
  
    ```
    export IBMCLOUD_API_KEY="<ibm-cloud-api-key>"
    ```
    {: pre}

  *  Run the following curl command to create a refresh token: 

    ```
    curl -X POST "https://iam.cloud.ibm.com/identity/token" -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=urn:ibm:params:oauth:grant-type:apikey&apikey=$IBMCLOUD_API_KEY" -u bx:bx
    ```
    {: pre}

5. Make sure to update the following required parameters: `api_key`, `ibm_customer_number`, `remote_allowed_ips`, `ssh_key_name`, and `zone`.
6. Run the Python script by using `python3 <python-file-name>` to generate a plan in the {{site.data.keyword.cloud_notm}}.
7. You get an activity ID in the response if the parameters passed as part of the request are valid. You should be able to see the plan generated in the {{site.data.keyword.bpshort}} workspace that you created in the {{site.data.keyword.cloud_notm}} console. If you don’t get a successful response, the error response contains the errors that you need to resolve. Resolve those errors and run the script until you are able to get a valid response and generate a plan.
8. If you want to check the logs of the action, see [Retrieving action logs with {{site.data.keyword.bpshort}} API](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-retrieve-action-logs) to retrieve the logs. The response contains the status of the action, and it appears in either a `COMPLETED` or `FAILED` state.

### Example Python request
{: #example-request-generate}
{: api}

```python
# Generate a plan using Schematics Python API

import json, logging

from ibm_cloud_sdk_core.authenticators import IAMAuthenticator
from ibm_schematics.schematics_v1 import SchematicsV1

logging.basicConfig()
logging.root.setLevel(logging.NOTSET)
logging.basicConfig(level=logging.NOTSET)

authenticator = IAMAuthenticator('<ibmcloud-api-key>')

schematics_service = SchematicsV1(authenticator = authenticator)

schematics_service.set_service_url('https://us.schematics.cloud.ibm.com')

logging.info("Started Generating Schematic Plan")

workspace_activity_plan_result = schematics_service.plan_workspace_command(
    w_id='<workspace id>',
    refresh_token='<refresh-token>'
).get_result()

print(json.dumps(workspace_activity_plan_result, indent=2))

logging.info("Completed Generating Schematic Plan")
```
{: codeblock}

### Example Python response
{: #example-response-generate}
{: api}

```python
INFO:root:Started Generating Schematic Plan
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): iam.cloud.ibm.com:443
DEBUG:urllib3.connectionpool:https://iam.cloud.ibm.com:443 "POST /identity/token HTTP/1.1" 200 985
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): us.schematics.cloud.ibm.com:443
DEBUG:urllib3.connectionpool:https://us.schematics.cloud.ibm.com:443 "POST /v1/workspaces/us-south.workspace.Schematic-Sunil-Test-Workspace.5a4cbf11/plan HTTP/1.1" 202 49
{
  "activityid": "8c2ce5f031d23a60d26690d3cb398bb1"
}
INFO:root:Completed Generating Schematic Plan
```
{: screen}

## Next steps
{: #next-steps-generate-plan-api}
{: api}

After you have successfully generated a plan, you can begin [Applying a plan](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-applying-plan&interface=api) to deploy your {{site.data.keyword.cloud_notm}} resources to build a {{site.data.keyword.spectrum_short}} cluster instance.
