## EpicBook Application Deployment (Ansible)
## 📌 Overview

This repository contains the Ansible configuration for deploying the EpicBook application onto the infrastructure provisioned by Terraform.
It is separate from the infrastructure repo, this repo focuses only on configuration management and application deployment.

---

## 📂 Project Structure

ansible/
├── group_vars/
│   └── web.yml              # Database credentials and shared variables
│
├── roles/
│   ├── common/              # General setup tasks (update packages, install unzip, Python)
│   ├── nginx/               # Install and configure Nginx
│   │   ├── tasks/main.yml
│   │   ├── handlers/main.yml
│   │   └── templates/epicbook.conf.j2
│   └── epicbook/            # Deploy EpicBook app
│       ├── tasks/main.yml
│       ├── handlers/main.yml
│
├── inventory.ini            # VM IPs and SSH connection details
├── site.yml                 # Master playbook that applies all roles
└── ansible.cfg              # Ansible configuration (remote_user, private_key, etc.)

---

## 🚀 How It Works
1. Inventory
- inventory.ini defines the VM(s) provisioned by Terraform.

2. Roles
- common → Updates packages, installs unzip, Python, and other base tools.
- nginx → Installs and configures Nginx as the web server.
- epicbook → Clones the EpicBook repo, installs Node.js/npm, and starts the app.

3. Variables
- group_vars/web.yml stores database credentials and other shared variables.
- These are referenced inside roles for configuration.

4. Playbook
- site.yml ties everything together:

  yaml
  - hosts: epicbook
    become: yes
    roles:
      - common
      - nginx
      - epicbook

---

## 🛠️ Usage
1. Ensure your VM is provisioned by the infra pipeline and reachable via SSH.
2. Update inventory.ini with the VM’s public IP.
3. Run the playbook locally:
      ansible-playbook -i inventory.ini site.yml
4. The pipeline can also run this automatically:
    - It captures the VM IP from the infra stage.
    - Downloads the SSH private key from Library Secrets.
    - Installs Ansible and runs the playbook against the VM.

---

## ✅ Notes
- This repo is only for Ansible/app deployment.
- Infrastructure provisioning (Terraform, storage backend, etc.) is handled in the infra repo.
- Keep secrets (like DB credentials) in group_vars or Azure DevOps Library, not hardcoded in playbooks.
