# Ansible Configuration Management for Azure
Repository for Ansible playbooks and roles to manage Azure resources.

## Features
- Connects to Windows via PSRP with NTLM authentication
- Uses azure.azcollection.azure_rm dynamic inventory plugin with OIDC (auth_source: auto)
- Applies various registry tweaks for Windows customization
- Implements Let's Encrypt certificate issuance for RDP listener *Functionality Broken - fix in progress*
- Script to remove Microsoft Edge
- CI/CD with GitHub Actions using Terraform Post-Apply Run Webhook
- Dynamic playbooks based on Azure Tags
- Uses Mitogen strategy for faster Ansible execution

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

### Notes
- azure_rm plugin requires ~/.ansible/collections/ansible_collections/azure/azcollection/requirements.txt which contains the specific versions of the Azure SDKs.
- [uv pip install azure-mgmt-batch --upgrade]
  This resolves the warning `SyntaxWarning: invalid escape sequence '\ '`
---