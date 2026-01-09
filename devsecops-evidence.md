---

copyright: 
  years: 2021, 2026
lastupdated: "2026-01-09"

keywords: DevSecOps, compliance evidence, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Evidence
{: #devsecops-evidence}

Collecting evidence is one of the essential aspects of the DevSecOps reference architecture. Compliance evidence creates the audit trail that auditors look for during a compliance audit. One of the goals of DevSecOps is automated evidence generation and storage in auditable evidence lockers.
{: shortdesc}

The way that DevSecOps pipelines handle evidence (file format and locker structure) is:

- v2 evidence ([asset-based evidence](/docs/devsecops?topic=devsecops-devsecops-evidence-summary#evidence-v2))

## Evidence creation
{: #cd-devsecops-evidence-creation}

Evidence differs from artifacts that are created by pipeline stage steps, such as unit test results, or XML or JSON files. Each task must report to several tools that handle evidence such as creating, formatting, and storing evidence.

Any generic test, check, or scan can produce evidence within a pipeline stage by using the steps within DevSecOps tools or pipelines that are shown in the following image. The DevSecOps tools must be able to receive the result of the task, create the evidence, and then store it in the evidence locker.

 ![Evidence creation](images/evidence-creation.png){: caption="Evidence creation" caption-side="bottom"}

The evidence format contains the result of the task (passing or failing), links to the created artifacts, and links to any incident issue that is created based on the Task result.

These tools focus only on evidence collection and do not change the behavior of your build process. The DevSecOps reference pipeline does not break due to failed Task results. An image can be built and deployed with failing tests and vulnerabilities if evidence of the checks and failures exists, the team is notified, a change request that is created during the deployment shows evidence of these issues, and the change request is manually approved.
{: important}

## Evidence flow
{: #devsecops-evidence-flow}

The following diagram shows how the evidence is handled and how it flows through the stages of continuous integration and continuous deployment.

 ![Evidence flow](images/evidence-flow.png){: caption="Evidence flow" caption-side="bottom"}

 Each piece of evidence that is collected in the various stages of the DevOps Architecture is stored in auditable evidence lockers. During deployment, this evidence is collected to create an evidence summary that is saved to the evidence locker at the end of the deployment run.

The evidence summary is attached to the change request, which is posted to the change request store. During a manual change request approval, the approver is aware of any issues that are found during the build. Additionally, the summary is submitted to the Security and Compliance Center.

Effective 15 Dec 2025, IBM Cloud Security and Compliance Center is deprecated. Any existing service instances are non-functional.
{: deprecated}

## v2 evidence (current format)
{: #devsecops-v2-evidence-current}

### v2 evidence locker
{: #devsecops-v2-evidence-lockers}

Evidence is stored in a flat hierarchy, where each piece of evidence is identified by its own SHA256 hash, this provides a layer of integrity protection (that is, any modification of the evidence content can be detected). As each piece of evidence is related to one or more assets, the evidence summarization algorithms discover the relevant evidence based on assets.

The only hierarchy is the type differentiation and some hash grouping similar to the structure of Git hash objects.

#### Example
{: #devsecops-v2-evidence-lockers-example}

```text
.
└── raw/
    ├── assets/
    │   └── xx/
    │       └── abcdef123456789/
    │           ├── evidences/
    │           │   ├── 00abcdef123456789
    │           │   └── 01abcdef123456789
    │           └── index.json
    ├── attachments/
    │   ├── aa/
    │   │   └── abcdef123456789/
    │   │       └── content
    │   └── ab/
    │       └── abcdef123456789/
    │           └── content
    ├── cd/
    │   ├── c9b77749-fd59-4d32-bbdb-18e55db1615d/
    │          └── summary.json
    |          └── evdience-checks.json 
    ├── cc/
    │   ├── absd7749-fd59-4d32-bbdb-18e55db1615d/
    │          └── summary.json
    |          └── evdience-checks.json              
    └── evidences/
        ├── 00/
        │   └── abcdef123456789/
        │       └── index.json
        └── 01 /
            └── abcdef123456789/
                └── index.json      
```

### v2 evidence collection
{: #devsecops-v2-evidence-collection}

The v2 evidence must be collected as near as possible to the process that created the result for an evidence. After each scan run, after each test for example.

For collecting evidence, the [collect-evidence](/docs/devsecops?topic=devsecops-devsecops-collect-evidence) script can be used in the DevSecOps pipelines.

### v2 evidence format
{: #devsecops-v2-evidence-format}

A piece of evidence represents the outcome of a scan, test, and so on. The evidence is always connected to at least a single asset. Multiple assets are allowed, such as a single end-to-end test suite that probably tests multiple assets together.

An asset represents something that you can test, scan, and so on, such as a Git `commit` in a repository, or a docker `image`, or any `generic` asset with an URI.

The `Evidence` and `Asset` types represent the schema of the v2 locker elements: evidence and asset. Although the schema uses typescript syntax, you can convert it to use JSON schema.

```ts
type SHA1 = string;          // 40 character string representing a SHA-1 hash in hexadecimal format
type SHA256 = string;        // 64 character string representing a SHA256 hash in hexadecimal format
type IssueURL = string;      // Link to issues on a git service provide like GitHub or GitLab
type RepositoryURL = string; // Link to a git repository
type AssetURI = string;      // URI of an Asset, like an image or a repository link and git hash
type FileName = string;      // file basename of the attachment


interface Evidence {
  version: 2;
  id: SHA256;
  date: string;
  evidence_type_id: string;
  evidence_type_version: string;
  origin: {
    // scope defines a contextual set for multiple evidence, usually a SHA256 identifier or a CI/CD run ID
    scope: SHA256;  
    
    // any further IDs can be used to determine evidence origin, see example
    [index: string]: string;
  },
  details: {
    result: 'success' | 'failure' | 'pending';
    tool: string;

    // field "details" can have any arbitrary key-value pairs to provide metadata
    [index: string]: string;
  }
  attachments: Record<string, string> | EvidenceAssetAttachment[];
  assets: string[] | EvidenceAssetAttachment[];
  issues: IssueURL[],
  findings?: IncidentFinding[];
}

export interface IncidentFinding {
  id: string;
  url: string;
  due_date: string;
  first_found?: string;
  severity: ("high", "medium", "low", "critical, "informational");
  has_exempt: boolean;
  found_status: ("new", "existing", "autoclosed", "readonly");
}

export interface EvidenceAssetAttachment {
  url: string; // hash of the asset or attachment
  hash: string; // complete url of the asset or attachment
  uri?: string; // name of the asset
}

interface Asset {
    version: 1;
    id: SHA256;
    uri: AssetURI;
    date: string;
    type: 'commit' | 'image' | 'generic';
    origin: {
      // any IDs can be used to determine asset origin, see example
      [index: string]: string;
    },
    details: Record<string, string>,

    // Assets can relate to each other, for example 
    // an Image Asset can relate to the Git Commit Asset 
    // it was built from on code level
    related: SHA256[]; 
}

```


### v2 evidence summary
{: #devsecops-v2-evidence-summary}

The DevSecOps pipeline creates an evidence summary document. This document contains the most recent of all evidence that is created during each of the continuous integration builds that deploy an image, and the evidence that is created during the deployment itself. The summary is created for the change request that is required to deploy any stage. This format of evidence is not yet supported by the {{site.data.keyword.compliance_short}} integration. 

```bash
interface Summary {
  version: '2.0';                // schema version
  date: string;                  // ISO-8601, UTC, ie. YYYY-MM-DDThh:mm:ssZ
  toolchain_crn: string;         // CRN of the toolchain that generated the summary
  pipeline_id: string;           // ID of the pipeline that generated the summary
  pipeline_run_id: string;       // ID of the pipeline run that generated the summary
  evidences: Evidence[];
}
```

This summary does not perform any result aggregation. It is the raw data of collected v2 evidence, as they were found for assets that are related to a change request.
{: important}
