# Lightnet CI Templates

Repository containing reusable GitHub Actions workflows for Lightnet projects.

## 📋 Available Workflows

### Build, Push & Deploy to Azure Container Apps (ACA)
**File:** `.github/workflows/build-deploy-aca.yml`

This workflow performs:
- Build Docker image
- Push image to Azure Container Registry (ACR)
- Deploy to Azure Container Apps with new revision

## 🚀 Usage

```yaml
name: Deploy to ACA

on:
  push:
    branches: [develop, beta, main]

jobs:
  deploy:
    uses: lightnet/lightnet-ci-templates/.github/workflows/build-deploy-aca.yml@main
    with:
      service_name: "your-service-name"
      container_rg_name: "your-container-rg"
      registry_dev: "yourregistry.azurecr.io"
      registry_stg: "yourregistry.azurecr.io"
      registry_prod: "yourregistry.azurecr.io"
      custom_tag: "" # Optional
    secrets:
      AZURE_CREDENTIALS: ${{ secrets.AZURE_CREDENTIALS }}
```

## 📝 Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| `service_name` | ✅ | Service name (e.g. iremit-admin) |
| `container_rg_name` | ✅ | Resource group of container app |
| `registry_dev` | ✅ | ACR name for DEV environment |
| `registry_stg` | ✅ | ACR name for STG environment |
| `registry_prod` | ✅ | ACR name for PROD environment |
| `custom_tag` | ❌ | Custom tag (e.g. hotfix-001) |

## 🔐 Secrets

| Secret | Required | Description |
|--------|----------|-------------|
| `AZURE_CREDENTIALS` | ✅ | Azure service principal credentials |

## 🌍 Environment Mapping

- `develop` branch → DEV environment
- `beta` branch → STG environment  
- `main` branch → PROD environment

## 🏷️ Tagging Strategy

- **DEV:** `dev-{short-sha}`
- **STG:** `stg-{short-sha}`
- **PROD:** `v{semantic-version}` (uses semver.yaml)
- **Custom:** Use `custom_tag` parameter

## 📋 Requirements

1. **Dockerfile** in repository root
2. **semver.yaml** for semantic versioning (PROD only)
3. **Azure credentials** configured in repository secrets

### Example semver.yaml:

```yaml
version: 1.0.0
versioning:
  dev_branch: develop
  release_branch: main
  tag_prefix: v
```

## 📁 Examples

Check the `examples/` directory for usage examples and configuration files.

## 📞 Support

Contact DevOps team for support.