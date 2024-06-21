---

copyright:
  years: 2021, 2022, 2024
lastupdated: "2024-06-20"

keywords: 

subcollection: ibm-spectrum-lsf

---

{:support: data-reuse='support'}
{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:troubleshoot: data-hd-content-type='troubleshoot'}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:external: target="_blank" .external}
{:pre: .pre}
{:tip: .tip}
{:note: .note}
{:important: .important}

# Troubleshooting
{: #troubleshooting-spectrum-lsf}

## Why is IBM Cloud Schematics not able to clone the private GitHub repo?
{: #troubleshoot-topic-1}
{: troubleshoot}
{: support}

Schematics isn't able to clone the private GitHub repository, and you are seeing the following error message: `Failed to clone git repository, repository not found (check url, also check the scope 'repo' of the personal access token if SCHEMATICSGITTOKEN is used)`
{: tsSymptoms}

You didn't provide the correct GitHub token, or you didn't provide a GitHub token altogether.
{: tsCauses}

Provide a [GitHub token](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token){: external} and check to see whether the correct GitHub token has been provided in the `github_token` parameter in the create workspace API.
{: tsResolve}

## Why is IBM Cloud Schematics not able to clone the public GitHub repo?
{: #troubleshoot-topic-2}
{: troubleshoot}
{: support}

Schematics isn't able to clone the public GitHub repository, and you are seeing one of the following error messages:

* `Fatal, could not download repo, Failed to clone git repository, authentication required (or the git url is incorrect). Problems found with the Repository. Please Rectify and Retry`
* `Template error: Failed to clone git repository, authentication required (or the git url is incorrect)`
{: tsSymptoms}

You didn't provide the correct GitHub URL, or you provided a GitHub token, which is not required to clone a public repo. A GitHub access token is only required to access a private repo.
{: tsCauses}

Do not provide a GitHub token, and check to see whether the GitHub token was provided in the `github_token` parameter while creating a workspace by using the public repo.
{: tsResolve}

## Why is IBM Cloud Schematics not able to create a workspace?
{: #troubleshoot-topic-3}
{: troubleshoot}
{: support}

Schematics isn't able to create a workspace, and you are seeing the following error message: `You don't have the required to create a workspace in any resource groups. You must be assigned the manager role on the Schematics service in at least one resource group. Contact your account administrator for access.`
{: tsSymptoms}

You don't have the required access to create a workspace in any resource groups. You must be assigned the manager role on the Schematics service in at least one resource group.
{: tsCauses}

Contact your account administrator and get assigned with the manager role on the Schematics service in at least one resource group.
{: tsResolve}

## Why is IBM Cloud Schematics not able to provision the cluster and fails with an error message for the `lsf_license_confirmation` variable?
{: #troubleshoot-topic-4}
{: troubleshoot}
{: support}

Schematics isn't able to provision the cluster, and you are seeing the following error message: `Error: Invalid value for variable "lsf_license_confirmation"`
{: tsSymptoms}

You entered a value other than "true" for the property `lsf_license_confirmation`.
{: tsCauses}

The property `lsf_license_confirmation` only accepts "true" as a valid value. A "true" value indicates that you have agreed to one of the following two conditions:

1. If you are deploying a production cluster, you have confirmed with your business team that you have enough licenses to deploy the {{site.data.keyword.spectrum_full_notm}} on {{site.data.keyword.cloud_notm}} and that these licenses are covered for use under the International Program License Agreement (IPLA).
2. You are deploying an evaluation cluster with {{site.data.keyword.spectrum_full_notm}} on {{site.data.keyword.cloud_notm}} and agree to abide by the International License Agreement for Evaluation of Program (ILAE).

IBM terms of software use for both IPLA and ILAE can be found [here](https://www-03.ibm.com/software/sla/sladb.nsf/sla/bla){: external}.

After you agree to one of the two conditions, update the property value to "true" and try again.
{: tsResolve}

## Why is IBM Cloud Schematics not able to provision the cluster and fails with an authorization error?
{: #troubleshoot-topic-5}
{: troubleshoot}
{: support}

Schematics isn't able to provision the cluster, and you are seeing the following error message: `Request is not authorized. Check your user permissions and authorizations and try again.`
{: tsSymptoms}

You don't have the required access to get any VPC resources provisioned. 
{: tsCauses}

Contact your account administrator and get all of the required accesses. For more information, see [Required permissions](/docs/vpc?topic=vpc-resource-authorizations-required-for-api-and-cli-calls).
{: tsResolve}

## Why is IBM Cloud Schematics not able to provision the cluster and fails with an error that the provided name is not unique? 
{: #troubleshoot-topic-6}
{: troubleshoot}
{: support}

Schematics isn't able to provision the cluster, and you are seeing the following example error message:
{: tsSymptoms}

```
"code": "validation_unique_failed",
"message": "Provided Name (sample-lsf-vpc) is not unique",
"target": {
"name": "name",
"type": "field",
"value": "sample-lsf-vpc"
}
```
{: screen}

VPC resource names must be unique. If a resource exists with the same name, you might get a similar error.
{: tsCauses}

Deprovision the existing resource and try again.
{: tsResolve}

## Why is IBM Cloud Schematics not able to provision the cluster while using a custom image?
{: #troubleshoot-topic-7}
{: troubleshoot}
{: support}

While using a custom image, Schematics isn't able to provision the cluster, and you are seeing one of the following error messages:

* `The arugment "image" is required, but no definition was found.`
* `Unknown variable. There is no variable named "image_id".`
{: tsSymptoms}

The custom image that is used for one of the virtual server instances isn't present in the target region and zone or it is not accessible by the account and API key that is used to provision the cluster.
{: tsCauses}

If you are using a custom image for any of your virtual server instances, ensure that the custom image is available in the target region and zone and is accessible by the account and API key that is used to provision the cluster.
{: tsResolve}

## Why am I receiving an error for my refresh token?
{: #troubleshoot-topic-8}
{: troubleshoot}
{: support}

You are receiving a refresh token error in the **generate a plan**, **apply a plan**, and **destroy resources** requests: `Error: The provided Refresh Token is invalid. Please provide a proper refresh token for Terraform to run the configuration. Code: 400`
{: tsSymptoms}

You didn't provide the correct refresh token, or you didn't provide a refresh token altogether.
{: tsCauses}

Check to see whether the refresh token that was generated by using the curl command is correct; otherwise, regenerate the refresh token.
{: tsResolve}

## Why am I receiving an error when I apply a change to my workspace?
{: #troubleshoot-topic-9}
{: troubleshoot}
{: support}

You are receiving the following error when you try to apply a change to your workspace: `Apply failed due to "Error: Error Deleting Volume : The volume is still attached to an instance."`
{: tsSymptoms}

After reconfiguring the volume profile, capacity, or IOPS, your workspace needs to be cleaned up before applying the change. 
{: tsCauses}

You need to destroy your existing resources and try applying the change again. Your data on the storage node will be deleted if you destroy your existing resources.
{: tsResolve}

## Why does cluster creation fail with SSH issues?
{: #troubleshoot-topic-10}
{: troubleshoot}
{: support}

You are receiving the following error messages when the Ansible provisioner tries to set up the {{site.data.keyword.scale_short}} function on the worker and storage node resources:

* `msg": "Failed to connect to the host via ssh, Connection closed by UNKNOWN port 65535", "unreachable": true}`
* `Error: Failed to connect to the host via ssh: Connection timed out during banner exchange", "unreachable`
{: tsSymptoms}

While {{site.data.keyword.bpshort}} deploys the infrastructure resources, the automation code is configured with a few Ansible playbooks, which are required to set up the {{site.data.keyword.scale_short}} function on the virtual server instance nodes with the help of the Ansible provisioner. When the Ansible provisioner tries to SSH to these nodes to se the {{site.data.keyword.scale_short}} feature, the nodes go to an `unreachable` state. 
{: tsCauses}

To fix the issue, you can:
1. Try to destroy the resources from the workspace and deploy again.
2. If this issue is observed on all of the deployments, raise a support issue with the {{site.data.keyword.cloud_notm}} support team to investigate if there is an infrastructure issue. 
3. If there are no issues with the infrastructure, report this issue to the automation team who can investigate further.
{: tsResolve}

## Why do I see resource errors due to authentication or timeout issues?
{: #troubleshoot-topic-11}
{: troubleshoot}
{: support}

You are receiving the following error messages during the creation of any specific resource:

* `Error: An error occurred while performing the ‘authenticate’ step: Post “https://iam.cloud.ibm.com/identity/token”: context deadline exceeded (Client.Timeout exceeded while awaiting headers)`
* `Error: timeout while waiting for state to become 'done, ' (last state: 'provisioning', timeout: 10m0s)`
{: tsSymptoms}

While {{site.data.keyword.bpshort}} deploys the infrastructure resources, it authenticates with {{site.data.keyword.cloud_notm}} through API calls. If there are too many requests through the API to the cloud environment, {{site.data.keyword.bpshort}} won't be able to authenticate and could error out with the authentication error.
{: tsCauses}

To fix either issue (resource failing due to authentication error or the timeout error), destroy the resources from the {{site.data.keyword.bpshort}} workspace and retry deploying the resources. 
{: tsResolve}

## Limitation of available profiles for dedicated hosts
{: #troubleshoot-topic-12}
{: troubleshoot}
{: support}

The offering automatically selects instance profiles for dedicated hosts to be the same prefix (for example, bx2 and cx2) as ones for worker instances (`worker_node_instance_type`). However, available instance prefixes can be limited, depending on your target region. If you use dedicated hosts, check `ibmcloud target -r {region_name}` and `ibmcloud is dedicated-host-profiles` to see whether your `worker_node_instance_type` has the available prefix for your target region.

## Why am I receiving an error with the provided `ssh_key_name` value?
{: #troubleshoot-topic-13}
{: troubleshoot}
{: support}

You are receiving the following error when you try to generate or apply a plan on {{site.data.keyword.bpshort}} workspace: `failed due to "Error: No SSH Key found with name <KEY_NAME>".`
{: tsSymptoms}

Terraform could not find the given SSH key names that are provided by you.
{: tsCauses}

1. Check whether the given SSH key is present in the current region where the cluster is being provisioned. If the given SSH key is not present, create the SSH key in the current region.
2. While configuring multiple SSH keys, ensure that there is no white space added before or after the SSH key names.
3. If you are using multiple SSH keys, check whether a comma (,) is used as a delimiter between the SSH keys and that there is no white space added before or after the SSH key.
{: tsResolve}

## Why do I see a `remote-exec provisioner error` during cluster deployment?
{: #troubleshoot-topic-14}
{: troubleshoot}
{: support}

You encounter an error similar to this:
{: tsSymptoms}

```console
2023/10/30 08:09:30 Terraform apply | module.check_cluster_status.null_resource.remote_exec[0] (remote-exec): ls_gethostinfo(): Cannot locate master LIM now, try later
2023/10/30 08:09:30 Terraform apply | module.check_cluster_status.null_resource.remote_exec[0] (remote-exec): lsid: ls_getentitlementinfo() failed: Cannot locate master LIM now, try later
2023/10/30 08:09:30 Terraform apply |
Error: remote-exec provisioner error
```
{: pre}

When Schematics deploys the infrastructure resources, there is automatic validation of the health of the cluster nodes to ensure that all required configuration is set appropriately. In rare occasions, LSF is unable to find the LIM configuration and therefore, cannot start the LIM daemon on the management nodes.
{: tsCauses}

Sometimes the LSF management nodes take time to retrieve LIM status, so wait a while before checking. However, if the issue persists, destroy the cluster from Schematics and reapply.
{: tsResolve}

## Why do I see an error when I use {{site.data.keyword.cloud}} Secrets Manager during cluster deployment?
{: #troubleshoot-topic-15}
{: troubleshoot}
{: support}

When you use the {{site.data.keyword.cloud}} Secrets Manager for secure deployment input values, you encounter an error similar to this:
{: tsSymptoms}

```console
Unable to validate your configuration.
An error occurred when fetching secret from Secrets Manager. Reason: Error: Cannot find the secret de132a21-7d64-d63f-c77a-c86e9cd50d17 from secrets manager crn:v1:bluemix:public:secrets-manager:us-east:a/ec1b082b25144a52bb1a269c883d5a00:94e42547-ab61-4ce1-b7f1-d96b230a75eb::
```
{: pre}

There is a known issue with {{site.data.keyword.cloud}} projects when reading [**user credentials**](/docs/secrets-manager?topic=secrets-manager-user-credentials&interface=ui) type secrets from Secrets Manager.
{: tsCauses}

Change all user credential type secrets to [**arbitrary**](/docs/secrets-manager?topic=secrets-manager-arbitrary-secrets&interface=ui) type secrets in Secret Manager, and retry your deployment.
{: tsResolve}

## Why is my LDAP user not found?
{: #troubleshoot-topic-16}
{: troubleshoot}
{: support}

When attempting to switch to an LDAP user using the `su` command, you encounter an error stating that the user does not exist. An example of the error message might look like this:

```console
[lsfadmin@lsfuser05-ubuntu-two-mgmt-1 ~]$ su lsfuser05
su: user lsfuser05 does not exist
[lsfadmin@lsfuser05-ubuntu-two-mgmt-1 ~]
```
{: pre}
{: tsSymptoms}

The error occurs because the specified LDAP user (`lsfuser05` in this case) is not found on the LDAP server. The LDAP user might not have been created or is not available in the LDAP directory.
{: tsCauses}

[Verify that the user exists](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-create-ldap-user) using the `ldapsearch` command. If the user does not exist, [create the user](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-create-ldap-user).
{: tsResolve}

## Why does my LDAP user authentication fail?
{: #troubleshoot-topic-17}
{: troubleshoot}
{: support}

When attempting to switch to an LDAP user using the `su` command, you encounter an error stating that the system cannot authenticate the user. An example of the error message might look like this:

```console
[lsfadmin@lsfuser05-ubuntu-two-mgmt-1 ~]$ su lsfuser05
Password:
su: Authentication failure
[lsfadmin@lsfuser05-ubuntu-two-mgmt-1 ~]$
```
{: pre}
{: tsSymptoms}

An authentication failure suggests that the specified LDAP password is not correct.
{: tsCauses}

Reset the LDAP password:
{: tsResolve}

1. Connect to your OpenLDAP server through SSH by using the `ssh_to_ldap_node` command from the Schematics log output and switch to root user:

      ```text
      ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -o ServerAliveInterval=5 -o ServerAliveCountMax=1 -J vpcuser@<floatingg_IP_address> ubuntu@<LDAP_server_IP>
      ```
      {: codeblock}

      where `<floating_IP_address>` is the floating IP address for the bastion node and `<LDAP_server_IP>` is the IP address for the OpenLDAP node.

2. Reset the password using the `ldappasswd` command. This command allows you to set a new password for the specified LDAP user. Ensure to replace the distinguished name (DN) and user details with the correct values for your LDAP setup. Here is an example of resetting the user passoword:

      ```text
      ubuntu@<LDAP_server_IP>:~$ ldappasswd -x -D "cn=admin,dc=hpcaas,dc=com" -W -S "uid=lsfuser05,ou=People,dc=hpcaas,dc=com"
      New password:
      Re-enter new password:
      Enter LDAP Password:
      ubuntu@anand-ga-ldap-1:~$
      ```
     {: codeblock}

## Why the ssh connection not established using host shortnames?
{: #troubleshoot-topic-18}
{: troubleshoot}
{: support}

You are receiving the following error messages as the NetworkManager is not picking up the domain name:

```console
[root@user-lsf-test1-mgmt-2 ~]# nslookup user-scale-spectrum-storage-1
Server: 10.241.0.15
Address: 10.241.0.15#53
** server can't find user-scale-spectrum-storage-1: NXDOMAIN

or

[lsfadmin@user-lsf-test1-mgmt-2 ~]$ ssh user-lsf-test1-mgmt-1
ssh: Could not resolve hostname mani-lsf-basic-login: Name or service not known
```
{: pre}
{: tsSymptoms}

The error occurs because after configuring the DNS domains and custom resolver, the RHEL based systems uses NetworkManager service to pick up the domain name under search in `/etc/resolv.conf` file to resolve the shortname of the host without depending on the actual domain name.
{: tsCauses}

To resolve this issue:
{: tsResolve}

1. Go as root (sudo su).
2. Run the `Run systemctl restart NetworkManager` command.

Once the above command is submitted in `/etc/resolv.conf` file, search must be updated with the domain name as mentioned here:

```
[lsfadmin@user-lsf-test1-mgmt-2 ~]$ cat /etc/resolv.conf

Generated by NetworkManager

search lsf.com
nameserver 10.241.0.10
```