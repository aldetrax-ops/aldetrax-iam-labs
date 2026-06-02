# Local Enterprise IAM Lab (Keycloak)

##  Objective
A hands-on implementation of enterprise Identity and Access Management (IAM) principles using an open-source Identity Provider (IdP). This lab replicates critical access control mechanisms—specifically MFA, RBAC, and system audit logging—without relying on cloud subscription dependencies.

---

##  Tech Stack & Architecture
* **Identity Management:** Keycloak (Latest)
* **Containerization:** Docker / Docker Compose
* **Host Environment:** Windows 11 (WSL2 Virtualization Backend)

The identity server is deployed in an isolated development mode (`start-dev`) to allow local configuration testing before shifting to production-hardened clustering.

Core Security Controls Implemented
1. Multi-Factor Authentication (MFA)
Threat Target: Credential stuffing and compromised administrative sessions.

Configuration: Enforced global Time-Based One-Time Password (TOTP) tracking within the operational realm. New accounts are blocked from accessing resources until a cryptographic token is paired with an authenticator app.

2. Role-Based Access Control (RBAC) & Privilege Boundaries
Threat Target: Privilege creep and unauthorized lateral movement.

Configuration: Implemented strict least-privilege access control by mapping permissions to specific operational roles instead of individual users:

Security Group | Assigned Role | Access Level
SecOps-Admins	| Realm-Admin	 | Full control over client applications and token signing keys.
Compliance-Auditors |	Auditor-Read-Only |	Read-only access to system telemetry and auth logs.
General-Staff | Standard-User | Zero admin privileges; profile management only.

3. Identity Telemetry & Auditing
Threat Target: Undetected brute-force attempts and unmapped policy changes.

Configuration: Activated live system events logging. The engine records timestamped JSON data for every authentication lifecycle shift, creating a clean audit trail for SIEM parsing.

Deployment & Verification Steps
1. Navigate to the workspace directory: cd C:\enterprise-iam-labs
2. Initialize the container fabric: docker compose up -d
3. Verify active container runtime state: docker psdocker ps
4. Access the Identity Portal:
Open a browser and navigate to http://localhost:8080 (or local loopback http://127.0.0.1:8080) to access the Administration Console.

Engineering Logs & Troubleshooting
Incident: Docker API Connection Failure (500 Internal Server Error)
Symptom: Running deployment commands returned an API communication failure on route /_ping via named pipe //./pipe/dockerDesktopLinuxEngine. The container environment failed to construct.

Root Cause: The underlying Windows Subsystem for Linux (WSL2) virtualization engine layer was unmapped by the host OS daemon, leaving the Docker CLI without an active backend communication path.

Resolution: 1. Triggered a manual system installation and repair of the kernel layer using wsl --install.
2. Executed a full hardware host reboot to commit the virtualization changes.
3. Launched the Docker Desktop GUI app manually and waited for the engine health indicator to stabilize to solid Green (Running) before restarting the container deployment.
