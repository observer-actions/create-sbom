# SBOM Observer Scan and Upload

This GitHub Action facilitates the creation of a Software Bill of Materials (SBOM) and its subsequent upload to [SBOM Observer](https://sbom.observer/). This action can be used to scan either a filesystem path or a container image to generate the SBOM, which is then sent to the specified SBOM Observer namespace.

## Inputs



| Input                         | Description                                                                                                    | Required | Default           |
|-------------------------------|----------------------------------------------------------------------------------------------------------------|----------|-------------------|
| `token`                       | The API token for authenticating with the SBOM Observer.                                                       | Yes      | N/A               |
| `namespace`                   | The namespace within SBOM Observer to which the SBOM will be uploaded.                                         | No       | `'default'`       |
| `name`                        | Override the Name field for the top level component in the SBOM.                                               | No       | N/A               |
| `version`                     | Override the Version field for the top level component in the SBOM.                                            | No       | N/A               |
| `group`                       | Override the Group field for the top level component in the SBOM.                                              | No       | N/A               |
| `path`                        | The filesystem path to analyze for generating the SBOM. Either `path` or `image` should be provided, not both. | No       | N/A               |
| `image`                       | The container image to analyze for generating the SBOM. Either `path` or `image` should be provided, not both. | No       | N/A               |
| `retention-policy`            | Retention policy to optionally apply after importing the SBOM into SBOM Observer.                              | No       | N/A               |
| `retention-keep`              | Retention policy configuration: Keep the last N attestations (per {name, type, tags} set).                     | No       | N/A               |
| `retention-keep-dependencies` | Retention policy configuration: Keep attestations for components that have incoming dependencies.              | No       | N/A               |
| `sbom`                        | Name of the SBOM file generated.                                                                               | No       | `'sbom.cdx.json'` |


## Outputs

| Output           | Description                                      |
|------------------|--------------------------------------------------|
| `attestation-id` | ID for the attestation created in SBOM Observer. |
| `status`         | Status of the SBOM upload to SBOM Observer.      |

These outputs can be used in subsequent steps of your GitHub Actions workflow. 

## Usage

Below is an example workflow that uses the SBOM Observer Scan and Upload action:

```yaml
name: Generate and Upload SBOM

on:
  push:
    branches:
      - main

jobs:
  sbom-upload:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: SBOM Observer Scan and Upload
        uses: observer-actions/create-sbom@v1
        id: create-sbom
        with:
          token: ${{ secrets.SBOM_OBSERVER_TOKEN }}
          path: ./
          namespace: my-namespace
          name: my-component
          version: 1.0.0
          group: my-group
          
      - name: Status
        shell: bash
        run: |
          echo ${{ steps.create-sbom.outputs.status }}
          echo ${{ steps.create-sbom.outputs.attestation-id }}

```

In this example, when code is pushed to the `main` branch, the workflow checks out the code and then runs the SBOM Observer Scan and Upload action. The SBOM is generated from the filesystem at the root of the repository (`./`), and it is then uploaded to the SBOM Observer under the specified namespace, name, version, and group.

## Notes

- Ensure that the `token` input is stored securely as a GitHub Secret.

## Support

For support, please open an issue in [this GitHub repository](https://github.com/observer-actions/create-sbom/issues).

