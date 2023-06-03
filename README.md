# Homelab: Ansible Playbook for Buildkite Agents

[![Build status](https://img.shields.io/github/actions/workflow/status/muhlba91/homelab-ansible-buildkite-agent/pipeline.yml?style=for-the-badge)](https://github.com/muhlba91/homelab-ansible-buildkite-agent/actions/workflows/pipeline.yml)
[![License](https://img.shields.io/github/license/muhlba91/homelab-ansible-buildkite-agent?style=for-the-badge)](LICENSE.md)

This repository contains the Ansible playbook for deploying and configuring [Buildkite agents](https://buildkite.com/docs/agent/v3), including all required packages.

---

## Running the Playbook

A valid `inventory.yml` file needs to be generated/supplied containing all server hosts, and settings, like UFW rules.

You can then run the playbook similar to:

```bash
ansible-playbook -i inventory/inventory.yml --extra-vars ansible_ssh_private_key_file=inventory/ssh.key site.yml
```

---

## Roles

### AWS CLI

Installs the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html).

### Buildkite

Install and configures the Buildkite agents.

Example configuration:

```yaml
buildkite_agent_version: 3.42.0-4984
buildkite_environment: dev
buildkite_ssh_key: ssh
buildkite_organizations:
  - name: org
    spawn: 1
    token: token
    doppler:
      token: token
    minio:
      bucket: bucket
      endpoint: endpoint
      region: region
      access_key: access_key
      secret_access_key: secret_access_key
    gcp:
      bucket: bucket
      key: endpoint
      access_key: access_key
      secret_access_key: secret_access_key
buildkite_additional_env:
  - name: BUILDKITE_PLUGIN_DOCKER_PROPAGATION_LIST
    value: GCS_ACCESS_KEY_ID GCS_SECRET_ACCESS_KEY
```

### Docker

Installs [Docker](http://docker.com).

Example configuration:

```yaml
os_release: bullseye
```

### Doppler

Installs the [Doppler CLI](https://docs.doppler.com/docs/cli).

Example configuration:

```yaml
os_release: bullseye
```

### GCP CLI

Installs the [Google Cloud CLI](https://cloud.google.com/sdk/docs/install).

---

## Triggering a Deploy

A deploy ca be triggered by dispatching the [`deploy.yml`](.github/workflows/deploy.yml) workflow.

### Inputs

- `s3_asset_bucket`: S3 Bucket and Path to the inventory to be downloaded and used

### Example Trigger using GitHub Actions

```yaml
- name: Deploy Services
  uses: benc-uk/workflow-dispatch@v1
  with:
    workflow: deploy.yml
    repo: muhlba91/homelab-ansible-buildkite-agent
    inputs: '{ "s3_asset_bucket": "bucket/path" }'
    token: ${{ secrets.GITHUB_PAT_TOKEN }}
```

---

## Continuous Integration and Automations

- [GitHub Actions](https://docs.github.com/en/actions) are linting the playbook and roles.
- [Renovate Bot](https://github.com/renovatebot/renovate) is updating Ansible and Python dependencies, and GitHub Actions.
