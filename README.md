# NetasCode NX-OS CI/CD Automation

This repository enables automated, version-controlled deployment of Cisco NX-OS VXLAN EVPN configurations using **NetasCode**, **Ansible**, and **GitHub Actions**.

---

## 📁 Folder Structure

```
csv/                    # Source of truth (manually maintained)
nac-configs/            # YAML generated for NetasCode (auto)
templates/              # Jinja2 templates for rendering YAML
playbooks/
  Create/               # add_<type>.yml
  Update/               # update_<type>.yml
  Remove/               # remove_<type>.yml
  cleanup_removed_entries.yml  # Cleans up processed rows
.github/
  workflows/
    nac-deploy.yml      # CI/CD logic
```

---

## 🚀 CI/CD Workflow

### ✅ Trigger

The workflow only runs when files under `csv/` are modified:

```yaml
on:
  push:
    paths:
      - 'csv/*.csv'
  workflow_dispatch:
```

---

## 📌 CI/CD Jobs

### 1. `detect-changes`
- Checks which CSV files were modified
- Sets flags to drive conditional execution

### 2. `deploy`
- Runs only playbooks for changed CSVs:
  - `add_<type>.yml`
  - `update_<type>.yml`
  - `remove_<type>.yml`
- Cleans up CSVs afterward:
  - Converts `add`/`update` → `live`
  - Deletes rows marked `remove`

### 3. `csv-diff`
- Generates and logs a diff between previous and current CSV content

---

## 🧠 Example CSV (`vlans.csv`)

```csv
action,id,name,vrf,svi_ip
add,10,WEB,PROD,10.10.10.1/24
update,20,DB,PROD,10.10.20.100/24
remove,30,MGMT,INFRA,10.1.1.1/24
```

After a successful run:
- `add`/`update` entries are marked `live`
- `remove` entries are deleted from the file

---

## 📥 Manual Execution

To run locally:

```bash
ansible-playbook playbooks/Create/add_vlans.yml
ansible-playbook playbooks/cleanup_removed_entries.yml
```

To trigger manually in GitHub:
- Go to Actions tab
- Select `NetasCode CI/CD Optimized`
- Click “Run workflow”

---

## 👥 Maintainers
- Created for Chris Nwamarah

