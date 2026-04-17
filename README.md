# Workflows Palo Alto Cortex XDR Audit and Incidents Integration for IBM QRadar
This workflows can be used to configure a new Source Log of type Universal Cloud REST API protocol in order to ingest  Audit Log [Get Audit Management Log and 
Get Audit Agent Report ] and Incident Management [Get all Incidents and  Get all Alerts].

https://docs-cortex.paloaltonetworks.com/r/Cortex-XDR-REST-API/Audit-Log

https://docs-cortex.paloaltonetworks.com/r/Cortex-XDR-REST-API/Incident-Management

Universal Cloud REST API protocol

https://www.ibm.com/docs/en/qsip/7.4.0?topic=configuration-universal-cloud-rest-api-protocol


## Configuration & Installation Guide

This guide details the steps required to integrate Palo Alto Networks Cortex XDR with IBM QRadar using the Universal Cloud REST API.
### 1. Prerequisites (Cortex XDR)
Before beginning, ensure you have gathered the following credentials from your Cortex XDR instance:

API Key
API Key ID (x-xdr-auth-id)
Hostname (e.g., api-yourcompany.xdr.us.paloaltonetworks.com)

#### Parameter Configuration
Edit the **cortexxdr_workflow.parameter.values.xml** file and populate it with your values:

hostname: Place the hostname **without** https://.

api_key_id: Your API Key ID.

api_key: Your API Key.


### 2. Pre-Deployment Validation (CLI Testing)
Before creating log sources in the UI, test the Workflow XML files via the QRadar CLI to ensure they are free of syntax errors and can successfully reach the API.

#### Testing Audits Workflow
The following command should pull Audit payloads without errors. Use grep to filter for the custom CORTEX_DEBUG tags:

```console
/opt/qradar/bin/test-workflow.sh -w cortexxdr_workflow_audit.xml -wp cortexxdr_workflow.parameter.values.xml | grep -i CORTEX_DEBUG
```
Expected Output:
```console
[CORTEX_DEBUG] Cycle Start. Bookmark: 1776426468849 | End Window: 1776427482333
[CORTEX_DEBUG] Agent API Success. Found 0 events.
[CORTEX_DEBUG] Mgmt API Success. Found 1 events.
[CORTEX_DEBUG] Cycle Complete. New Bookmark saved: 1776427488852
```

#### Testing Incidents and Alerts Workflow
```console
/opt/qradar/bin/test-workflow.sh -w cortexxdr_workflow_incidents_alerts.xml -wp cortexxdr_workflow.parameter.values.xml | grep -i CORTEX_DEBUG
```

Expected Output:
```console
[CORTEX_DEBUG] Starting Incidents/Alerts Cycle.
[CORTEX_DEBUG] Incidents API Success. Found 0 incidents.
[CORTEX_DEBUG] Alerts API Success. Found 3 alerts.
[CORTEX_DEBUG] Incidents/Alerts Cycle Complete.
```


### 3. Creating Log Sources in QRadar UI
We will create two distinct log sources to manage different data streams.

#### A. "Audit" Log Source
Log in to QRadar Console.

1- Click the Admin tab.

2- Click the QRadar Log Source Management app icon.

3- Click New Log Source and then select Single Log Source.

4- On the Select a Log Source Type page, select "Universal DSM" (you will do custom mapping) and then click Select Protocol Type button.

5- On the Select a Protocol Type page, select "Universal Cloud REST API" and click Configure Log Source Parameters.

6- **Log Source Identifier should be CortexXDR_Audit** (to change the name to something else one the workflow and look for it and change )

7- On the Configure the Log Source parameters page, configure the log source parameters and click Configure Protocol Parameters.

8- On the Configure the protocol parameters page, configure the protocol-specific parameters (cortexxdr_workflow_audit.xml and cortexxdr_workflow.parameter.values.xml).

9- In the Test protocol parameters window, click Start Test.

10- To fix any errors, click Configure Protocol Parameters. Configure the parameters and click Test Protocol Parameters.

11- Click Finish and Deploy Changes.



#### B. "Incidents & Alerts" Log Source
Log in to QRadar Console.

1- Click the Admin tab.

2- Click the QRadar Log Source Management app icon.

3- Click New Log Source and then select Single Log Source.

4- On the Select a Log Source Type page, select "Universal DSM" (you will do custom mapping) and then click Select Protocol Type button.

5- On the Select a Protocol Type page, select "Universal Cloud REST API" and click Configure Log Source Parameters.

6- **Log Source Identifier should be CortexXDR_Incidents_Alerts**   (to change the name to something else one the workflow and look for it and change )

7- On the Configure the Log Source parameters page, configure the log source parameters and click Configure Protocol Parameters.

8- On the Configure the protocol parameters page, configure the protocol-specific parameters (cortexxdr_workflow_incidents_alerts.xml and cortexxdr_workflow.parameter.values.xml).

9- In the Test protocol parameters window, click Start Test.

10- To fix any errors, click Configure Protocol Parameters. Configure the parameters and click Test Protocol Parameters.

11- Click Finish and Deploy Changes.


### 4. Real-Time Monitoring
To verify that the log sources are pulling events every cycle (e.g., every 2 minutes), monitor the qradar.log file from the console:

```console
tail -f /var/log/qradar.log | grep CORTEX_DEBUG
```

Expected Output:
```console
Apr 10 11:05:57 ::ffff:127.0.0.1 [ecs-ec-ingress.ecs-ec-ingress] [Thread-383] com.q1labs.semsources.sources.universalcloudrestapi.v2.workflow.action.LogAction: [INFO] [NOT:0000006000][hostname/- -] [-/- -][CORTEX_DEBUG] Cycle Start. Bookmark: 177643***61 | End Window: 177643***7687
Apr 10 11:05:52 ::ffff:127.0.0.1 [ecs-ec-ingress.ecs-ec-ingress] [Thread-383] com.q1labs.semsources.sources.universalcloudrestapi.v2.workflow.action.LogAction: [INFO] [NOT:0000006000][hostname/- -] [-/- -][CORTEX_DEBUG] Agent API Success. Found 2 events.
Apr 10 11:06:02 ::ffff:127.0.0.1 [ecs-ec-ingress.ecs-ec-ingress] [Thread-383] com.q1labs.semsources.sources.universalcloudrestapi.v2.workflow.action.LogAction: [INFO] [NOT:0000006000][hostname/- -] [-/- -][CORTEX_DEBUG] Mgmt API Success. Found 1 events.
Apr 10 11:06:02 ::ffff:127.0.0.1 [ecs-ec-ingress.ecs-ec-ingress] [Thread-383] com.q1labs.semsources.sources.universalcloudrestapi.v2.workflow.action.LogAction: [INFO] [NOT:0000006000][hostname/- -] [-/- -][CORTEX_DEBUG] Cycle Complete. New Bookmark saved: 177643***7687
Apr 10 11:06:22 ::ffff:127.0.0.1 [ecs-ec-ingress.ecs-ec-ingress] [Thread-632] com.q1labs.semsources.sources.universalcloudrestapi.v2.workflow.action.LogAction: [INFO] [NOT:0000006000][hostname/- -] [-/- -][CORTEX_DEBUG] Starting Incidents/Alerts Cycle. End Window: 17764***263374
Apr 10 11:06:22 ::ffff:127.0.0.1 [ecs-ec-ingress.ecs-ec-ingress] [Thread-632] com.q1labs.semsources.sources.universalcloudrestapi.v2.workflow.action.LogAction: [INFO] [NOT:0000006000][hostname/- -] [-/- -][CORTEX_DEBUG] Incidents API Success. Found 2 incidents.
Apr 10 11:06:22 ::ffff:127.0.0.1 [ecs-ec-ingress.ecs-ec-ingress] [Thread-632] com.q1labs.semsources.sources.universalcloudrestapi.v2.workflow.action.LogAction: [INFO] [NOT:0000006000][hostname/- -] [-/- -][CORTEX_DEBUG] Alerts API Success. Found 1 alerts.
Apr 10 11:06:22 ::ffff:127.0.0.1 [ecs-ec-ingress.ecs-ec-ingress] [Thread-632] com.q1labs.semsources.sources.universalcloudrestapi.v2.workflow.action.LogAction: [INFO] [NOT:0000006000][hostname/- -] [-/- -][CORTEX_DEBUG] Incidents/Alerts Cycle Complete.
```



### 5. Custom DSM Normalization
To ensure the JSON payload is correctly mapped to QRadar fields (Source IP, Username, Event ID, etc.), refer to the IBM Documentation for JSON Keypath expressions:
IBM Docs: [Expressions in JSON format for structured data](https://www.ibm.com/docs/en/qradar-on-cloud?topic=editor-expressions-in-json-format-structured-data)


### 6. Troubleshooting
If you encounter issues during implementation:

Direct API Test: Use curl from the QRadar CLI to verify the Cortex XDR hostname is reachable.

Connectivity: Ensure firewalls or proxies allow QRadar to communicate with the Cortex XDR cloud over port 443.

Permissions: Verify the Cortex XDR API Key is generated with Admin-level.

Workflow Errors: If test-workflow.sh fails, check the XML line numbers mentioned in the error output for stray characters or missing tags.


