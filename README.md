# Cybersecurity & SOC Analyst Lab Reports

Welcome to my cybersecurity portfolio. This repository serves as a centralized documentation hub for my hands-on labs, security audits, and vulnerability assessments.

The focus here is on Blue Team operations, risk management, and incident response analysis.

> *Note: The reports below are based on case studies from the Google Cybersecurity Professional Certificate, adapted with additional hands-on technical work (firewall configuration, log analysis, scan syntax) to demonstrate applied skills beyond the original course material.*

## Portfolio Structure

### 1. Risk & Vulnerability Assessment

* [NIST SP 800-30 Risk Assessment: Marketing Database](./vulnerability-assessments/marketing-db-report.md) – A comprehensive risk assessment of a core MySQL infrastructure using the NIST framework.

### 2. Incident Response (IR)

* [Incident Final Report: E-Commerce Data Breach (IDOR)](./incident-response/ecommerce-breach-report.md) – A formal post-incident report detailing the forensic investigation, containment, and remediation of a forced browsing / IDOR attack.

---

## Frameworks, Standards & Concepts Used

* **NIST SP 800-30 Rev. 1** (Risk Assessment Guide for Information Security Systems)
* **OWASP Top 10** (Specifically IDOR / Insecure Direct Object Reference mitigation)
* **AAA Framework** (Authentication, Authorization, Auditing)
* **Incident Response Lifecycle** (Detection, Analysis, Containment, Eradication, Recovery)
* **Principle of Least Privilege** & Role-Based Access Control (RBAC)

## Tools & Techniques

* **Network Scanning:** Nmap (service/version detection, SSL/TLS cipher enumeration)
* **Firewall Hardening:** UFW (host-based access control, subnet allow-listing)
* **Log Analysis & Detection:** Splunk SPL (SIEM query syntax for anomaly detection)
