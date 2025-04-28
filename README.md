# Ansible Configuration Management for Azure
Repository for Ansible playbooks and roles to manage Azure resources.

## Features
- Uses Mitogen strategy for faster Ansible execution
- Connects to Windows via PSRP with NTLM authentication
- Uses azure.azcollection.azure_rm dynamic inventory plugin with OIDC (auth_source: auto)
- Applies various registry tweaks for Windows customization
- Implements Let's Encrypt certificate issuance for RDP listener
- Customizes Firefox startup via syspin pinning
- Automates Microsoft Edge uninstallation
- Supports CI/CD workflows (GitHub Actions, uv for dependency management)
- Includes handlers for automatic reboot after registry updates

### Azure Dynamic Inventory Authentication (OIDC)
- When using the `azure_rm` dynamic inventory plugin in CI/CD (GitHub Actions), set `auth_source: auto` in `inventory/azure_rm.yml`.
- The `azure/login` action with OIDC (federated credentials) authenticates Azure CLI, but the Ansible inventory plugin will only work if `auth_source: auto` is set.
- Auth order: ENV > CLI
- OIDC does not use `AZURE_SECRET`.

### Environment Variables:
```bash
export AZURE_CLIENT_ID
export AZURE_SECRET
export AZURE_TENANT
export AZURE_SUBSCRIPTION_ID
export ANSIBLE_PASSWORD
```

### Authentication
- The Windows password should be stored as a GitHub Actions secret named `ANSIBLE_PASSWORD`.
- The secret must be exposed as an environment variable in the workflow step that runs Ansible:
- In Ansible variable files, the following pattern is used to retrieve the password from the environment:
  ```yaml
  ansible_password: "{{ lookup('env', 'ANSIBLE_PASSWORD') }}"
  ```

### Workflow Trigger
- GitHub Actions workflow is triggered by TF Cloud Run Task using webhook.

---