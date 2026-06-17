# Enterprise Linux Server Infrastructure: "PolMetal" Corporate Server
*A university project demonstrating practical Linux system administration, security, and automation skills applied to a simulated business environment.*

---

## Project Overview
This project showcases the end-to-end setup and configuration of an internal corporate server for a fictitious wholesale company, "PolMetal." Designed to replace an outsourced IT workflow, the infrastructure provides a secure, zero-maintenance environment for cross-departmental file sharing, system auditing, and disaster recovery. 

## Technologies & Tools Used
* **OS:** Linux (openSUSE Leap 15.6)
* **Scripting & Shell:** Bash, Command-Line Interface (CLI)
* **Access Management:** Standard Linux Permissions, Access Control Lists (ACLs), `sudoers`
* **Automation:** `cron`, `crontab`
* **Security:** SSH (Key-based Authentication), Audit Logging
* **Data Management:** `tar`, `gzip`

---

## Key Features & Business Value

### 1. Secure Identity Management & Role-Based Access (RBAC)
* **What was done:** Created a hierarchical user and group structure mirroring the company's departments (Sales, Marketing, Accounting, Board). 
* **Business Value:** Established secure file boundaries. Departments have strict isolation for their data, while the Executive Board maintains global oversight using advanced Access Control Lists (ACLs). A shared collaboration folder was implemented using the Sticky Bit (`chmod 1777`) to prevent accidental data deletion by unauthorized colleagues.

### 2. Automated Disaster Recovery & Backups
* **What was done:** Engineered a scheduled background job using `cron` to archive and compress (`tar.gz`) the entire corporate document repository to an external mount point every night at 2:00 AM.
* **Business Value:** Ensures business continuity and protects critical company assets against accidental deletion or corruption without requiring manual administrative intervention.

### 3. Secure Privilege Delegation (Principle of Least Privilege)
* **What was done:** Modified sudoers configuration via a secure drop-in file (`/etc/sudoers.d/`) to allow the Marketing team to run system patch updates (`zypper up`) without needing the root password.
* **Business Value:** Reduces IT bottlenecks and operational costs by safely delegating routine maintenance tasks to non-admin users, completely avoiding the security risks of sharing root credentials.

### 4. Continuous System Auditing & Compliance
* **What was done:** Developed automated monitoring scripts triggered every 5 minutes to dump active root processes and filter global logs (`/var/log/messages`) for SSH authentication attempts.
* **Business Value:** Prepares the infrastructure for internal audits and cybersecurity compliance by maintaining clear, isolated trails of privileged execution and remote access attempts.

### 5. Streamlined Remote Access
* **What was done:** Deployed the `sshd` service and configured passwordless, RSA key-pair authentication for executive oversight across employee environments.
* **Business Value:** Provides secure, frictionless remote access for authorized management to inspect system configurations and user environments safely.

---

## Key System Configuration Paths
*(Note: As this was a server configuration project, the implementation lives within Linux system files. Below is a summary of the configuration paths utilized:)*
* User/Group Definitions: `/etc/passwd`, `/etc/group`
* Custom Sudo Rules: `/etc/sudoers.d/marketing-zypper`
* Scheduled Tasks: `crontab` (root)
* Global Environment Variables: `/etc/profile.d/powitanie.sh`
* SSH Keys: `~/.ssh/authorized_keys`, `~/.ssh/id_rsa`
