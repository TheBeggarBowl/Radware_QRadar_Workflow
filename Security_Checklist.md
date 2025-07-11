# ✅ QRadar Workflow Security Checklist

This checklist is intended for teams deploying a Universal Cloud REST (UCR) workflow in IBM QRadar. It outlines key security controls and practices to validate before production rollout.

---

## 🔐 1. API Key Handling
- [ ] API keys are **not hardcoded** in the XML
- [ ] API keys are input via QRadar `<Parameter>` fields
- [ ] API keys have **least privilege** access (read-only/reporting)
- [ ] API keys are rotated and reviewed regularly

---

## 🔒 2. SSL Verification
- [ ] SSL verification is **enabled** for all HTTPS connections
- [ ] Insecure options like `verify: false` are used only in development/testing
- [ ] QRadar is configured to trust all production API endpoints

---

## 📁 3. Log & Output Handling
- [ ] Workflow avoids logging sensitive data or full API payloads
- [ ] Logs contain only high-level metadata (e.g., counts, timestamps)
- [ ] API credentials and user-supplied parameters are never written to logs

---

## 🧩 4. Context Separation
- [ ] `context_id` is used exclusively for Application Protection workflows
- [ ] `infra_context_id` is used only for Infrastructure (L4 DDoS) events
- [ ] The workflow does not mix different contexts between endpoints

---

## ⏱️ 5. Polling & Time Range Alignment
- [ ] Polling interval is set to **5+ minutes** (300+ sec)
- [ ] `fetch_minutes`, `fetch_hours`, and `fetch_days` match the interval
- [ ] The workflow avoids overlap or missed event windows

---

## 📦 6. QRadar Log Ingestion
- [ ] Event format is **JSON**
- [ ] Parsing rules (DSM, custom regex, or event properties) are defined
- [ ] Unused or verbose fields are ignored or filtered

---

## 🧠 7. QRadar Access Controls
- [ ] Access to log source and workflow configuration is **restricted**
- [ ] Parameter values (like API key, context IDs) are not exposed to general users
- [ ] Role-based access controls are enforced for viewing logs and metadata

---

## 🧪 8. Testing & Validation
- [ ] Workflow is tested in a **non-production environment**
- [ ] At least one successful event ingestion is confirmed
- [ ] Workflow responses are validated for correctness and structure

---

## ✅ Summary

Use this checklist as part of your internal change control, security review, or operational runbook when deploying UCR workflows in QRadar.

---

## ⚠️ Disclaimer

This checklist and workflow were developed for **educational and development purposes only**.

The author makes no guarantees regarding its correctness, compatibility, or performance in your production environment.  
**Use at your own risk.** You are solely responsible for validating, testing, and securing this integration.

The author assumes **no liability** for any data loss, operational impact, or security issues resulting from the use of this checklist or integration.
