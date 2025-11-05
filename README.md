# Lightnet CI Templates

Repository containing reusable GitHub Actions workflows for Lightnet projects.

## 📋 Available Workflows

### Build, Push & Deploy to Azure Container Apps (ACA)
**File:** `.github/workflows/build-deploy-aca.yml`

This workflow performs:
- Build Docker image
- Push image to Azure Container Registry (ACR)
- Deploy to Azure Container Apps with new revision

### Security Scan Workflows

#### Dependency Check
**File:** `.github/workflows/dependency-check.yml`

Scans dependencies for known security vulnerabilities using OWASP Dependency Check.

#### Semgrep SAST Scan
**File:** `.github/workflows/semgrep-scan.yml`

Performs static application security testing (SAST) using Semgrep.

#### Trivy Container Scan
**File:** `.github/workflows/trivy-scan.yml`

Scans Docker images for security vulnerabilities using Trivy.

## 🚀 Usage

### Deploy to ACA

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

### Security Scans

```yaml
name: Security Scans

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 3 * * 1'  # Weekly

jobs:
  dependency-check:
    uses: lightnet/lightnet-ci-templates/.github/workflows/dependency-check.yml@main
    with:
      project_name: "MyProject"
      scan_path: "./src"
      product_type: "Web Application"
    secrets:
      DOJO_USERNAME: ${{ secrets.DOJO_USERNAME }}
      DOJO_PASSWORD: ${{ secrets.DOJO_PASSWORD }}

  semgrep-scan:
    uses: lightnet/lightnet-ci-templates/.github/workflows/semgrep-scan.yml@main
    with:
      project_name: "MyProject"
      semgrep_config: "p/owasp-top-ten"
      product_type: "Web Application"
    secrets:
      DOJO_USERNAME: ${{ secrets.DOJO_USERNAME }}
      DOJO_PASSWORD: ${{ secrets.DOJO_PASSWORD }}

  trivy-scan:
    uses: lightnet/lightnet-ci-templates/.github/workflows/trivy-scan.yml@main
    with:
      project_name: "MyProject"
      product_type: "Web Application"
    secrets:
      DOJO_USERNAME: ${{ secrets.DOJO_USERNAME }}
      DOJO_PASSWORD: ${{ secrets.DOJO_PASSWORD }}
```

## 📝 Parameters

### Build & Deploy Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| `service_name` | ✅ | Service name (e.g. iremit-admin) |
| `container_rg_name` | ✅ | Resource group of container app |
| `registry_dev` | ✅ | ACR name for DEV environment |
| `registry_stg` | ✅ | ACR name for STG environment |
| `registry_prod` | ✅ | ACR name for PROD environment |
| `custom_tag` | ❌ | Custom tag (e.g. hotfix-001) |

### Security Scan Parameters

| Parameter | Required | Default | Description |
|-----------|----------|---------|-------------|
| `project_name` | ✅ | - | Project name for DefectDojo |
| `scan_path` | ❌ | `./` | Path to scan (dependency-check) |
| `semgrep_config` | ❌ | `p/owasp-top-ten` | Semgrep ruleset |

| `dockerfile_path` | ❌ | `./Dockerfile` | Path to Dockerfile |
| `dojo_url` | ❌ | `https://dojo.devfocus.site/` | DefectDojo URL |
| `product_type` | ❌ | `Web Application` | DefectDojo product type |

## 🔐 Secrets

### Build & Deploy Secrets

| Secret | Required | Description |
|--------|----------|-------------|
| `AZURE_CREDENTIALS` | ✅ | Azure service principal credentials |

### Security Scan Secrets

| Secret | Required | Description |
|--------|----------|-------------|
| `DOJO_USERNAME` | ✅ | DefectDojo username |
| `DOJO_PASSWORD` | ✅ | DefectDojo password |
| `DEFECTDOJO_API_KEY` | ❌ | DefectDojo API key (Semgrep only) |

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

Check the `examples/` directory for usage examples:

- `usage-aca.yml` - Basic ACA deployment
- `security-scans.yml` - Security scans only
- `complete-pipeline.yml` - Full CI/CD with security + deploy
- `pr-security-only.yml` - PR security checks only

## 📞 Support

Contact DevOps team for support.