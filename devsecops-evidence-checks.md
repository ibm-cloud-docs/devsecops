---

copyright: 
  years: 2022, 2023
lastupdated: "2023-10-30"

keywords: DevSecOps, compliance evidence, evidence checks, IBM Cloud, Security and Compliance Center

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Validating evidence
{: #evidence-checks-validation}

In the DevSecOps architecture, before deploying the assets, all the pieces of evidence that were collected in the CI pipeline must be validated so that the assets meet compliance requirements.
{: shortdesc}

Collecting evidence is an essential aspect of the DevSecOps reference architecture. The CI pipeline collects the pieces of evidence for all the scans or steps for the assets that are performed by the CI pipeline. The Continuous Deployment (CD) pipeline deploys the assets that are produced by the CI pipeline and Continuous Compliance (CC) pipeline scans the asset deployed by the CD pipeline.

You can validate the evidence in one of the following ways:

1. Using the Security and Compliance Center (SCC) profile.
2. Using a config file.

## Using the SCC profile
{: #evidence-checks-scc}

Use SCC to embed security checks into your everyday workflows to monitor for security and compliance. By monitoring for risks, you can identify security vulnerabilities, work to mitigate the impact, and fix the issues. The toolchain must have SCC integration and `Use profile with attachment` enabled, based on the SCC profile and version. All controls are validated based on the collected evidence for all the resources in the attachment. The toolchain supports both the {{site.data.keyword.cloud}} Security Best Practices with version `1.0.0` or above or the {{site.data.keyword.cloud}} for Financial Services with version `1.2.0` or above profile. If you want a subset of specified controls, create a custom profile using these profiles and select a subset of controls. The toolchain is validated against that profile. For more information, see [Security and Compliance Center](/docs/ContinuousDelivery?topic=ContinuousDelivery-scc).

## Using a config file
{: #evidence-checks-config-file}

Validating evidence by using a config file works as follows as follows:

- Pre-deployment checks validate the evidence that is produced by the CI pipeline and gates the deployment based on these checks. If all the checks are validated successfully, the change request is auto-approved. If any of the checks fail, the change request is not auto-approved and deployment is blocked. In the case of an `emergency` change request, the deployment is not blocked. The config file is part of the change request.
- Post-deployment checks validate the evidence that is produced by the CD pipeline and evaluates the pipeline based on these checks.
- The finishing step validates the post-deployment evidence checks and evaluates the CD pipeline. The checks are stored in the evidence locker beside the `summary.json` file.

For each asset type, different evidence types might need to be collected. So, in the checks you can define the asset type and based on that asset, the evidence that is collected for a tool is as follows:

### Pre-deployment (checks that are done before the change request is auto-approved)
{: #evidence-checks-predep}

- Asset type (for example `image`, `commit`, `*`, or any asset)
   - Evidence
     - Evidence type id (for example `com.ibm.static-scan`)
       - Required (The evidence presents in the `success` state)
         - Tool (Tool type for which evidence is collected. For example, `SonarQube`, `owasp-zap`, `*`, or any tool)
       - Optional (if the evidence is present, then it must be in the `success` state)
         - Tool (Tool type for which evidence is collected. For example, `SonarQube`, `owasp-zap`, `*`, or any tool)
       - Ignore (evidence is not validated)

### Post-deployment (checks that evaluate the CD pipeline)
{: #evidence-checks-postdep}

- Asset type (for example `image`, `commit`, `*`, or any asset)
   - Evidence 
     - Evidence type id (for example `com.ibm.acceptance_tests`)
       - Required (evidence must be present in the `success` state)
         - Tool (tool type, for example `jest`, `*`, or any tool)
       - Optional (if the evidence is present, then it must be in the `success` state)
         - Tool (tool type, for example `servicenow-v3`, `*`, or any tool)
       - Ignore (evidence is not validated)

### Enable evidence validation and evaluation using config file
{: #enable-evidence-checks-config-file}

To enable the `validation of evidence` in your toolchain, set the environment variable `opt-in-evidence-checks` to `1` in the CD and CC toolchain.



Pipeline evaluation will also occur when the check is enabled in both the CD and CC, utilizing the configuration file.

### Configure config file
{: #configure-evidence-checks-config-file}

To define the config file path, set `evidence-checks-config-path` to the file path present in the `pipeline-config-repo`, otherwise the default config file is used. Different deployment environments might have different config files. For example, `stage` might have evidence checks that differ from the production evidence checks. If `evidence-checks-config-path` is not defined, the config file searches for the file with the name `<region>.<target>.validation.json`, `<target>.validation.JSON`, or `validation.json` in the `pipeline-config-repo`.

### Sample config file
{: #evidence-checks-samp-config-file}

```cmd
{
  "pre-deployment": [
    {
      "asset_type": "commit",
      "evidences": [
        {
          "evidence_type_id": "com.ibm.branch_protection",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.peer_review",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.unit_tests",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.static_scan",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.detect_secrets",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.code_vulnerability_scan",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.code_cis_check",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.code_bom_check",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.acceptance_tests",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        }
      ]
    },
    {
      "asset_type": "image",
      "evidences": [
        {
          "evidence_type_id": "com.ibm.cloud.image_signing",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.cloud.image_vulnerability_scan",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.dynamic_scan",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.cloud.verify_signature",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        }
      ]
    },
    {
      "asset_type": "generic",
      "evidences": [
        {
          "evidence_type_id": "com.ibm.pipeline_run_data",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.pipeline_logs",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        }
      ]
    },
    {
      "asset_type": "*",
      "evidences": [
        {
          "evidence_type_id": "com.ibm.code-branch-protection",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.peer_review",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.unit_tests",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.static_scan",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.detect_secrets",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.code_vulnerability_scan",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.code_cis_check",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.code_bom_check",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.cloud.image_signing",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.acceptance_tests",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.cloud.image_vulnerability_scan",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.dynamic_scan",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.cloud.verify_signature",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        }
      ]
    }
  ],
  "post-deployment": [
    {
      "asset_type": "image",
      "evidences": [
        {
          "evidence_type_id": "com.ibm.prod_change_request",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.acceptance_tests",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        }
      ]
    },
    {
      "asset_type": "generic",
      "evidences": [
        {
          "evidence_type_id": "com.ibm.pipeline_run_data",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.pipeline_logs",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        }
      ]
    },
    {
      "asset_type": "commit",
      "evidences": [
        {
          "evidence_type_id": "com.ibm.acceptance_tests",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        }
      ]
    },
    {
      "asset_type": "*",
      "evidences": [
        {
          "evidence_type_id": "com.ibm.prod_change_request",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        },
        {
          "evidence_type_id": "com.ibm.acceptance_tests",
          "rules": {
            "required": [],
            "optional": [
              {
                "tool": "*"
              }
            ],
            "ignore": []
          }
        }
      ]
    }
  ]
}

```

### Default tool value for the verious stages in the DevSecOps
{: #default-supported-tool-in-evidence-checks-config-file}

| Evidence type ID  | Default supported tool |
|:----------|:------------------------------|
|`com.ibm.branch_protection` | `cocoa-branch-protection` |
|`com.ibm.unit_tests` | `jest` |
|`com.ibm.detect_secrets` | `detect-secrets` |
|`com.ibm.code_vulnerability_scan` | For apps: `cra-tf`, `cra`, `mend` </br>For infrastructure as code: `tfsec`, `checkov`|
|`com.ibm.code_bom_check` | `cra-bom`, `sbom-utility` |
|`com.ibm.code_cis_check` | `cra-cis` |
|`com.ibm.peer_review` | `peer-review` |
|`com.ibm.static_scan` | For apps: `sonarqube`, `gosec` </br> For infrastructure as code: `terraform-fmt`, `terraform-validate`, `tflint` |
|`com.ibm.cloud.image_signing` | `artifact-signing` |
|`com.ibm.acceptance_tests` | `jest` |
|`com.ibm.dynamic_scan` | `owasp-zap`, `owasp-zap-ui` |
|`com.ibm.cloud.image_vulnerability_scan` | `va`, `sysdig`, `xray` |
|`com.ibm.prod_change_request` | `servicenow-v3`, `gitlab` |
|`com.ibm.close_change_request` | `servicenow-v3`, `gitlab` |
{: caption="Table 1. Supported tool for evidence" caption-side="top"}
