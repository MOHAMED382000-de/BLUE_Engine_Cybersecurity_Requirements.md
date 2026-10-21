# 🛡️ BLUE Engine: Cybersecurity Requirements & Rules Specification

## 📌 Executive Summary
This document defines the formal security requirements, detection rules, forensics specs, and evaluation criteria provided by the **Cybersecurity Team** to the **AI & Data Analysis Team** for the **BLUE Engine** within the ImpersonaGuard platform.

---

## 1. Domain Similarity Rules
* **Thresholds & Algorithms:** Implement fuzzy string matching (Levenshtein distance, Jaro-Winkler) combined with phonetic algorithms (Soundex/Metaphone) to flag domain similarity scores $\ge 80\%$.
* **Domain Categorization:**
  * **Legitimate:** Officially registered organization assets, authorized third-party vendors, and verified partner domains.
  * **Malicious (Lookalike):** Typosquatting, combosquatting, and IDN homograph attacks (e.g., `digita1-fortress.com`, `paypal-security-update.com`).
* **Verdict Contribution:** A high domain similarity score ($\ge 85\%$) combined with an external or spoofed sender address must directly escalate the message to a **High-Risk Phishing** verdict or trigger automated quarantine.

---

## 2. Email Header Forensics & Authentication
* **Required Header Fields:**
  * `Authentication-Results`, `Received`, `Return-Path`, `SPF`, `DKIM`, `DMARC`, `From`, `Reply-To`.
* **Protocol Interpretation Rules:**
  * **SPF (Sender Policy Framework):** Verify sending IP against authorized envelope domains (`PASS`, `FAIL`, `SOFTFAIL`, `NEUTRAL`).
  * **DKIM (DomainKeys Identified Mail):** Validate cryptographic signature to verify message integrity and origin.
  * **DMARC (Domain-based Message Authentication, Reporting, and Conformance):** Verify alignment between SPF and DKIM. A DMARC `FAIL` with `p=reject` or `p=quarantine` must significantly increase the overall threat score.
* **Feature Handover Standard:** The Cybersecurity team provides structured, pre-parsed security feature payloads (boolean/enum states) to the AI engine to minimize runtime parsing overhead and latency.

---

## 3. Threat Detection Rules & Labeled Datasets
* **Key Security Indicators:**
  * Suspicious or high-urgency language in subject/body (e.g., "Account Suspended", "Immediate Wire Transfer").
  * Display name spoofing (e.g., executive name paired with external/freemail domain).
  * Newly registered domains (NRDs) created $< 30$ days prior to email receipt.
  * Mismatched link targets (anchor text domain $\ne$ actual target URL).
* **Validation & Testing:**
  * Provide labeled ground-truth datasets consisting of **True Positives** (confirmed phishing lures), **False Positives** (internal newsletters/legitimate marketing), and **True Negatives**.
  * Validate detection outputs against real-world attack playbooks (Credential Harvesting, Spear-Phishing, BEC).

---

## 4. Anomaly Detection & Account Takeover (ATO)
* **Suspicious Behavioral Indicators:**
  * **Impossible Travel:** Geographically distant logins within an impossible timeframe.
  * **Anomalous Access Times:** Uncharacteristic login activity during off-hours or outside user/team baseline schedules.
  * **Authentication Anomaly:** High volume of failed MFA challenges followed by password resets or administrative role changes.
  * **Data Exfiltration Signals:** Bulk downloads or mass file deletion immediately following a new device login.
* **Anonymization & Logs:** Provide anonymized login logs with stable pseudonymous employee IDs, UTC timestamps, source network tags, and device posture flags.

---

## 5. Investigation & Mitigation Workflows
* **Investigation Workflow:**  
  `Threat Detection` $\rightarrow$ `Automated Triage & Scoring` $\rightarrow$ `Analyst Case Creation` $\rightarrow$ `Remediation / Training Assignment`
* **Mitigation Action Hierarchy:**
  * **Automated Actions (System-Enforced):** Email quarantine, session invalidation, forced password reset.
  * **Authorized Actions (Analyst Approval Required):** Firewall IP blocking, user account suspension, domain-wide email purge.
* **Escalation SLA:** High-severity alerts unresolved within 15 minutes must escalate automatically to the SOC Lead.

---

## 6. Evaluation & Validation Criteria
* **Performance Metrics:** The model must be evaluated using **Precision**, **Recall**, and **F1-Score** to ensure minimal False Positive rates ($\le 1\%$) and prevent alert fatigue for SOC analysts.
* **Continuous Feedback Loop:** Cybersecurity analysts will review disputed/incorrect AI verdicts weekly to provide labeled retraining samples and refine feature weights.

---

**Approved By:** Cybersecurity Team  
**Project:** ImpersonaGuard — BLUE Engine
