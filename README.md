# Ansible Configuration Management for Azure
Repository for Ansible playbooks and roles to manage Azure resources.

## Resolved Issues & Important Notes

### Azure Dynamic Inventory Authentication (OIDC)
- When using the `azure_rm` dynamic inventory plugin in CI/CD (GitHub Actions), set `auth_source: auto` in `inventory/azure_rm.yml`.
- The `azure/login` action with OIDC (federated credentials) authenticates Azure CLI, but the Ansible inventory plugin will only work if `auth_source: auto` is set.
- Auth order: ENV > CLI
- OIDC does not use `AZURE_SECRET`.

### Authentication
- The Windows password should be stored as a GitHub Actions secret named `ANSIBLE_PASSWORD`.
- The secret must be exposed as an environment variable in the workflow step that runs Ansible:
  ```yaml
  env:
    ANSIBLE_PASSWORD: ${{ secrets.ANSIBLE_PASSWORD }}
  ```
- In Ansible variable files, the following pattern is used to retrieve the password from the environment:
  ```yaml
  ansible_password: "{{ lookup('env', 'ANSIBLE_PASSWORD') }}"
  ```

### Using uv in CI/CD
- The `uv` tool is used for fast Python dependency installs. In CI/CD, use `uv pip install --system -r requirements.txt` to avoid venv errors.
- Add `$HOME/.cargo/bin` to `$GITHUB_PATH` to call `uv`.

### Workflow Triggers TODO: automate tf workflow
- To trigger Ansible after Terraform completes, use `workflow_run` (monorepo) or `repository_dispatch` (multi-repo) triggers.

---

This workflows enables local execution via WSL or Github Actions.

**Keep this section updated as new issues are resolved or new patterns are adopted!**
- Azure CLI x