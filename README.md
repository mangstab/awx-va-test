# 📄 Vulnerability Assessment (VA) – AWX Collector

This folder contains playbooks and workflows for performing **automated Vulnerability Assessment (VA)** using **AWX / Ansible**, **without agents or third‑party scanners**.  
The playbooks collect system information using safe, read‑only Linux commands (Ubuntu 24.x and similar) and generate machine‑readable evidence suitable for SOC 2 audits.

---

## 🎯 Purpose

- Automate VA evidence collection from all servers  
- Standardize and centralize the VA process  
- Produce per‑server JSON files and one consolidated merged JSON  
- Support automated generation of multi‑server VA reports  
- Improve consistency, traceability, and repeatability for SOC 2 Type 2 assessments

---

## ▶️ Usage (AWX)

### A) Job Template — Collect VA Evidence
- **Project:** This repository  
- **Playbook:** `va-collector/collect_va.yml`  
- **Inventory:** All production servers  
- **Credential:** SSH key / service account  
- **Output:**  
  - Per‑host JSON files stored on Controller (e.g., `/tmp/awx_va_results/`)  
  - Host‑level artifacts visible in AWX UI (via `set_stats`)

### B) Job Template — Merge Results
- **Playbook:** `va-collector/merge_va.yml`  
- **Output:**  
  - `/tmp/awx_va_merged.json` — consolidated VA data for all servers

### C) Workflow (mandatory)
Recommended pipeline:
[ Collect VA ] → [ Merge Results ] → [ Render DOCX Report ]

This enables fully automated, scheduled VA runs.

---

## 🧩 JSON Output Format (per server)

Example file: `va_<hostname>.json`
```json
{
  "host": "web-server-02",
  "ip": "10.11.88.80",
  "os_release": "...",
  "kernel": "Linux 6.8.0-1030-aws ...",
  "upgradable": "packages list...",
  "ports": "ss -tulnp output",
  "sshd_T": "effective sshd configuration",
  "services": "systemd service list",
  "security_status": "pro security-status output",
  "collected_at": "2026-03-10T02:15:00Z"
}
```
Use the merged JSON to build the Consolidated Overview and per‑server Appendix sections in the multi‑server VA report.

---

## 📑 VA Report Generation
### Option A — Manual
Open Multi-Server_Internal_VA_Report_Template.docx and populate:

Consolidated overview
Per‑server appendix pages
Findings summary

### Option B — Automated (recommended)
Run render_docx.yml with:

Input: awx_va_merged.json + report template .docx
Output: fully formatted multi‑server VA report (.docx)

---

## 🛠 Commands Collected (read‑only, safe for production)

uname -a
cat /etc/os-release
apt list --upgradable
ss -tulnp
sshd -T
systemctl list-units --type=service
pro security-status or ubuntu-security-status

---

## 🔐 Security Notes

No configuration changes or package installations
SSH credentials stored securely in AWX
Output can be archived or purged per policy
Suitable for SOC 2, ISO 27001, and internal audits

---

## ✔️ Compliance Alignment

SOC 2 CC7.1 – Vulnerability Management
SOC 2 CC7.2 – Monitoring & Detection
Quarterly VA cycles (Type 2 evidence)
Multi‑environment, multi‑region coverage

---

## 🙋 Extending the Collector
Add tasks to collect_va.yml (or create a role) to include:

CIS hardening checks
Kernel CVE parsing
Application‑specific checks (e.g., web server, DB)
