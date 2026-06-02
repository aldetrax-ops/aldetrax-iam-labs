# Local Enterprise IAM Lab (Keycloak)

## Objective
A hands-on implementation of enterprise Identity and Access Management (IAM) principles using an open-source Identity Provider (IdP). This lab replicates critical access control mechanisms—specifically MFA, RBAC, and system audit logging—without relying on cloud subscription dependencies[cite: 1].

---

## Core Security Controls Implemented

### 1. Multi-Factor Authentication (MFA)
* **Threat Target:** Credential stuffing and compromised administrative sessions[cite: 1].
* **Configuration:** Enforced global Time-Based One-Time Password (TOTP) tracking within the operational realm. New accounts are blocked from accessing resources until a cryptographic token is paired with an authenticator app.

### 2. Role-Based Access Control (RBAC) & Privilege Boundaries
* **Threat Target:** Privilege creep and unauthorized lateral movement[cite: 1].
* **Configuration:** Implemented strict least-privilege access control by mapping permissions to specific operational roles instead of individual users[cite: 1]:

| Security Group | Assigned Role | Access Level |
|       |        |               |
| **SecOps-Admins** | `Realm-Admin` | Full control over client applications and token signing keys. |
| **Compliance-Auditors** | `Auditor-Read-Only` | Read-only access to system telemetry and auth logs[cite: 1]. |
| **General-Staff** | `Standard-User` | Zero admin privileges; profile management only. |

### 3. Identity Telemetry & Auditing
* **Threat Target:** Undetected brute-force attempts and unmapped policy changes[cite: 1].
* **Configuration:** Activated live system events logging. The engine records timestamped JSON data for every authentication lifecycle shift, creating a clean audit trail for SIEM parsing.

---

## Deployment Blueprint

The environment is containerized via Docker for network and host isolation.
