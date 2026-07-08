---

copyright:
  years: 2026, 2026
lastupdated: "2026-07-08"

keywords: DevSecOps, OPA, Open Policy Agent, Rego, deployment gating, evidence validation, compliance, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Deployment Gating using OPA
{: #devsecops-evidence-checks-opa}

In the DevSecOps architecture, evidence collected during the CI pipeline must be validated before deployment to ensure assets meet compliance requirements. This validation process acts as a quality gate, preventing non-compliant assets from being deployed to stage or production environments.
{: shortdesc}

For more information about evidence gating, see [Validating evidence](/docs/devsecops?topic=devsecops-evidence-checks-validation).

## Introducing Open Policy Agent (OPA)
{: #introducing-opa}

To provide greater flexibility, the pipeline now supports deployment gating by using Open Policy Agent (OPA). OPA is an open-source, general-purpose policy engine that decouples policy decision-making from pipeline execution. It allows you to define compliance rules as code, making evaluations highly customizable and completely transparent. OPA uses Rego as its query language, making it easy to write and maintain policies.

For more information about Open Policy Agent, see [Open Policy Agent](https://www.openpolicyagent.org/){: external}.

## What is Rego?
{: #what-is-rego}

Rego is the native, declarative query language that is used by OPA. It allows you to write your "policy-as-code." Instead of relying on hardcoded pipeline logic, you evaluate the pipeline's evidence payload and return a decision.

In the context of deployment gating, you can think of Rego as the filter between your collected evidence and your target environment. Instead of relying on rigid pipeline logic, Rego takes the pipeline's evidence payload (formatted as JSON) and evaluates it against your custom rules to return a definitive deployment decision.

For more information about Rego, see [The Rego Policy Language](https://www.openpolicyagent.org/docs/policy-language){: external}.

### The `allow` Rule Standard
{: #allow-rule-standard}

As a standard practice, your Rego policy file must include an `allow` rule. In the context of this pipeline, this is not just a suggestion—it is a strict requirement.

The pipeline engine specifically queries the `allow` boolean to make the final Go/No-Go deployment decision. If you are providing a custom Rego policy, you must explicitly define this rule. If the `allow` rule is missing, or if it evaluates to `false`, the pipeline immediately halts, blocks the deployment, and outputs your defined deny messages.

To ensure maximum security, OPA operates on a strict "fail-closed" model. Your policy should always default to `false` and only evaluate to `true` if all your required compliance conditions are met.

```rego
package one_pipeline.config

# Default deny
default allow := false

# Allow only if your custom checks pass
allow {
    check_sast_scans_passed
    check_secrets_scans_passed
}
```
{: codeblock}

## Enabling OPA in Your Pipeline
{: #enabling-opa-pipeline}

To start using OPA for deployment gating, you need to configure specific properties (environment variables) in your pipeline settings. Use the table below to determine which properties are required for your specific pipeline type:

| Requirement | Pipeline Property | Purpose |
| :--- | :--- | :--- |
| **Enable Gating (Promotion)** | `opt-in-evidence-checks` | Turns on the compliance gate in Promotion validation pipelines. |
| **Enable OPA (CD & CC)** | `opt-in-rego-translation` | Instructs Continuous Deployment (CD) and Continuous Compliance (CC) pipelines to use the new OPA logic. |
| **Define Logic (The Rules)** | `policy-path` | Points the pipeline engine to your custom `.rego` policy files. |
| **Provide Data (The Facts)** | `evidence-checks-config-path` | Points to your static data validation `.json` files. |


### Policy Files and Data Validation Files
{: #policy-data-validation-files}

**The Policy File (.rego) - The Logic**

This is where you write your actual rules and conditions. For example, your policy might dictate: *"Do not allow this deployment unless a vulnerability scan was completed, and the results show zero critical issues."* You can provide your custom policy file by using the `policy-path` pipeline environment property. If no policy file is provided, the pipeline uses the default policy file.

**The Data Validation File (.json) - The Facts**

This file acts as your static reference list. Instead of hardcoding data directly into your Rego rules, you use this file to maintain lists of approved variables—like your allowed image registries (`us.icr.io` or `eu.icr.io`) or specific repositories that are exempt from certain checks. You can provide your data validation file by using the `evidence-checks-config-path` pipeline environment property. If no data validation file is provided, the pipeline uses the default data validation file.

Example data validation file:

```rego
        package one_pipeline.config
        pre_deployment := {
            "com.ibm.prod_change_request": {
                "asset_type": [
                "*",
                "image"
                ],
                "source": [
                "prod",
                "stage"
                ],
                "target": [
                "prod",
                "stage"
                ],
                "recommended": [
                "*"
                ]
            }
        }
        post_deployment := {
            "com.ibm.prod_change_request": {
                "asset_type": [
                "*",
                "image"
                ],
                "source": [
                "prod",
                "stage"
                ],
                "target": [
                "prod",
                "stage"
                ],
                "recommended": [
                "*"
                ]
            }
        }
```

Each block in the configuration defines exactly what evidence is expected, where it comes from, and how strictly it should be enforced.

* **The Evidence Type ID (`com.ibm.prod_change_request`):** The top-level key dictates the specific category of evidence being evaluated. Common examples include `com.ibm.static-scan`, `com.ibm.unit-tests`, or `com.ibm.image_signing`.
* **`asset_type`:** Defines the type of artifact this rule applies to. For example, it might only apply to an `image` or a `repo`. Using `["*"]` applies the rule to all asset types.
* **`source` (Source Environments):** Defines where the evidence originates. This could be a specific branch or a pipeline environment (e.g., `ci-pipeline`, `stage`, `prod`).
* **`target` (Target Environments):** Defines the destination environment where this evidence is being validated for deployment (e.g., `stage`, `prod`) More details about source and target can be found [here](https://cloud.ibm.com/docs/devsecops?topic=devsecops-evidence-checks-validation#various-deployment-topology).
* **`gating mode`**: Last fields contains which gating mode you want to apply for the evidence type. More information on the supported gating modes can be found [here](https://cloud.ibm.com/docs/devsecops?topic=devsecops-evidence-checks-validation#understanding-evidence-declaration-levels).

**Note** : All the pre-deployment checks must be defined `pre_deployment :{}` array and all the post-deployment checks must be defined `post_deployment :{}` array. Also if you are using default policy path then use must inlcude import of `one_pipeline.config` packagae in your data validation rego file.


## Seamless Migration for Existing JSON Data Validation

For teams already using the pipeline, the biggest question is usually: *“Do I have to manually rewrite all my existing JSON config files into Rego?”*

**The answer is no.** To make the migration completely seamless, you can plug your existing JSON **data validation files** directly into the pipeline. The pipeline natively understands your legacy JSON files and will evaluate them seamlessly alongside the new OPA features. 

> **Note:** This backwards compatibility applies specifically to **Data Validation files**. If you wish to implement custom evaluation *logic*, you must write a Rego **Policy file**.

---

### The JSON to Rego Converter Script

While the pipeline natively supports legacy JSON data validation files, you may eventually want to modernize your configurations into native Rego to fully adopt the policy-as-code standard. 

To help with this, we provide a standalone utility script that automatically translates your existing JSON data validation files into the new Rego format. 

**Script Location:** 
`/opt/one-pipeline/polyglot/polyglot/translate-pipeline-config.sh`

### How to Use the Converter Script

You can execute this script locally or within a pipeline run. When running the script locally, you must provide the target and source environments explicitly.

#### Script Parameters

| Parameter | Required? | Description |
| :--- | :--- | :--- |
| `--input` | **Yes** | The file path to your existing legacy JSON configuration file. |
| `--target-env` | **Yes** | The target environment for the validation (e.g., `prod`, `stage`). *Note: If run inside the pipeline, this auto-resolves via `get_env`.* |
| `--source-env` | **Yes** | The source environment of the evidence (e.g., `master`, `stage`). *Note: If run inside the pipeline, this auto-resolves via `get_env`.* |
| `--output` | No | The destination path where the generated `.rego` file will be saved. If omitted, it will output to standard out or generate alongside the input file depending on python script logic. |

#### Example Usage

```bash
# Convert a JSON config to Rego using named parameters
/opt/one-pipeline/polyglot/polyglot/translate-pipeline-config.sh \
  --input ./legacy-evidence-config.json \
  --output ./new-evidence-config.rego \
  --target-env prod \
  --source-env master
```

> **Note:** You will also have to provide `source-environment` and `target-environment` pipeline environment properties in case if you directly provide `.json` file in `--config-path` property.

---

## Promotion Validation Pipeline: Shift-Left Compliance Gating

Integrating Open Policy Agent (OPA) into the Promotion Validation pipeline brings compliance checks directly into the pull request (PR) phase. This **"shift-left"** approach empowers developers to validate their evidence payload and catch compliance gaps *before* code is merged. By surfacing missing or failing evidence early in the development lifecycle, teams can avoid late-stage deployment blockers and significantly reduce rework.

### Flexible Configuration: Multiple Validation Files

Complex environments often require modular rule sets. To support this, the promotion validation pipeline allows you to evaluate your evidence against multiple configurations simultaneously. 

You can provide multiple data validation files by passing their paths as a comma-separated list to the `evidence-checks-config-path` parameter. In this case the pipeline will evaluate the evidence payload against each configuration file and generate a separate evaluation tables for each file. And in the PR comment section you will see the evaluation tables for each file as separate comments. 

### Automated PR Feedback

For maximum visibility, the pipeline evaluates the current evidence payload against each configuration file you provide. After the evaluation is complete, the pipeline automatically generates detailed summary and evaluation tables, appending them directly to the PR.

If you configure multiple data validation files, the pipeline posts the results for each file as a separate PR comment. This gives reviewers a clear, comprehensive breakdown of the compliance status across different contexts without mixing the outputs.

Inside each PR comment, you can find:
* **Three Evaluation Tables**: A complete breakdown of the validation results. For more information about how to read these tables, see [Understanding the Evidence Check Table](/docs/devsecops?topic=devsecops-evidence-checks-validation#understanding-evidence-check-table).
* **Configuration Links**: Direct links to the specific data validation file and policy file that were used to perform that exact evaluation, making it easy to audit and troubleshoot your rules.

During the Promotion Validation phase, this OPA compliance check is designed for visibility and feedback. If compliance issues are detected against your rules, the pipeline does not halt or fail. Instead, the pipeline completes its run and posts the detailed findings to the PR. This ensures that developers receive a complete report of all compliance gaps and can remediate them without the build crashing mid-process.
{: important}

## The OPA Data Contract: Communicating with the Pipeline
{: #opa-data-contract}

When you write a custom policy, OPA evaluates your rules perfectly in the background. However, the pipeline needs a standardized way to understand those results so it can show developers exactly what happened. This is where the **data contract** comes in.

Think of this contract as a handshake between your custom Rego file and the pipeline engine. You have total freedom to write whatever complex logic you need to reach a decision. But at the very end of your policy, you must map your findings to two specific outputs: the `table_results` object and the `allow` rule.

### The `table_results` Object (For PR Feedback)
{: #table-results-object}

To generate the detailed Markdown tables in your PR comments, the pipeline looks for a specific data structure named `table_results`.

If your policy outputs a `res` object inside `table_results` containing these exact eight fields, the pipeline takes over. It safely parses your output and automatically transforms it into the clean, detailed Markdown table you see in the PR:

```rego
table_results contains res if {
    # ... your custom evaluation logic goes here ...
    
    # Map your findings to the contract structure
    res := {
        "RuleType":       "Required",       
        "AssetType":      "image",          
        "AssetId":        "12345678",      
        "EvidenceTypeId": "com.ibm.scan",   
        "Tool":           "zap",         
        "Status":         "failure",        
        "Source":         "ci-pipeline",    
        "Target":         "prod",           
    }
}
```
{: codeblock}

### The `allow` Rule (For Pipeline Execution)
{: #allow-rule-execution}

As mentioned earlier, the second mandatory part of this contract is the `allow` rule.

**Why is this required?**

While `table_results` handles the reporting of your compliance checks, the `allow` rule is the actual kill switch for the pipeline. The pipeline engine is hardcoded to query the `allow` boolean to determine the final Go/No-Go decision.

If you provide a custom policy file but forget to include the `allow` rule, the pipeline does not know how to proceed. Operating on a strict fail-closed security model, it defaults to failing the pipeline run and blocking the deployment.

## Continuous Deployment (CD) & Continuous Compliance (CC) Pipelines
{: #cd-cc-pipelines}

While the Promotion Validation pipeline focuses on PR-level checks, you can also use OPA for deployment gating within your Continuous Deployment (CD) and Continuous Compliance (CC) pipelines.

### Enabling OPA
{: #enabling-opa-cd-cc}

To enable OPA-based compliance gating in your CD and CC pipelines, you must explicitly turn on the `opt-in-rego-translation` flag (set it to `true` or `1`).

### Configuration Constraints
{: #configuration-constraints}

Just like the Promotion pipeline, you can provide both a Policy file (your logic) and a Data Validation file (your facts). However, there is one key difference: the CD and CC pipelines currently accept only one data validation file at a time, rather than a comma-separated list.

### Change Request (CR) Attachments and Pipeline Finish Steps
{: #cr-attachments}

When you run the CD pipeline, the engine processes your data validation file and attaches it to your Change Request for auditing purposes. The file attachment behavior depends entirely on the format you provide:

* **If you provide a legacy JSON data validation file**: The pipeline automatically translates it behind the scenes. Both the original JSON file and the generated Rego config file are attached to the Change Request step.
* **If you provide a native Rego data validation file**: The pipeline directly attaches only your provided Rego file.

This exact same attachment behavior also applies to the configuration reconstruction process that happens during the CD Finish step at the very end of the pipeline.
{: note}
