# Incident Response Final Report: E-Commerce Data Breach

**Date of Report:** January 2023
**Severity:** Critical
**Status:** Closed
**Affected Records:** ~50,000 customers
**Incident Type:** Data Breach / Insecure Direct Object Reference (IDOR)

## 1. Executive Summary

On December 28, 2022, the organization confirmed a severe security incident involving unauthorized access to customer Personally Identifiable Information (PII) and financial data. A threat actor successfully exfiltrated approximately 50,000 customer records. The estimated financial impact, encompassing direct costs and potential revenue loss, is $100,000. The incident has been contained, the vulnerability remediated, and a full post-incident investigation concluded.

## 2. Incident Timeline (PT)

* **Dec 22, 2022 (03:13 PM):** An employee received an extortion email from an external threat actor claiming possession of stolen customer data, demanding a $25,000 cryptocurrency ransom to prevent public disclosure. The email was initially classified as spam and deleted.
* **Dec 28, 2022:** The threat actor sent a follow-up email to the same employee containing a sample of the compromised data and escalating the ransom demand to $50,000.
* **Dec 28, 2022:** The employee escalated the communication to the Security Operations team. An official Incident Response (IR) was initiated.
* **Dec 28 – Dec 31, 2022:** The IR team conducted forensic log analysis, identified the root cause, and determined the scope of the exfiltration.

## 3. Technical Investigation & Root Cause Analysis

The IR team traveled on-site and initiated a forensic review of the web application infrastructure.

* **Root Cause:** The breach was facilitated by an **Insecure Direct Object Reference (IDOR)** / Forced Browsing vulnerability within the e-commerce web application.
* **Attack Vector:** The application failed to implement proper authorization checks on the purchase confirmation page. The threat actor bypassed access controls by sequentially modifying the `order_number` parameter in the URL string.
* **Indicators of Compromise (IOCs):** Web server access logs revealed an anomalous, exceptionally high volume of sequential HTTP `GET` requests targeting customer order URIs originating from a single external IP address.

## 4. Containment, Eradication, and Recovery

* **Vulnerability Patching:** Access control logic was immediately updated to validate user session tokens against the requested order ID before rendering the confirmation page.
* **Public Relations & Compliance:** In coordination with the PR and Legal departments, breach notification protocols were executed to inform affected customers.
* **Customer Protection:** The organization provided complimentary identity protection and credit monitoring services to all 50,000 impacted individuals.

## 5. Post-Incident Recommendations (Lessons Learned)

To harden the infrastructure and prevent recurrence, the following strategic actions are mandated:

1. **Continuous Security Validation:** Integrate routine dynamic vulnerability scans (DAST) and schedule quarterly third-party penetration testing.
2. **Access Control Hardening (Zero Trust principles):**
   * Implement strict object-level authorization checks to ensure users can only access content associated with their authenticated session.
   * Deploy URL Allowlisting / Web Application Firewall (WAF) rules to strictly define acceptable URL request patterns and automatically block anomalous directory traversal or forced browsing attempts.
