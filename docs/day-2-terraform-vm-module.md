Day 2 – Terraform VM Module (Proxmox)
1. Objective

The objective of Day 2 was to design and implement a reusable Terraform module to provision virtual machines on Proxmox using a previously created Golden VM Template.

This step transitions the project from manual infrastructure preparation to fully automated Infrastructure as Code (IaC).

2. Scope

During Day 2, the following was implemented:

A reusable Terraform module to create Proxmox VMs

Environment-specific configuration using variables and tfvars

Provider source correction and version pinning

Validation of Terraform initialization and module wiring

Preparation for downstream automation (Ansible, Kubernetes)

No application workloads were deployed at this stage.

3. Design Principles

The Terraform design follows these principles:

Module-based architecture – reusable and scalable

Separation of concerns – module vs environment

No hardcoding – all values parameterized

Golden Image usage – immutable infrastructure

Promotion-safe IaC practices

4. Directory Structure
terraform/
├── modules/
│   └── proxmox-vm/
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
└── environments/
    └── prod/
        ├── provider.tf
        ├── main.tf
        ├── variables.tf
        └── terraform.tfvars

Structure Rationale

modules/ contains reusable infrastructure blueprints

environments/ contains environment-specific instantiations

The same module can later be reused for dev, staging, or test

5. Terraform Module: proxmox-vm
5.1 Purpose

The proxmox-vm module defines how a single VM is created on Proxmox.

It does not define:

Whether the VM is a Kubernetes master or worker

How many VMs exist

Which environment it belongs to

Those decisions are made at the environment level.

5.2 Module Inputs (variables.tf)

The module exposes inputs for:

VM identity (name, ID)

Proxmox node placement

Golden template ID

CPU, memory, and disk sizing

Storage backend

Network configuration

cloud-init IP configuration

This makes the module flexible and reusable without modification.

5.3 Provider Declaration Inside Module

The module explicitly declares the Proxmox provider source:

terraform {
  required_providers {
    proxmox = {
      source  = "Telmate/proxmox"
      version = "~> 2.9"
    }
  }
}

Why This Is Required

Terraform assumes hashicorp/* providers by default.
Since Proxmox is maintained by Telmate, the provider source must be declared explicitly in every module that uses it.

This prevents provider resolution errors during terraform init.

5.4 VM Resource Definition (main.tf)

The core resource used is:

resource "proxmox_vm_qemu" "this"


Key behaviors:

Clones the VM from the Golden Template

Enables cloud-init

Configures CPU, memory, disk, and network

Enables QEMU guest agent support

Uses lifecycle rules to avoid unnecessary VM recreation

This maps directly to Proxmox VM behavior while remaining declarative.

5.5 Lifecycle Management
lifecycle {
  ignore_changes = [
    network,
  ]
}


This prevents Terraform from recreating VMs due to:

DHCP lease changes

MAC address updates

This is a production-grade safeguard.

5.6 Module Outputs (outputs.tf)

The module exposes:

VM name

VM ID

These outputs will later be used to:

Generate Ansible inventories

Bootstrap Kubernetes clusters

Improve automation visibility

6. Environment Configuration: prod
6.1 Purpose

The prod environment defines which VMs are created using the module.

This includes:

Kubernetes master nodes

Kubernetes worker nodes

Supporting infrastructure

6.2 Environment Variables (variables.tf)

Environment-level variables define:

Target Proxmox node

Storage backend

This allows infrastructure placement to change without modifying the module.

6.3 Environment Values (terraform.tfvars)
target_node = "dell-pve"
storage     = "sas-storage"


These are real, environment-specific values and are intentionally isolated from reusable code.

6.4 Module Usage (main.tf)

Each VM is created by calling the module:

module "k8s_master" {
  source = "../../modules/proxmox-vm"
  ...
}


Creating multiple VMs simply means reusing the same module with different inputs.

This demonstrates true Infrastructure as Code reuse.

7. Terraform Initialization & Validation
7.1 Initialization
terraform init


This performs:

Provider download and verification

Module initialization

Backend preparation

No infrastructure changes occur during this step.

7.2 Validation
terraform validate


This confirms:

Syntax correctness

Variable wiring

Module integrity

8. VM Lifecycle Management

Virtual machines are provisioned using Terraform but may be stopped when not in use to conserve resources.

Terraform manages existence and configuration

Proxmox manages runtime power state

Stopping VMs does not impact Terraform state or IaC integrity.

9. Outcome

At the end of Day 2:

A reusable Terraform module for Proxmox VMs exists

Infrastructure provisioning is fully automated

Golden Template usage is enforced

Environment separation is established

The platform is ready for Ansible and Kubernetes bootstrap

10. Day 2 Completion Summary

Day 2 successfully transitioned the project from infrastructure preparation to Infrastructure as Code.

The Terraform implementation demonstrates:

Modular design

Provider correctness

Production-safe lifecycle handling

Promotion-ready DevOps practices

✅ Day 2 Status: Completed
🔒 Commit Recommendation
git add docs/day-2-terraform-vm-module.md
git commit -m "Day 2: document Terraform VM module and environment design"
git push

🧠 Promotion Soundbite (Use This)

“I implemented a reusable Terraform module to provision Proxmox VMs from a Golden Image, separated environment concerns, fixed provider resolution issues, and validated the infrastructure lifecycle end to end.”
