# ES8 Notes App for Splunk SOAR

## Overview
The **ES8 Notes App** allows Splunk SOAR playbooks to **read, create, update, and delete Mission Control notes** directly via the Splunkd REST API (`https://<splunk_host>:8089`).  
It supports both **Findings** and **Investigations** within Splunk Enterprise Security 8 / Mission Control.

This connector provides seamless integration with Splunk’s native note-taking features used in Enterprise Security Notables and Mission Control Investigations.

---

## 🧩 Supported Actions

| Action | Description |
|--------|-------------|
| **Add Note** | Adds a note to a Splunk Mission Control investigation or finding. |
| **Get Notes** | Retrieves all notes associated with a given investigation or finding. |
| **Update Note** | Updates the title or content of an existing note. |
| **Delete Note** | Deletes a note from a specific investigation or finding. |
| **Get MC ID from Notable** | (Optional utility) Converts an ES notable event ID into its corresponding Mission Control investigation ID. |

---

## ⚙️ Configuration Parameters

| Field | Required | Description |
|--------|-----------|-------------|
| **Splunk Host (base_url)** | ✅ | Base Splunkd URL including port 8089 (e.g. `https://192.168.128.33:8089`). |
| **Auth Token (token)** | ✅ | Splunk authentication token with `missioncontrol_admin` or `es_admin` capabilities. |
| **Verify SSL** | ❌ | Whether to verify SSL certificates (recommended for production). |

Example configuration:
```json
{
  "base_url": "https://192.168.128.33:8089",
  "token": "Splunk <session_token>",
  "verify_ssl": false
}
```

---

## 🚀 Actions & Input Parameters

### 📝 1. Add Note
**Endpoint:**  
`POST /servicesNS/nobody/missioncontrol/v1/investigations/{id}/notes`

**Input Parameters:**
| Name | Required | Description |
|------|-----------|-------------|
| `id` | ✅ | Investigation or finding ID. |
| `title` | ✅ | Note title. |
| `content` | ✅ | Note content. |

**Example Output:**
```json
{
  "note_id": "ab12cd34-ef56-7890-gh12-ijklmn345678",
  "title": "IOC review complete",
  "author": "admin",
  "object_type": "Investigation"
}
```

---

### 📖 2. Get Notes
**Endpoint:**  
`GET /servicesNS/nobody/missioncontrol/v1/investigations/{id}/notes`

**Input Parameters:**
| Name | Required | Description |
|------|-----------|-------------|
| `id` | ✅ | Investigation or finding ID. |

**Example Output:**
```json
{
  "total_notes": 3,
  "notes": [
    {
      "id": "ef137615-28af-4753-9a58-5216bfa7bf50",
      "title": "Initial triage",
      "content": "Notified SOC and started correlation search",
      "author": "analyst1"
    }
  ]
}
```

---

### ✏️ 3. Update Note
**Endpoint:**  
`POST /servicesNS/nobody/missioncontrol/v1/investigations/{id}/notes/{note_id}`

**Input Parameters:**
| Name | Required | Description |
|------|-----------|-------------|
| `id` | ✅ | Investigation or finding ID. |
| `note_id` | ✅ | Note ID. |
| `title` | ❌ | Updated note title (optional). |
| `content` | ❌ | Updated note content (optional). |

---

### ❌ 4. Delete Note
**Endpoint:**  
`DELETE /servicesNS/nobody/missioncontrol/v1/investigations/{id}/notes/{note_id}`

**Input Parameters:**
| Name | Required | Description |
|------|-----------|-------------|
| `id` | ✅ | Investigation or finding ID. |
| `note_id` | ✅ | Note ID. |

**Example Output:**
```json
{
  "status": "deleted",
  "deleted_note_id": "ef137615-28af-4753-9a58-5216bfa7bf50",
  "object_id": "5e1a3b12-0e24-4bfa-ae12-77b3216b9e4a",
  "object_type": "Investigation"
}
```

---

## 🧠 Helper Action: Get Mission Control ID from Notable
**Endpoint:**  
`GET /servicesNS/nobody/missioncontrol/v1/get_mc_incident_id_from_es_notable_id?notable_id=<uuid>`

**Purpose:**  
Converts an Enterprise Security notable event ID into its corresponding Mission Control investigation ID.

**Example Input:**
```json
{
  "reference_id": "54a8c71e-8f9d-4980-bd22-852405195824@@notable@@54a8c71e8f9d4980bd22852405195824"
}
```

**Example Output:**
```json
{
  "investigation_id": "5e1a3b12-0e24-4bfa-ae12-77b3216b9e4a"
}
```

---

## 🔐 Permissions Required

The Splunk user or token used must have:
- **missioncontrol_admin**, **es_admin**, or equivalent role
- Access to the `/servicesNS/nobody/missioncontrol/v1/` namespace

---

## 🧪 Testing in Splunk SOAR

1. Configure the app with your Splunk 8089 credentials.  
2. Test `get_notes` on a known investigation ID.  
3. Add a note, then confirm it appears in Mission Control.  
4. Update and delete the note to confirm bidirectional functionality.  

---

## 🧰 Troubleshooting

| Symptom | Likely Cause | Fix |
|----------|---------------|-----|
| `Missing required parameters: id and note_id` | You only provided `note_id` | Pass both `id` (investigation/finding ID) and `note_id`. |
| `Error 400: ES notable event id is required` | Incorrect parameter name | Use `notable_id` in the query string. |
| SSL errors | Self-signed certificate | Disable SSL verification in config (for lab environments only). |

---

## 📄 Version History

| Version | Date | Notes |
|----------|------|-------|
| **1.0.0** | Initial release | Basic add/get note actions |
| **1.1.0** | Added update & delete note support |
| **1.2.0** | Added dynamic object type detection (Finding vs Investigation) |

---

## 🧑‍💻 Author
**Aaron Barbas**  
Splunk Staff Security Solutions Architect  
*Automation Games / ES8 Integration Suite*
