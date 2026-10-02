# deekayen.splunk_enterprise

[![CI](https://github.com/deekayen/ansible-role-splunk-enterprise/actions/workflows/ci.yml/badge.svg)](https://github.com/deekayen/ansible-role-splunk-enterprise/actions/workflows/ci.yml) [![Ansible Galaxy](https://img.shields.io/badge/galaxy-deekayen.splunk__enterprise-blue.svg)](https://galaxy.ansible.com/ui/standalone/roles/deekayen/splunk_enterprise/) [![Project Status: Inactive – The project has reached a stable, usable state but is no longer being actively developed; support/maintenance will be provided as time allows.](https://www.repostatus.org/badges/latest/inactive.svg)](https://www.repostatus.org/#inactive) ![0BSD license](https://img.shields.io/badge/license-0BSD-blue)

An Ansible role that installs Splunk Enterprise on a single Windows Server host with an initial admin account and adds the Splunk `bin` directory to the system `PATH`.

The role runs `ansible.windows.win_package` against the MSI at `splunk_download_url`, so the target downloads the installer itself. The MSI runs with `AGREETOLICENSE=Yes`, `INSTALLDIR`, `SPLUNKUSERNAME`, `SPLUNKPASSWORD`, and `/quiet`. A second task adds `<splunk_home_dir>\bin` to `PATH` with `ansible.windows.win_path`. The Galaxy name uses an underscore (`deekayen.splunk_enterprise`), while the repository name uses hyphens.

## Requirements

- ansible-core 2.15 or newer on the controller.
- The `ansible.windows` collection: `ansible-galaxy collection install ansible.windows`.
- A WinRM or SSH connection to the target with administrative rights.
- Outbound HTTPS from the target to the host in `splunk_download_url`, `download.splunk.com` by default.

## Supported platforms

| Platform | Versions |
| --- | --- |
| Windows | 2016, 2019, 2022, 2025 |

CI runs `ansible-lint` and `ansible-playbook --syntax-check` only.

## Installation

From Ansible Galaxy:

```bash
ansible-galaxy role install deekayen.splunk_enterprise
ansible-galaxy collection install ansible.windows
```

Or pin it in `requirements.yml`:

```yaml
---
roles:
  - name: deekayen.splunk_enterprise
    src: https://github.com/deekayen/ansible-role-splunk-enterprise.git
    scm: git
    version: main

collections:
  - name: ansible.windows
```

```bash
ansible-galaxy install -r requirements.yml
```

## Role variables

| Variable | Default | Description |
| --- | --- | --- |
| `splunk_download_url` | `https://download.splunk.com/products/splunk/releases/9.1.1/windows/splunk-9.1.1-64e843ea36b1-x64-release.msi` | URL of the Splunk Enterprise MSI. Must start with `http://` or `https://` and end in `.msi`. See [Known issues](#known-issues) before changing the version. |
| `splunk_home_dir` | `C:\Program Files\Splunk` | Installation directory, passed as `INSTALLDIR`. Its `bin` subdirectory is added to `PATH`. |
| `splunk_admin_user` | `""` | Initial admin user name. Required; the role fails when it is empty. |
| `splunk_admin_pass` | `""` | Initial admin password. Required, at least 8 characters, with no double quotes. Store it in Ansible Vault or pull it from a lookup; the install task sets `no_log: true` because the MSI arguments contain it. |

## Behavior

- Running the role accepts the Splunk license on your behalf through `AGREETOLICENSE=Yes`.
- `win_package` checks the fixed `product_id` before downloading. Once that product is installed, the role skips the install, so it does not upgrade Splunk or change the admin credentials on later runs.
- The URL check requires the URL to end in `.msi`, so a URL with a query string, such as a presigned object storage link, fails validation.

## Dependencies

None. The `ansible.windows` collection is a requirement, not a role dependency.

## Example playbook

```yaml
---
- name: Install Splunk Enterprise.
  hosts: splunk_indexers

  vars:
    splunk_admin_user: admin
    splunk_admin_pass: "{{ vault_splunk_admin_pass }}"

  roles:
    - deekayen.splunk_enterprise
```

`vault_splunk_admin_pass` is a placeholder for a variable defined in an Ansible Vault file.

## Tags

| Tag | Tasks |
| --- | --- |
| `always` | Input validation in `tasks/assert.yml`. |
| `install` | The MSI install and the `PATH` update. |
| `msi` | The MSI install. |
| `path` | The `PATH` update. |

## Known issues

- `tasks/main.yml` hardcodes `product_id: '{3736D415-5488-4A28-896A-E2BCFDCCE757}'`, which `meta/argument_specs.yml` describes as the ID of the default 9.1.1 package. Changing `splunk_download_url` to another release leaves that ID in place, so the installed-product check still looks for the 9.1.1 product.

## Development

CI runs on every push to `main` and every pull request (see `.github/workflows/ci.yml`). It installs `ansible.windows`, runs `ansible-lint --profile production`, and syntax-checks `tests/test.yml` with placeholder credentials. To run the same checks locally:

```bash
pip3 install ansible-lint
ansible-galaxy collection install ansible.windows
ansible-lint --profile production
mkdir -p .ansible/roles && ln -sfn "$PWD" .ansible/roles/deekayen.splunk_enterprise
ANSIBLE_ROLES_PATH=.ansible/roles:~/.ansible/roles ansible-playbook --syntax-check tests/test.yml -i tests/inventory
```

The repository also has a `.pre-commit-config.yaml`; run `pre-commit run --all-files` before pushing.

### Repository layout

| Path | Purpose |
| --- | --- |
| `tasks/main.yml` | Input validation, MSI install, and `PATH` update. |
| `tasks/assert.yml` | Checks the admin credentials and the MSI URL. |
| `defaults/main.yml` | Every user-facing variable. |
| `meta/argument_specs.yml` | Argument types and descriptions. |
| `tests/` | Syntax-check playbook and inventory used by CI. |

## Releases

Pushing a git tag runs `.github/workflows/release.yml`, which imports the tagged commit into Ansible Galaxy as `deekayen.splunk_enterprise`. The import needs a `GALAXY_API_KEY` repository or organization secret.

## License

0BSD (BSD Zero Clause). See [LICENSE](LICENSE).

## Authors

[Po-temkin](https://github.com/Po-temkin) wrote the original [splunk-windows-ansible](https://github.com/Po-temkin/splunk-windows-ansible). [David Norman](https://github.com/deekayen) forked it and converted it to a generic role. Sponsorship links are in [.github/FUNDING.yml](.github/FUNDING.yml).
