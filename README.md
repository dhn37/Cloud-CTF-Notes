# Multi Cloud Security CTF Notes

The notes are designed for real world cloud misconfiguration scenarios rather than theoretical coverage.

The primary goal of this repository is to help quickly identify identity, enumerate effective permissions, discover misconfigurations, and escalate privileges across AWS, Azure, and GCP environments.

---

## Contents

* AWS Cloud Security CTF Commands
* Azure Cloud Security CTF Commands
* GCP Cloud Security CTF Commands

Each section focuses on actionable CLI commands, common misconfigurations, and attacker style enumeration techniques used in cloud security assessments.

---

## Scope

These notes focus on

* Identity discovery and context setup
* Permission enumeration when IAM access is restricted
* Storage misconfigurations and data exposure
* Secrets discovery and credential leakage
* Privilege escalation through roles service accounts and managed identities
* Logging and metadata abuse scenarios

The commands and workflows are intentionally aligned with cloud CTF platforms and interview style lab assessments.

---

## Intended Audience

This repository is intended for

* Cloud Security Analysts
* Security Engineers
* Blue teamers learning attacker techniques
* CTF players preparing for cloud based challenges
* Candidates preparing for cloud security interviews

---

## How to Use This Repository

1. Start with identity and context commands to understand who you are and what environment you are in
2. Attempt IAM enumeration if permitted
3. If IAM is restricted pivot to service level probing to infer effective permissions
4. Focus on storage secrets and compute resources as primary targets
5. Look for privilege escalation paths through roles service accounts and managed identities

The notes are structured to be skimmed quickly during labs or reviewed before interviews.

---

## Cloud Coverage

### AWS

Covers IAM enumeration role assumption S3 Secrets Manager Lambda EC2 metadata and common privilege escalation paths.

### Azure

Covers Azure AD role assignments managed identities storage accounts Key Vault App Services Logic Apps and Azure resource enumeration.

### GCP

Covers project level IAM service accounts impersonation GCS Secret Manager Compute Engine Cloud Functions Cloud Run and metadata abuse.

---

## Disclaimer

These notes are provided for educational purposes only.
They are intended for use in authorized environments such as CTFs labs interviews and training scenarios.

---

## Author

Maintained by Dhanush Nair

---

If you want I can also

* Rewrite this README in a more academic style
* Add a short motivation section for recruiters
* Create per folder READMEs for AWS Azure and GCP
* Optimize this repo for interview portfolio reviews

Just tell me what you want next.
