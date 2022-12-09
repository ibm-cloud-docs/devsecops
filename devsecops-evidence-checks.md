---

copyright:
  years: 2022
lastupdated: "2022-12-09"

keywords: DevSecOps, compliance evidence, evidence checks, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Evidence validation 
{: #evidence-checks-validation}

In the DevSecOps architecture, before deploying the assets, all the pieces of evidence that were collected in the CI pipeline must be validated so that the assets meet compliance requirements.
{: shortdesc}

Collecting evidence is an essential aspect of the DevSecOps reference architecture. The CI pipeline collects the pieces of evidence for all the scans or steps for the assets that are performed by the CI pipeline. The CD pipeline deploys the assets that are produced by the CI pipeline.

Validation of the evidence is categorized as follows:

- Pre-Deployment checks validate the evidence that is produced by the CI pipeline and gates the deployment based on these checks.
- Post-Deployment checks validate the evidence that is produced by the CD pipeline and evaluates the pipeline based on these checks

## Config file
{: #evidence-checks-config-file}

The config file contains the pre-deployment and post-deployment checks. Pre-deployment checks gate the deployment. If all the checks are validated successfully, the change request is auto-approved. If any of the checks fail, the change request is not auto-approved, and deployment is blocked. In the case of an `emergency` change request, the deployment is not blocked. The config file is part of the change request.

The finishing step validates the post-deployment evidence checks and evaluates the CD pipeline.
The checks are stored in the evidence locker next to the `summary.json` file.

For each asset type, there might be different evidence types that need to be collected. So in the checks, you can define the asset type and based on that, the evidence that is collected for a given tool:

### Pre deployment (checks that are done before the change request is auto-approved)
{: #evidence-checks-predep}

- Asset type (for example `image`, `commit`, `*`, or any asset)
   - Evidence
     - Evidence type id (for example `com.ibm.static-scan`)
       - Required (The evidence should present in the `success` state)
         - Tool (Tool type for which evidence is collected. For example, `SonarQube`, `owasp-zap`, `*`, or any tool)
       - Optional (if the evidence is present, then it must be in the `success` state)
         - Tool (Tool type for which evidence is collected. For example, `SonarQube`, `owasp-zap`, `*`, or any tool)
       - Ignore (evidence is not validated)

### Post deployment (checks that evaluate the CD pipeline)
{: #evidence-checks-postdep}

- Asset type (for example `image`, `commit`, `*`, or any asset)
   - Evidence 
     - Evidence type id (for example `com.ibm.acceptance_tests`)
       - Required (evidence must be present in the `success` state)
         - Tool (tool type, for example `jest`, `*`, or any tool)
       - Optional (if the evidence is present, then it should be in the `success` state)
         - Tool (tool type, for example `servicenow-v3` , `*`, or any tool)
       - Ignore (evidence is not validated)

To enable the `validation of evidence` in your toolchain, set the environment variable `opt-in-evidence-checks` to `1` in the CD toolchain.

To define the config file path, set `evidence-checks-config-path` to the file path present in the `pipeline-config-repo`, otherwise the default config file is used. For different deployment environments, there might be different config files. For example, `stage` might have evidence checks that differ from the production evidence checks. If `evidence-checks-config-path` is not defined, the config file searches for the file with the name `<region>.<target>.validation.json`, `<target>.validation.JSON`, or `validation.json` in the `pipeline-config-repo`.

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
