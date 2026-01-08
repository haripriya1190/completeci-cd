### Disk Import & Attachment Note

When using directory-based Proxmox storage, imported disks are referenced using the format:

<storage>:<vmid>/<disk-file>

After importing the Ubuntu cloud image, the disk was attached using its full storage path rather than LVM-style volume naming. This ensures compatibility with directory-backed storage backends.


### SSH Key Injection for cloud-init

The cloud-init SSH key must be accessible from the Proxmox host. The public key generated on the DevOps control node was securely copied to the Proxmox host and referenced during template configuration.

This avoids unnecessary key duplication while maintaining centralized access control.


## Day 1 Completion Summary

The Ubuntu 22.04 Golden VM was successfully validated and converted into a reusable Proxmox template.

The template includes:
- cloud-init–based user and SSH key provisioning
- QEMU guest agent for lifecycle and network introspection
- Kubernetes prerequisites (swap disabled)
- Storage on dedicated SAS-backed Proxmox storage

This Golden Template is now used as the immutable base image for all infrastructure VMs provisioned via Terraform.
