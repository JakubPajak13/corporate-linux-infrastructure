# Server Deployment Runbook

This runbook documents the sequence of commands used to provision the infrastructure, configure Role-Based Access Control (RBAC), and apply advanced file permissions for the PolMetal corporate server.

### 1. Identity & Access Management
Create the departmental groups and assign user accounts. The `-m` flag ensures a home directory is created, and `-g` assigns the primary group.

```bash
# Create groups
groupadd sales
groupadd marketing
groupadd accounting
groupadd executive

# Create users and assign to primary groups
useradd -m -g sales mdlugowas
useradd -m -g sales rpiana
useradd -m -g marketing jrolka
useradd -m -g marketing akrotka
useradd -m -g accounting bskarbowa
useradd -m -g accounting hwatowska
useradd -m -g executive jwazny
```

### 2. Directory Structure & Base Permissions
Create the foundational directories and apply standard Linux group ownership and permissions (`chmod 770`).

```bash
# Generate directory tree
mkdir -p /documents/{sales,marketing,accounting,executive,shared}

# Apply departmental ownership and strip 'other' permissions
chgrp sales /documents/sales
chmod 770 /documents/sales

chgrp marketing /documents/marketing
chmod 770 /documents/marketing

chgrp accounting /documents/accounting
chmod 770 /documents/accounting

# Executive (Strict isolation)
chown jwazny /documents/executive
chmod 700 /documents/executive

# Shared Workspace (Sticky Bit applied to prevent accidental deletion by peers)
chmod 1777 /documents/shared
```

### 3. Advanced Access Control Lists (ACLs)
Standard permissions are insufficient for the Executive department's requirement to have global read/write access. We use ACLs to grant the CEO (`jwazny`) explicit access across all departmental folders, setting default (`-d`) rules for future files.

```bash
# Sales Data Access
setfacl -m u:jwazny:rwx /documents/sales
setfacl -d -m u:jwazny:rwx /documents/sales
setfacl -d -m g:sales:rwx /documents/sales

# Marketing Data Access
setfacl -m u:jwazny:rwx /documents/marketing
setfacl -d -m u:jwazny:rwx /documents/marketing
setfacl -d -m g:marketing:rwx /documents/marketing

# Accounting Data Access
setfacl -m u:jwazny:rwx /documents/accounting
setfacl -d -m u:jwazny:rwx /documents/accounting
setfacl -d -m g:accounting:rwx /documents/accounting
```

### 4. Secure Remote Access (SSH Directory Permissions)
To allow passwordless access for the CEO (`jwazny`), an RSA keypair was generated. The public key is distributed to all employee accounts using a loop, enforcing strict `chmod` directory permissions to satisfy SSH security requirements.

```bash
# Store the CEO's public key in a variable
PUB_KEY=$(cat /home/jwazny/.ssh/id_rsa.pub)

# Distribute key and enforce strict SSH directory permissions
for employee in mdlugowas rpiana jrolka akrotka bskarbowa hwatowska; do
    sudo -u $employee mkdir -p /home/$employee/.ssh
    sudo -u $employee chmod 700 /home/$employee/.ssh
    
    echo "$PUB_KEY" | sudo -u $employee tee -a /home/$employee/.ssh/authorized_keys > /dev/null
    sudo -u $employee chmod 600 /home/$employee/.ssh/authorized_keys
done
```

### 5. Log Accessibility
Create a symbolic link to the main system log and use ACLs to grant the CEO read-only access without compromising the file's primary security.

```bash
# Create symbolic link in the CEO's home directory
ln -s /var/log/messages /home/jwazny/system_logs

# Grant explicit read permission via ACL
setfacl -m u:jwazny:r /var/log/messages
```
