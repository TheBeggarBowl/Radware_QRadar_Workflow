# QRadar Workflow: Radware CWAF + DDoS API Integration

This QRadar Universal Cloud REST API workflow fetches events from Radware Cloud Services using API key authentication. It supports:

- ✅ WAF (Web Application Firewall) Events  
- ✅ Web DDoS (L7) Events  
- ✅ CDDos (L4 Infrastructure DDoS) Events  

---

## 🧾 Parameters

| Parameter           | Description                                               | Required | Default |
|---------------------|-----------------------------------------------------------|----------|---------|
| `api_key`           | Radware API Key                                           | ✅ Yes   |         |
| `context_id`        | Application Protection Context ID (WAF, Web DDoS)         | ✅ If using WAF or Web DDoS |
| `infra_context_id`  | Infrastructure Protection Context ID (CDDos)              | ✅ If using CDDos |
| `application_id`    | (Optional) App Filter for WAF/Web DDoS                    | ❌ No    |         |
| `fetch_minutes`     | Minutes to fetch data for                                 | ❌ No    | `5`     |
| `fetch_hours`       | Hours to fetch data for                                   | ❌ No    | `0`     |
| `fetch_days`        | Days to fetch data for                                    | ❌ No    | `0`     |
| `page`              | Pagination page number                                    | ❌ No    | `0`     |
| `size`              | Number of events per page                                 | ❌ No    | `100`   |

---

## 🔐 Authentication & Context Logic

- **API Key (`api_key`)**: Required for all API calls.
- **Context ID (`context_id`)**: Required if fetching:
  - WAF events (`APPWALL_REPORTS`)
  - Web DDoS events (`L7_DDOS_ATTACK_REPORT`)
- **Infra Context ID (`infra_context_id`)**: Required if fetching:
  - CDDos events (`L4_DDOS_ATTACK_REPORT`)
- **Application ID (`application_id`)**: Optional, filters WAF/Web DDoS events if supplied.

> ℹ️ If either context is omitted, the associated API call is skipped automatically.

---

## 🛠 Retry Logic

Each API call includes a **retry mechanism**:
- Retries up to **2 times** on failure (e.g., HTTP 500 or timeout)

---

## 🧠 Workflow Logic Summary

- Dynamic time range based on `fetch_days`, `fetch_hours`, and `fetch_minutes`
- Optional filtering by `application_id`
- Conditional API execution:
  - WAF & Web DDoS require `context_id`
  - CDDos requires `infra_context_id`
- Pagination support (`page`, `size`)
- Logging for event count, inputs, and skipped sections

---

## 🚀 QRadar Deployment Instructions (UCR Workflow)

This section outlines how to deploy the Radware API workflow in IBM QRadar using the Universal Cloud REST API protocol.

---

### 🛠 Step-by-Step Deployment

#### 1. **Import the Workflow**
- Go to: `Admin → Universal Cloud REST API`
- Click **“Upload Workflow”**
- Select and upload the `Q_Radar_Workflow_Updated.xml` file

#### 2. **Create a Log Source**
- Go to: `Admin → Log Sources`
- Click **“Add”**
- Choose:
  - **Log Source Type**: `Universal Cloud REST API`
  - **Protocol Configuration**: Select your uploaded workflow
  - **Log Source Identifier**: `Radware_CWAF_DDOS` or similar

#### 3. **Configure Workflow Parameters**
QRadar will prompt for values based on your XML `<Parameter>` entries:

| Parameter           | Description                                 | Example Value                    |
|---------------------|---------------------------------------------|----------------------------------|
| `api_key`           | Your Radware API Key                        | `abcd1234xyz...`                 |
| `context_id`        | Context ID for WAF / Web DDoS               | `app-ctx-xxxx-xxxx`              |
| `infra_context_id`  | Context ID for Infrastructure Protection    | `infra-ctx-xxxx-xxxx`            |
| `application_id`    | (Optional) App ID to filter events          | `radware-prod-app1`              |
| `fetch_minutes`     | Minutes of history to fetch                 | `5`                              |
| `fetch_hours`       | Hours of history to fetch                   | `0`                              |
| `fetch_days`        | Days of history to fetch                    | `1`                              |
| `page`              | Page number for pagination                  | `0`                              |
| `size`              | Number of records per page                  | `100`                            |

> ℹ️ You can leave optional values blank unless filtering is required.

#### 4. **Set Polling Interval**
- Still in the Log Source form:
  - Under **Protocol Configuration**, set **Polling Interval** to e.g. `300` seconds (5 min)

#### 5. **Deploy & Verify**
- Click **“Save”**, then **“Deploy Changes”**
- Go to **Log Activity**
- Search for events using:
  - Log Source Name: `Radware_CWAF_DDOS`
  - Time Range: `Last 5 minutes`

---

## 📦 Output Format & Parsing

### 🧾 Log Format: `JSON`

This workflow returns logs in **raw JSON**, which QRadar supports natively via:
- Built-in JSON parsers
- Custom Event Properties
- Regex Extractors

### 🧩 DSM Support

If your events don’t parse automatically:
- Use **Custom DSM Editor**
- Map fields from the raw JSON into QRadar events using regex or JSON path
- Example: extract `attackType`, `sourceIp`, `severity`, `timestamp`

> ✅ No transformation to LEEF is required unless QRadar parsing logic requires it — JSON is the recommended default.

---

## ⚠️ Disclaimer

This workflow was developed for **educational and development purposes only**.

The author makes no guarantees regarding its correctness, compatibility, or performance in your production environment.  
**Use at your own risk.** You are solely responsible for validating, testing, and securing this integration.

The author assumes **no liability** for any data loss, operational impact, or security issues resulting from the use of this workflow.