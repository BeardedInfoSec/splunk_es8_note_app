# ES8 Notes App for Splunk SOAR

## Overview
The ES8 Notes App enables Splunk SOAR playbooks to add and retrieve notes directly from Splunk Enterprise Security 8 Mission Control via the Splunkd REST API (https://<splunk_host>:8089).

This connector supports both Findings and Investigations, allowing analysts to document and sync contextual notes within automation workflows — without needing to log into Splunk Enterprise Security directly.

--------------------------------------------------------------------------------

Supported Actions:
- Add Note: Adds a note to a Splunk Mission Control investigation or finding.
- Get Notes: Retrieves all notes associated with a given investigation

--------------------------------------------------------------------------------

Configuration Parameters:
- Splunk Host (base_url): Base Splunkd URL including port 8089 (e.g. https://192.168.128.33:8089).
- Username: Splunk username with missioncontrol_admin or es_admin privileges.
- Password: Password for the Splunk user.
- Verify SSL: Whether to verify SSL certificates. Disable for labs or self-signed setups.

Example configuration:
{
  "base_url": "https://192.168.128.33:8089",
  "username": "abarbas",
  "password": "YourPassword",
  "verify_ssl": false
}

--------------------------------------------------------------------------------

Actions & Input Parameters:

1. Add Note
Adds a note to a Splunk Mission Control Investigation or Finding.
Endpoint: POST /servicesNS/nobody/missioncontrol/v1/incidents/{id}/notes?notable_time=<epoch>
Input Parameters:
- id (required): The Investigation or Finding ID.
- content (required): Note body text.
- notable_time (optional): Epoch time for notable findings (auto-generated if missing).

2. Get Notes
Retrieves all notes for a given Investigation.
Endpoint: GET /servicesNS/nobody/missioncontrol/v1/investigations/{id}/notes
Input Parameters:
- id (required): Investigation ID.
- limit (optional): Maximum number of notes to return (default: 100).

--------------------------------------------------------------------------------

Permissions Required:
- missioncontrol_admin
- es_admin
- Or custom role with access to /servicesNS/nobody/missioncontrol/v1/incidents/ and /servicesNS/nobody/missioncontrol/v1/investigations/

--------------------------------------------------------------------------------

Testing in Splunk SOAR:
1. Configure the app with Splunk 8089 credentials.
2. Run Get Notes with a known investigation or finding ID.
3. Run Add Note to verify it appears in Mission Control.

--------------------------------------------------------------------------------

Troubleshooting:
- 401 Unauthorized: Invalid credentials, verify Splunk username and password.
- Payload invalid JSON: Misformatted payload, ensure note text is valid JSON.
- SSL Error: Self-signed certificate, disable SSL verification for labs.

--------------------------------------------------------------------------------

Version History:
1.4.0 (2025-10-10): Simplified connector — supports Add Note & Get Notes only.
1.3.x: Previous versions included update/delete and MC lookup actions.

--------------------------------------------------------------------------------

Author:
Aaron Barbas
Splunk Staff Security Solutions Architect
Automation Games / ES8 Integration Suite
