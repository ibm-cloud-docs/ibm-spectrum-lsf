---

copyright:
  years: 2023
lastupdated: "2023-03-24"

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

# Accessing REST API calls with `curl`
{: #access-rest-api-calls-curl}

IBM Spectrum LSF Application Center provides standard RESTful web services for application submission, data management, job information query, job actions, and more. The LSF Applicationn Center web service API can be integrated with many languages and methods. This example shows how to access the LSF Application Center REST API calls by using [`curl`](https://www.ibm.com/docs/en/slac/10.2.0?topic=reference-jobs){: external}.
{: shortdesc}

## Before you begin
{: #before-you-begin}

1. To access the LSF Application Center REST APIs from your terminal, you need to establish an SSH connection:

    ```
    ssh -L 8080:localhost:8080 -J root@{FLOATING_IP_ADDRESS} lsfadmin@{MANAGEMENT_NODE_IP_ADDRESS}
    ```
    {: codeblock}

    where `MANAGEMENT_NODE_IP_ADDRESS` needs to be replaced with the management node IP address that is associated with `<cluster_prefix>-management-host-0`, and `FLOATING_IP_ADDRESS` needs to be replaced with the login node floating IP address. To find the management and login node IPs, see the instructions for [Gathering IP addresses](/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-accessing-lsf-gui#gathering-ip-addresses). The default user is `lsfadmin`.

## Connecting to LSF Application Center with `curl`
{: #connect-lsf-application-center-curl}

1. Open a new terminal from your local device and run the following commands:

    1. Ping the cluster:

        ```bash
        curl -k -X POST -H "Accept: application/json" http://localhost:8080/platform/ws/ping
        ```
        {: pre}

    2. Log into the cluster:

        ```bash
        curl -k -X POST -H "Accept: application/json" -H "Content-Type: application/xml" -d "<User><name>lsfadmin</name> <pass>YourPassword</pass> </User>" http://localhost:8080/platform/ws/logon
        ```
        {: pre}

        The output of this command contains the token that you need to run additional commands. The token is only valid for a particular timeframe.
        {: note}

        **Example output:**

        ```
        {"csrftoken":"dbf6208a-0c01-4cb3-b6e1-9f75390311e5","token":"lsfadmin\"2022-05-25T20:53:12Z\"cn8FS6/FIXiVZc5vMpfPdoob9bosjk85u3lEPseqrWdX+DyWMPYkYgPOC5UJ+a4m87zyjHDq1DhjIZyYx1X47SFGRS4MRzeah94l+EpNBazKilXsG8cVuYyUgtz9M0J6\"PBNNTKLPN3JlyOpnCqI7cg=="}
        ```
        {: screen}

    3. After you receive your token, you need to make two changes to it. First, add `"platform_token="` at the beginning of the token, and second, replace the `\"` with `#quote#`. See the following example of an updated token:

        ```
        export 
        MYTOKEN=platform_token=lsfadmin#quote#2022-05-25T20:53:12Z#quote#cn8FS6/FIXiVZc5vMpfPdoob9bosjk85u3lEPseqrWdX+DyWMPYkYgPOC5UJ+a4m87zyjHDq1DhjIZyYx1X47SFGRS4MRzeah94l+EpNBazKilXsG8cVuYyUgtz9M0J6#quote#PBNNTKLPN3JlyOpnCqI7cg==
        ```
        {: screen}

    4. Get the cluster info:

        ```bash
        curl -k -X GET -H 'Content-Type: application/xml' -H "Cookie: $MYTOKEN" -H 'Accept:text/plain,application/xml,text/xml,multipart/mixed' -H 'Accept-Language:en-us'  http://localhost:8080/platform/ws/clusters/local
        ```
        {: pre}

    5. (Optional) Get the Platform Application Center version:

        ```bash
        curl -k -X GET -H 'Content-Type: application/xml' -H "Cookie: $MYTOKEN" -H 'Accept:text/plain,application/xml,text/xml,multipart/mixed' -H 'Accept-Language:en-us'  http://localhost:8080/platform/ws/version
        ```
        {: pre}

    6. (Optional) List out the users:

        ```bash
        curl -k -X GET -H 'Content-Type: application/xml' -H "Cookie: $MYTOKEN" -H 'Accept:text/plain,application/xml,text/xml,multipart/mixed' -H 'Accept-Language:en-us'   http://localhost:8080/platform/ws/users
        ```
        {: pre}

    7. (Optional) List out the groups:

        ```bash
        curl -k -X GET -H 'Content-Type: application/xml' -H "Cookie: $MYTOKEN" -H 'Accept:text/plain,application/xml,text/xml,multipart/mixed' -H 'Accept-Language:en-us'   http://localhost:8080/platform/ws/usergroups
        ```
        {: pre}

    8. (Optional) List out the host details:

        ```bash
        curl -k -X GET -H 'Content-Type: application/xml' -H "Cookie: $MYTOKEN" -H 'Accept:text/plain,application/xml,text/xml,multipart/mixed' -H 'Accept-Language:en-us'   http://localhost:8080/platform/ws/hosts
        ```
        {: pre}

    9. List out the existing jobs:

        ```bash
        curl -k -X GET -H 'Content-Type: application/xml' -H "Cookie: $MYTOKEN" -H 'Accept:text/plain,application/xml,text/xml,multipart/mixed' -H 'Accept-Language:en-us'   http://localhost:8080/platform/ws/pacclient/jobs
        ```
        {: pre}

    10. (Optional) Get detailed job information by job ID (GET):

        ```bash
        curl -k -X GET -H 'Content-Type: application/xml' -H "Cookie: $MYTOKEN" -H 'Accept:text/plain,application/xml,text/xml,multipart/mixed' -H 'Accept-Language:en-us'   http://localhost:8080/platform/ws/jobs/<job ID>
        ```
        {: pre}

    11. (Optional) Get basic and detailed job information by job attributes (GET):

        ```bash
        curl -k -X GET -H 'Content-Type: application/xml' -H "Cookie: $MYTOKEN" -H 'Accept:text/plain,application/xml,text/xml,multipart/mixed' -H 'Accept-Language:en-us'   http://localhost:8080/platform/ws/jobs/fullinfo
        ```
        {: pre}

2. Submit a job:

    ```bash
    curl -k -X POST -H 'Content-Type: multipart/mixed; boundary=bqJky99mlBWa-ZuqjC53mG6EzbmlxB' -H 'Accept:text/xml,application/xml' -H "Cookie:$MYTOKEN" -H 'Accept-Language:en-us' -d '--bqJky99mlBWa-ZuqjC53mG6EzbmlxB
    Content-Disposition: form-data; name="AppName"
    Content-ID: <AppName>

    generic
    --bqJky99mlBWa-ZuqjC53mG6EzbmlxB
    Content-Disposition: form-data; name="data"
    Content-Type: multipart/mixed; boundary=_Part_1_701508.1145579811786
    Accept-Language:en-us
    Content-ID: <data>

    --_Part_1_701508.1145579811786
    Content-Disposition: form-data; name="COMMANDTORUN"
    Content-Type: application/xml; charset=UTF-8
    Content-Transfer-Encoding: 8bit
    Accept-Language:en-us

    <AppParam><id>COMMANDTORUN</id><value>sleep 99</value><type></type></AppParam>
    --bqJky99mlBWa-ZuqjC53mG6EzbmlxB--
    --_Part_1_701508.1145579811786--
    ' http://localhost:8080/platform/webservice/pacclient/submitapp
    ```
    {: codeblock}






