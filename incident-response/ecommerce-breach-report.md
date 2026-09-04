# Incident Response Final Report: E-Commerce Data Breach

> *Case study completed as part of the Google Cybersecurity Professional Certificate ("Sound the Alarm: Detection and Response"), adapted and documented to demonstrate incident response methodology, forensic log analysis, and detection engineering skills.*

| Meta          | Details |
| :---          | :--- |
| **Date**      | January 2023 |
| **Severity**  | Critical |
| **Status**    | Closed |
| **Impact**    | ~50,000 customer records compromised |
| **Type**      | Data Breach / Insecure Direct Object Reference (IDOR) |

## 1. Executive Summary

On December 28, 2022, the organization confirmed a severe security incident involving unauthorized access to customer Personally Identifiable Information (PII) and financial data. A threat actor successfully exfiltrated approximately 50,000 customer records. The estimated financial impact, encompassing direct costs and potential revenue loss, is $100,000. The incident has been contained, the vulnerability remediated, and a full post-incident investigation concluded.

## 2. Incident Timeline (PT)

* **Dec 22, 2022 (03:13 PM):** An employee received an extortion email from an external threat actor claiming possession of stolen customer data, demanding a $25,000 cryptocurrency ransom to prevent public disclosure. The email was initially classified as spam and deleted.
* **Dec 28, 2022:** The threat actor sent a follow-up email to the same employee containing a sample of the compromised data and escalating the ransom demand to $50,000.
* **Dec 28, 2022:** The employee escalated the communication to the Security Operations team. An official Incident Response (IR) was initiated.
* **Dec 28 – Dec 31, 2022:** The IR team conducted forensic log analysis, identified the root cause, and determined the scope of the exfiltration.

## 3. Technical Investigation & Root Cause Analysis

The IR team conducted a forensic review of the web application infrastructure and SIEM alerts.

* **Root Cause:** The breach was facilitated by an **Insecure Direct Object Reference (IDOR)** / Forced Browsing vulnerability within the e-commerce web application. The application failed to implement proper authorization checks on the `/order_confirmation` endpoint.
* **Attack Vector & Artifacts:** The threat actor bypassed access controls by sequentially modifying the `order_number` parameter in the URL string. Web server access logs revealed an anomalous volume of sequential HTTP `GET` requests originating from a single external IP address (`198.51.100.45`).

**Example access log excerpt (illustrative):**
```text
198.51.100.45 - - [28/Dec/2022:14:01:05 +0000] "GET /order_confirmation?order_number=49001 HTTP/1.1" 200 4532 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64)"
198.51.100.45 - - [28/Dec/2022:14:01:06 +0000] "GET /order_confirmation?order_number=49002 HTTP/1.1" 200 4589 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64)"
198.51.100.45 - - [28/Dec/2022:14:01:06 +0000] "GET /order_confirmation?order_number=49003 HTTP/1.1" 200 4511 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64)"
```

**Detection logic (Splunk SPL):**
To quantify the scope of the exfiltration, the following query identifies IP addresses generating excessive requests to the vulnerable endpoint within a short timeframe:

```spl
index=web_logs sourcetype=nginx:access uri_path="/order_confirmation" status=200
| stats count as request_count dc(uri_query) as unique_orders by clientip
| where request_count > 100 AND unique_orders > 100
| sort - request_count
```

## 4. Containment, Eradication, and Recovery

* **Vulnerability Patching:** Access control logic was immediately updated to validate user session tokens against the requested order ID before rendering the confirmation page.
* **Public Relations & Compliance:** In coordination with the PR and Legal departments, breach notification protocols were executed to inform affected customers.
* **Customer Protection:** The organization provided complimentary identity protection and credit monitoring services to all 50,000 impacted individuals.

## 5. Post-Incident Recommendations (Lessons Learned)

1. **Continuous Security Validation:** Integrate routine dynamic vulnerability scans (DAST) and schedule quarterly third-party penetration testing.
2. **Access Control Hardening (Zero Trust principles):**
   * Implement strict object-level authorization checks to ensure users can only access content associated with their authenticated session.
   * Deploy URL Allowlisting / Web Application Firewall (WAF) rules to strictly define acceptable URL request patterns and automatically block anomalous directory traversal or forced browsing attempts.
