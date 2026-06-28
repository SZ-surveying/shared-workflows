# Shared Workflows

A collection of reusable GitHub Actions workflows and actions designed to simplify CI/CD processes across multiple projects.

## 📦 Contents

### Workflows

#### 1. Release Images

Path: `.github/workflows/release-images.yml`

Builds one or more Docker images from a release tag, pushes version and
`sha-<commit>` tags to GHCR, resolves immutable digests, and optionally sends a
`component_image_release` dispatch payload to the integration repository.

Caller example:

```yaml
jobs:
  release-images:
    uses: SZ-surveying/shared-workflows/.github/workflows/release-images.yml@main
    with:
      component: backend
      project-repository: ${{ vars.PROJECT_REPOSITORY }}
      images: |
        [
          {
            "key": "backend",
            "name": "surveying-backend",
            "title": "Surveying Backend API",
            "description": "Go HTTP API service for the surveying platform.",
            "documentation": "https://github.com/SZ-surveying/surveying-backend/blob/main/docs/images.md#surveying-backend",
            "context": ".",
            "file": "services/backend/Dockerfile"
          }
        ]
    secrets:
      project_dispatch_token: ${{ secrets.PROJECT_DISPATCH_TOKEN }}
```

Image fields:

| Field          | Required | Description                                      |
| -------------- | -------- | ------------------------------------------------ |
| `key`          | Yes      | Payload key, for example `backend` or `portal`. |
| `name`         | Yes*     | Image name under GHCR namespace.                |
| `repository`   | No       | Full image repository; overrides `name`.        |
| `context`      | No       | Docker build context, default `.`.              |
| `file`         | No       | Dockerfile path, default `Dockerfile`.          |
| `buildArgs`    | No       | Multiline Docker build args.                    |
| `portalClient` | No       | Resolve latest portal client and add secrets.   |
| `title`        | No       | OCI image title; defaults to image name/key.    |
| `description`  | No       | OCI image description.                          |
| `source`       | No       | OCI image source URL; defaults to repository.   |
| `url`          | No       | OCI image URL; defaults to source.              |
| `documentation` | No      | OCI image documentation URL.                    |
| `licenses`     | No       | OCI image license expression.                   |

`name` is required unless `repository` is provided.

The workflow writes standard OCI labels for every image, including title,
source, URL, version, revision, ref name, and creation time. Optional image
fields add description, documentation, and license labels.

Optional secrets:

- `project_dispatch_token`: sends release candidate dispatch.
- `npm_token`: required when an image uses `portalClient: true`.
- `portal_vite_env`: optional Vite env block for portal builds.

#### 2. Release Helm Chart

Path: `.github/workflows/release-helm.yml`

Packages a Helm chart from a `release-helm/<semver>` tag, merges the existing
Helm repo `index.yaml`, publishes to `gh-pages` with historical files retained,
and optionally sends a `component_chart_release` dispatch payload.

Caller example:

```yaml
jobs:
  release-chart:
    uses: SZ-surveying/shared-workflows/.github/workflows/release-helm.yml@main
    with:
      component: backend
      chart-dir: charts/surveying-backend
      repo-url: https://${{ github.repository_owner }}.github.io/${{ github.event.repository.name }}
      project-repository: ${{ vars.PROJECT_REPOSITORY }}
      app-version-images: |
        [
          "surveying-backend",
          "surveying-python-service"
        ]
    secrets:
      project_dispatch_token: ${{ secrets.PROJECT_DISPATCH_TOKEN }}
```

The workflow validates tag format, version monotonicity, `Chart.yaml` version,
`Chart.yaml` appVersion, and that the tag commit is already on `origin/main`.

### Actions

#### 1. Extract Tag Version

Path: `actions/extract-tag-version/`

Extracts and validates semantic version numbers (SemVer) from Git tags, ensuring proper format and merge status to the main branch.

**Features:**

- Validates tags against SemVer format
- Checks if tag commit is merged to main branch
- Outputs version without `v` prefix
- Customizable SemVer regex pattern

**Usage:**

```yaml
- name: Extract Tag Version
  uses: <your-org>/shared-workflows/actions/extract-tag-version@main
  id: extract_version
  with:
    semver-regex: "^v([0-9]+)\\.([0-9]+)\\.([0-9]+)(?:-([0-9A-Za-z.-]+))?(?:\\+([0-9A-Za-z.-]+))?$"
    main-branch: "main"

- name: Use extracted version
  run: echo "Version is ${{ steps.extract_version.outputs.version }}"
```

**Input Parameters:**

| Parameter      | Type   | Required | Default               | Description                    |
| -------------- | ------ | -------- | --------------------- | ------------------------------ |
| `semver-regex` | string | No       | Standard SemVer regex | Regex pattern to validate tags |
| `main-branch`  | string | No       | `main`                | Main branch name               |

**Outputs:**

| Name      | Description                          |
| --------- | ------------------------------------ |
| `version` | Extracted version without `v` prefix |

**Validation Rules:**

- Tags must follow SemVer format (e.g., `v1.0.0`, `v1.0.0-alpha.1`)
- Tag commit must be merged to the specified main branch
- If validation fails, the action will exit with an error message

## 🚀 Quick Start

1. Reference workflows or actions from this repository in your repository
2. Configure input parameters as needed
3. Ensure your GitHub token has the necessary permissions

## 📋 Prerequisites

- GitHub Actions must be enabled
- For version extraction: Tags must follow SemVer format

## 📝 Examples

For complete usage examples, please refer to the documentation sections for each workflow and action.

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
