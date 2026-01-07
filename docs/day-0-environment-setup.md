1. Objective
The goal of Day 0 is to prepare a reproducible, secure, and automation-ready local DevOps workstation that will act as the control plane for:
•	Infrastructure provisioning (Terraform)
•	VM configuration (Ansible)
•	Kubernetes operations (kubectl & Helm)
•	CI/CD interactions
•	GitOps workflows
This ensures that all infrastructure and platform changes are API-driven, version-controlled, and repeatable, without manual UI intervention.
________________________________________
2. Scope
This setup was performed on:
•	Operating System: Ubuntu Server (20.04 / 22.04)
•	Target Platform: Proxmox (existing hypervisor)
•	Provisioning Model: Local Terraform execution against Proxmox API
________________________________________
3. Design Principles Followed
•	Infrastructure as Code (IaC): No manual VM creation
•	Immutable & Declarative Approach: State defined in code, not clicks
•	Security First: Token-based API access, SSH keys
•	Production Parity: Tools and versions aligned with real-world environments
•	Version Control: Everything tracked in Git from Day 0
________________________________________
4. System Preparation
Before installing any tools, the system was updated to ensure:
•	Latest security patches
•	Compatibility with official vendor repositories
sudo apt update && sudo apt upgrade -y
Essential system packages were installed to support secure package management and CLI tooling:
sudo apt install -y \
  ca-certificates \
  curl \
  gnupg \
  lsb-release \
  software-properties-common
________________________________________
5. Installed Tooling & Rationale
5.1 Git – Source of Truth
Git is used as the single source of truth for:
•	Terraform configurations
•	Ansible playbooks
•	Kubernetes manifests
•	Documentation
Installation:
sudo apt install -y git
Global configuration:
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
Reasoning:
All infrastructure and platform changes must be traceable, reviewable, and revertible.
________________________________________
5.2 Terraform – Infrastructure as Code
Terraform is used to provision and manage Proxmox virtual machines via API.
Official HashiCorp repository was added to ensure:
•	Trusted binaries
•	Predictable upgrades
•	Enterprise-grade support
Installation:
sudo apt install -y terraform
Verification:
terraform version
Reasoning:
Terraform allows infrastructure to be defined declaratively, enabling reproducibility and eliminating configuration drift.
________________________________________
5.3 Ansible – Configuration Management
Ansible is used to:
•	Bootstrap virtual machines
•	Install Kubernetes prerequisites
•	Apply OS-level configurations consistently
Installation:
sudo apt install -y ansible
Verification:
ansible --version
Reasoning:
Terraform provisions infrastructure; Ansible configures it. This clean separation improves maintainability.
________________________________________
5.4 kubectl – Kubernetes CLI
kubectl is the primary interface for:
•	Cluster administration
•	Workload deployment
•	Debugging and operations
Installation:
curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
Verification:
kubectl version --client
Reasoning:
Using the official Kubernetes release ensures compatibility with upstream clusters.
________________________________________
5.5 Helm – Kubernetes Package Manager
Helm is used to:
•	Deploy complex applications (Ingress, Monitoring, GitOps tools)
•	Manage versioned Kubernetes releases
Installation:
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
Verification:
helm version
Reasoning:
Helm provides standardized, repeatable Kubernetes deployments and simplifies lifecycle management.
________________________________________
5.6 Docker – Container Runtime (Local)
Docker is used locally for:
•	Building container images
•	Testing CI/CD pipelines
•	Debugging application behavior
Installation:
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker
Non-root access:
sudo usermod -aG docker $USER
newgrp docker
Verification:
docker run hello-world
Reasoning:
Local container tooling ensures faster feedback loops during development.
________________________________________
5.7 Utility Tools (jq, yq, tree)
These tools support:
•	JSON/YAML parsing
•	Script automation
•	Repository readability
Installation:
sudo apt install -y jq tree net-tools
sudo snap install yq
________________________________________
6. Verification & Validation
A verification script was created to confirm tool availability:
terraform version
ansible --version
kubectl version --client
helm version
docker --version
git --version
This ensures the environment is ready for infrastructure provisioning and automation.
________________________________________
7. Repository Structure (Initial)
The project repository was initialized with a clean, scalable structure:
devops-proxmox-platform/
├── terraform/
│   ├── modules/
│   └── environments/
├── ansible/
│   ├── roles/
│   └── inventory/
├── kubernetes/
├── docs/
All changes are committed to Git from Day 0 to ensure traceability.
________________________________________
8. Outcome
At the end of Day 0:
•	The local environment is fully prepared for IaC-driven infrastructure provisioning
•	All tools required for Kubernetes, CI/CD, and GitOps workflows are installed
•	The foundation is set for a production-grade DevOps platform build
________________________________________
9. Next Steps
The next phase focuses on secure access and automation enablement:
•	SSH key-based authentication
•	Proxmox API token configuration
•	Terraform provider validation
________________________________________
10. Tooling Verification & Version Validation

After completing the Day 0 installation steps, all required tooling was validated to ensure compatibility with modern DevOps and Kubernetes workflows.

10.1 Verified Tool Versions
Terraform v1.14.3
Platform: linux_amd64


Validation:
Terraform v1.14.x is a current stable release and supports:

Modern provider SDKs

Remote state backends

Module-based infrastructure design

This version is suitable for long-term infrastructure lifecycle management.

Ansible [core 2.16.3]
Python version: 3.12.3
Jinja version: 3.1.2
libyaml: True


Validation:
Ansible 2.16.x is aligned with:

Python 3.12 (latest stable)

Modern collections-based architecture

Improved performance and security patches

This ensures reliable VM bootstrapping and OS-level configuration management.

kubectl Client Version: v1.35.0
Kustomize Version: v5.7.1


Validation:
The kubectl client version supports:

Newer Kubernetes APIs

Advanced debugging features

Native Kustomize integration

Client version independence allows safe interaction with slightly older or newer clusters.

Helm Version: v3.19.4
GitCommit: 7cfb6e486dac026202556836bb910c37d847793e
GoVersion: go1.24.11


Validation:
Helm 3.19.x supports:

OCI registries

Advanced chart dependency management

Secure, Tiller-less architecture (Helm v3+)

This is suitable for deploying production-grade Kubernetes components.

Docker version 28.2.2


Validation:
Docker 28.x includes:

Security fixes

Improved container runtime performance

Compatibility with modern CI/CD pipelines

Used primarily for local builds, testing, and pipeline validation.

git version 2.43.0


Validation:
Git 2.43.x supports:

Modern branching strategies

Improved performance on large repositories

Secure credential handling

All infrastructure and platform code is version-controlled from Day 0.

11. Compatibility Assessment

All installed tools meet the following criteria:

✅ Compatible with Kubernetes 1.28+ clusters

✅ Supported by upstream vendors

✅ Suitable for production environments

✅ Aligned with GitOps and IaC best practices

No version mismatches or deprecated tooling were identified.

12. Day 0 Completion Summary

At the conclusion of Day 0:

The local environment is fully prepared for Infrastructure as Code execution

Tooling versions are validated and documented

The system is ready to securely interact with the Proxmox API

The foundation for Kubernetes, CI/CD, and GitOps workflows is established

✅ Day 0 Status: Completed & Verified



