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

## 📘 Resources

- [GitHub Actions: repository_dispatch](https://docs.github.com/en/rest/repos/repos#create-a-repository-dispatch-event)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)