# Shared Workflows

A collection of reusable GitHub Actions workflows and actions designed to simplify CI/CD processes across multiple projects.

## 📦 Contents

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
