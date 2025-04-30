# Static-Web-Host-Dev
Static-Web-Host-Developer-repo

This repository contains a GitHub Actions workflow that automatically triggers a workflow in another repository (`vijaya49/AWS-ECS-Infra`) using a `repository_dispatch` event whenever there is a `push` to the `main` branch.

## ✅ How It Works

When you push code to the `main` branch of this repository:

1. GitHub Actions workflow is triggered.
2. It sends a `POST` request to GitHub's API endpoint to dispatch an event to another repository (`Repo B`).
3. The event `trigger-docker-build` is sent using the `repository_dispatch` API.
4. The other repository (`vijaya49/AWS-ECS-Infra`) must have a workflow that listens to this event.

## 📂 Workflow File

```yaml
name: Trigger Repo B Workflow

on:
  push:
    branches:
      - main

jobs:
  trigger:
    runs-on: ubuntu-latest
    steps:
      - name: Trigger Repo B via repository_dispatch
        env:
          PAT: ${{ secrets.PERSONAL_ACCESS_TOKEN }}
        run: |
          curl -X POST \
            -H "Authorization: token $PAT" \
            -H "Accept: application/vnd.github.v3+json" \
            https://api.github.com/repos/vijaya49/AWS-ECS-Infra/dispatches \
            -d '{"event_type": "trigger-docker-build"}'
```

## 🔐 Secrets Required

Ensure you have the following secret added to your repository:

- `PERSONAL_ACCESS_TOKEN`: A [GitHub personal access token](https://github.com/settings/tokens) with `repo` scope to authorize the API request.

## 🛠️ Requirements in Repo B

In the target repository (`vijaya49/AWS-ECS-Infra`), there should be a workflow that listens to the dispatched event:

```yaml
on:
  repository_dispatch:
    types: [trigger-docker-build]
```

# Terraform Operations Workflow

This repository uses GitHub Actions to automate Terraform operations such as `plan`, `apply`, and `destroy`.

## Workflow Triggers

The workflow is defined in `.github/workflows/terraform.yml` and supports the following triggers:

- **Push to `main` branch** – Runs Terraform Plan.
- **Pull Requests** – Runs Terraform Plan.
- **Manual Dispatch (`workflow_dispatch`)** – Runs either `terraform apply` or `terraform destroy`, based on user input.

## Manual Trigger

To trigger a manual workflow (via the GitHub UI):

1. Navigate to the "Actions" tab.
2. Select **Terraform Operations** workflow.
3. Click **Run workflow**.
4. Choose the action:
   - `apply` – Applies the Terraform changes.
   - `destroy` – Destroys the Terraform-managed infrastructure.

> ⚠️ Only `apply` and `destroy` are accepted as inputs.

## Secrets Configuration

The workflow depends on the following GitHub secrets (set under repository settings → Secrets and variables → Actions):

- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`

These credentials should have appropriate permissions for managing AWS resources using Terraform.

## Workflow Jobs

### 1. Terraform Plan
Runs on every push to `main` or on a pull request.

Steps:
- Checks out the code.
- Configures AWS credentials.
- Sets up Terraform.
- Initializes Terraform.
- Formats and validates Terraform code.
- Generates an execution plan.

### 2. Terraform Apply
Triggered manually with `apply` input.

Steps:
- Checks out the code.
- Configures AWS credentials.
- Sets up Terraform.
- Initializes Terraform.
- Applies changes automatically with `-auto-approve`.

### 3. Terraform Destroy
Triggered manually with `destroy` input.

Steps:
- Checks out the code.
- Configures AWS credentials.
- Sets up Terraform.
- Initializes Terraform.
- Destroys infrastructure automatically with `-auto-approve`.

## Terraform Version

The workflow uses **Terraform 1.10.5**. You can change this version by editing the `terraform_version` input in the workflow file.

---



## 📘 Resources

- [GitHub Actions: repository_dispatch](https://docs.github.com/en/rest/repos/repos#create-a-repository-dispatch-event)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
