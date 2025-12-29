
### Praxis 

This project demonstrates a **complete DevOps lifecycle** for deploying **OpenWebUI** on Microsoft Azure — starting from **infrastructure provisioning using Terraform**, followed by **configuration management**, **containerization**, and **fully automated CI/CD using Jenkins**.

The setup reflects **real-world production practices**, not a demo or tutorial-only workflow.

---

## 🔁 High-Level Flow

```
Terraform → Azure VM
        ↓
Ansible → VM Configuration
        ↓
Docker → Application Container
        ↓
Jenkins → CI/CD Automation
        ↓
Docker Hub → Image Registry
        ↓
Azure VM → Production Deployment
```

---

## 🧱 Phase 1 – Infrastructure Provisioning (Terraform)

### Objective

Provision all Azure infrastructure **from scratch** using Terraform.

### Tools

* Terraform
* Azure CLI
* Azure Resource Manager (ARM)

### Resources Created

* Resource Group
* Virtual Network
* Subnet
* Network Security Group (NSG)
* Public IP
* Network Interface
* Linux Virtual Machine (Ubuntu 22.04)

### Key Concepts

* Infrastructure as Code (IaC)
* Idempotent deployments
* Declarative provisioning

### Steps

1. Authenticate with Azure:

   ```bash
   az login
   ```

2. Create a Service Principal for Terraform:

   ```bash
   az ad sp create-for-rbac --role Contributor --scopes /subscriptions/<SUB_ID>
   ```

3. Export credentials:

   ```bash
   export ARM_CLIENT_ID=...
   export ARM_CLIENT_SECRET=...
   export ARM_TENANT_ID=...
   export ARM_SUBSCRIPTION_ID=...
   ```

4. Initialize and apply Terraform:

   ```bash
   terraform init
   terraform plan
   terraform apply
   ```

Result:
✅ Azure VM with public IP and secured networking.

---

## ⚙️ Phase 2 – VM Access & Base Setup

### Objective

Prepare the VM for automation and container workloads.

### Steps

* SSH into VM using key-based authentication
* Verify ports:

  * `22` → SSH
  * `80` → HTTP
  * `443` → HTTPS
  * `3000` → OpenWebUI
  * `8081` → Jenkins

Result:
✅ VM ready for configuration management.

---

## 🛠️ Phase 3 – Configuration Management (Ansible)

### Objective

Automate VM configuration and application setup.

### What Ansible Handles

* Package installation
* Docker installation
* Docker service management
* Environment file setup
* Persistent volume setup
* Container execution

### Why Ansible

* No agent required
* Repeatable and predictable
* Ideal for single-node servers

Result:
✅ OpenWebUI runs in Docker with persistent storage.

---

## 📦 Phase 4 – Containerization (Docker)

### Objective

Run OpenWebUI as a containerized service.

### Key Details

* Docker image: OpenWebUI (custom build later via CI)
* Persistent data mounted at:

  ```
  /opt/openwebui-data
  ```
* Environment variables stored securely on the VM

Result:
✅ Application survives restarts and redeployments.

---

## 🌐 Phase 5 – Domain, Nginx & SSL

### Objective

Expose OpenWebUI securely over HTTPS.

### Steps

* Domain mapped via DNS (A record → VM public IP)
* Nginx configured as reverse proxy
* SSL enabled (HTTPS)

Result:
✅ OpenWebUI accessible via custom domain over HTTPS.

---

## 👥 Phase 6 – Multi-User Control (Application Level)

### Objective

Enable and manage multiple users securely.

### Features

* Admin and user separation
* Default model control
* System-level prompts enforced
* Users cannot override admin policies

Result:
✅ Controlled multi-user environment.

---

## 🔁 Phase 7 – CI/CD Automation (Jenkins)

### Objective

Achieve **fully automated CI/CD** on every production push.

---

### 🔹 CI (Continuous Integration)

Triggered on push to `master` branch.

**Pipeline tasks:**

1. Clone GitHub repository
2. Build Docker image
3. Tag image (`latest`)
4. Push image to Docker Hub
5. Cleanup build cache

---

### 🔹 CD (Continuous Deployment)

After CI succeeds:

1. Jenkins SSHs into Azure VM
2. Executes deployment script
3. Pulls latest Docker image
4. Replaces running container
5. Rolls back automatically if failure occurs

Deployment script location:

```
/opt/openwebui/deploy.sh
```

---

## 🔐 Security Measures

* SSH key-based authentication only
* `known_hosts` enforced (no insecure SSH flags)
* Secrets stored outside GitHub
* Jenkins credentials stored securely
* No hardcoded tokens in pipeline

---

## ♻️ Rollback Strategy

If deployment fails:

* Old container is preserved
* New container is removed
* Previous version is restored automatically

Result:
✅ No downtime, no manual intervention.

---

## 🌿 Branch Strategy

| Branch   | Purpose     | CI/CD |
| -------- | ----------- | ----- |
| `dev`    | Development | ❌     |
| `master` | Production  | ✅     |

Only `master` triggers deployment.

---

## 📌 What This Project Proves

* Terraform-based Azure provisioning
* Real CI/CD (not manual builds)
* Secure Jenkins automation
* Docker image lifecycle management
* Production rollback handling
* End-to-end DevOps ownership

---

## 🚀 Future Enhancements

* Blue-Green deployment
* Monitoring & alerts
* Secrets via Azure Key Vault
* AKS migration

---

## 👤 Author

**Dharshan Kumar**
Built as a hands-on, production-focused DevOps implementation.

---
