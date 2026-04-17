# Workflows Palo Alto Cortex XDR Audit and Incidents Integration for IBM QRadar
This workflows can be used to configure a new Source Log of type Universal Cloud REST API protocol in order to ingest  Audit Log [Get Audit Management Log and 
Get Audit Agent Report ] and Incident Management [Get all Incidents and  Get all Alerts].

https://docs-cortex.paloaltonetworks.com/r/Cortex-XDR-REST-API/Audit-Log

https://docs-cortex.paloaltonetworks.com/r/Cortex-XDR-REST-API/Incident-Management













Testing Workflows
the command line is used to specify the workflow and workflow parameter values to load. The -w command is used to specify the myworkflow.XML workflow and the -wp command is used to specify the myworkflow.parameter.values.xml workflow parameter values.

/opt/qradar/bin/test-workflow.sh -w myworkflow.xml -wp myworkflow.parameter.values.xml
