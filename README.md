# Azure Automation using Ansible + Azure CLI (Step-by-Step Guide)

## Overview

This project documents the **complete working setup and steps** to use **Ansible with Azure CLI** to retrieve and manage Azure resources.

We use **Azure CLI with Ansible**, which is the simplest, most stable, and production-ready method.

This guide includes the **actual setup steps followed from the beginning**, excluding troubleshooting and package errors.

---

# Architecture

```
Azure VM (Ansible Control Node)
        │
        │ runs playbooks
        ▼
Azure CLI (az)
        │
        ▼
Azure Resource Manager (ARM)
        │
        ▼
Azure Resources (RG, VM, Storage, etc.)
```

---

# Step 1 — Create Ansible Control Node VM in Azure

We created an Azure Linux VM to act as:

**Ansible Control Node**

Example:

```
VM Name: vm-shubh-Ansible
OS: Ubuntu 22.04
```

Login to VM:

```bash
ssh azureuser@<public-ip>
```

---

# Step 2 — Install Ansible

Update system:

```bash
sudo apt update
```

Install pip:

```bash
sudo apt install python3-pip -y
sudo apt install python3.11-venv
python3 -m venv azure-venv
```

Install Ansible:

```bash
pip install ansible
```

Verify:

```bash
ansible --version
```

---

# Step 3 — Install Azure CLI

Install Azure CLI:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Verify:

```bash
az version
```

---

# Step 4 — Login to Azure

Run:

```bash
az login
```

This opens browser.

Login and return to terminal.

Verify:

```bash
az account show
```

---

# Step 5 — Create Project Folder

Create working directory:

```bash
mkdir ~/ansi-rg
cd ~/ansi-rg
```

---

# Step 6 — Create Inventory File

Create:

```bash
nano inventory
```

Content:

```
localhost ansible_connection=local
```

Save.

---

# Step 7 — Create Ansible Playbook to List Resource Groups

Create file:

```bash
nano list-rg-cli.yml
```

Content:

```yaml
---
- name: List Azure Resource Groups
  hosts: localhost
  connection: local
  gather_facts: no

  tasks:

    - name: Get Resource Groups
      command: az group list --query "[].name" -o tsv
      register: rg_output

    - name: Display Resource Groups
      debug:
        var: rg_output.stdout_lines
```

Save.

---

# Step 8 — Run Playbook

Execute:

```bash
ansible-playbook -i inventory list-rg-cli.yml
```

---

# Step 9 — Output

Example output:

```
TASK [Display Resource Groups]

ok: [localhost] =>

NetworkWatcherRG
rg-dr-mujahed
production-rg
```

This confirms:

Ansible successfully connected to Azure and retrieved resource groups.

---

# Additional Playbooks Created

---

# List Virtual Machines

File:

```
list-vm.yml
```

```yaml
- name: List Azure VMs
  hosts: localhost
  connection: local

  tasks:

    - command: az vm list --query "[].name" -o tsv
      register: vm_output

    - debug:
        var: vm_output.stdout_lines
```

---

# Create Resource Group

File:

```
create-rg.yml
```

```yaml
- name: Create Resource Group
  hosts: localhost
  connection: local

  tasks:

    - command:
        az group create
        --name ansible-rg-demo
        --location centralindia

      register: output

    - debug:
        var: output.stdout
```

---

# Stop Azure VM

```yaml
- name: Stop VM
  hosts: localhost
  connection: local

  tasks:

    - command:
        az vm stop
        --name vm-name
        --resource-group rg-name
```

---

# Project Structure

```
ansi-rg/
│
├── inventory
├── list-rg-cli.yml
├── list-vm.yml
├── create-rg.yml
└── README.md
```

---

# Authentication Method Used

Azure CLI stores login session in:

```
~/.azure/
```

Ansible uses this automatically.

No additional authentication required.

---

# Benefits of This Approach

✔ No dependency issues
✔ Simple setup
✔ Secure authentication
✔ Production ready
✔ Easy automation

---

# What We Successfully Achieved

We successfully:

• Installed Ansible
• Installed Azure CLI
• Connected Azure with Ansible
• Retrieved Resource Groups
• Retrieved VM names
• Created Resource Group

---

# Real-World Use Cases

This setup can automate:

• Infrastructure management
• Disaster Recovery
• Backup automation
• VM start / stop
• Resource reporting

---

# Recommended Best Practice

Always login before running playbooks:

```bash
az login
```

---

# Author

Azure Ansible Automation Lab

---

# Conclusion

Ansible with Azure CLI is the most reliable method to automate Azure resources without SDK dependency issues.

This setup provides a strong foundation for Azure automation.
