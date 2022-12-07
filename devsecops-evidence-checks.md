---


copyright:
  years: 2022
lastupdated: "2022-12-07"

keywords: DevSecOps, compliance evidence, evidence checks, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Evidence Validation 
{: #devsecops-evidence-validation}

Collecting evidence is one of the essential aspects of the DevSecOps reference architecture. In the CI pipeline, we collect the pieces of evidence for all the scans/steps for the assets performed by the CI pipeline. In the CD pipeline, we deploy the assets which were produced by the CI pipeline.
In the DevSecOps architecture, before deploying the assets, all the pieces of evidence that were collected in the CI pipeline need to be validated as if the asset meets the compliance requirement.
Validation of the evidence is categorized into two categories:
  - Pre-Deployment checks, validate the evidence produced by the CI pipeline and will gate the deployment based on these checks
  - Post-Deployment checks, validate the evidence produced by the CD pipeline and will evaluate the pipeline based on these checks
{: shortdesc}

## Config File
{: #evidence-checks-config-file}

The config file refers to the checks which will have the pre-deployment and post-deployment checks. Pre-deployment checks will gate the deployment, if all the checks are validated successfully, it will auto-approve the change request and if any of the checks failed, it will not auto-approve the change request and will block the deployment. In case of an `emergency`, it won't block the deployment. This config file will be part of the change request.
At the finishing step, it will validate the post-deployment evidence checks and evaluate the cd pipeline.
This will be stored in the evidence locker right next to the `summary.json` file.

For each asset type, there could be different evidence that needs to be collected. So in the checks, we can define the asset type and based on that evidence which needs to be collected for which tool.

- Pre Deployment (checks which will be done before the change request is auto-approved)
  - Asset Type (asset type like image, commit, “*” -> anything)
  - Evidences 
    - Evidence type id (eg. com.ibm.static-scan)
      - Required (The evidence should present in the success state)
        - Tool (Tool type for which evidence should be collected like SonarQube, owasp-zap, “*” -> anything )
      - Optional (if the evidence is present, then it should be in a success state)
        - Tool (Tool type for which evidence should be collected like owasp-zap-ui, “*” -> anything )
      - Ignore (will not be validated)

- Post Deployment(checks which will be done to evaluate the cd pipeline)
  - Asset Type (asset type like image, commit, “*” -> anything)
  - Evidences 
    - Evidence type id (eg. com.ibm.acceptance_tests)
      - Required (evidence should be present in the success state)
        - Tool (tool type like jest, “*” -> anything )
      - Optional (if the evidence is present, then it should be in a success state)
        - Tool (tool type like servicenow-v3 , “*” -> anything )
      - Ignore (will not be validated)

To enable the `validation of evidence` in your toolchain, set the environment variable `opt-in-evidence-checks` to `1` in the CD toolchains.
To define the config file path, set `evidence-checks-config-path` to the file path present in the `pipeline-config-repo` or it will use the default config file. For different deployment environments, there could be different config files, like stage can have some different evidence checks which differ from the production evidence checks. If `evidence-checks-config-path` is not defined, it will search for the file with the name `<region>.<target>.validation.json` or `<target>.validation.JSON` or `validation.json` in the `pipeline-config-repo`.
### Sample Config File
{: #config-file}

```
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
