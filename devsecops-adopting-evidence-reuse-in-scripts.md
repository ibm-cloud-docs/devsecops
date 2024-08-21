---

copyright: 
  years: 2024
lastupdated: "2024-08-09"

keywords: DevSecOps, evidence reuse

subcollection: devsecops

---

# adopting-evidence-reuse-in-scripts
{: #adopting-evidence-reuse-in-scripts}

### Pseudo code
{: #pseudo-code}
```
Check if evidence for reuse exists for (asset, tool, evidence-type) using check-evidence-for-reuse
  If evidence found
	 // optional
	 download result and publish to DOI using dot-publish
	 if [ doi-publish was successful ]
	  save the result file // optional
  Else
	 // no evidence found for reuse
	 // proceed with scan and collect-evidence
```

### Check if Evidence is Available for Reuse:

 - The script uses the `check-evidence-for-reuse` command with parameters similar to those in the `collect-evidence` script. For details on the `check-evidence-for-reuse` function, refer to the documentation for [check-evidence-for-reuse](devsecops-check-evidence-for-reuse.md).

### Publishing Test Records to DevOps Insights Using doi-publish:

- To publish test records to DOI based on reused evidence, the `doi-publish` function is used. For detailed information on how to use `doi-publish`, see the documentation for [doi-publish](devsecops-doi-publish.md).


### Example usage
{: #example-usage}

```bash

source "${COMMONS_PATH}/doi/doi-publish-testrecord.sh"
local evidence_params
evidence_params=(
  --tool-type "${tool-type}" \
  --evidence-type "${evidence-type}" \
  --asset-type "repo" \
  --asset-key "${repo_key}"
)
output="${repo_key}_cra_cis.json"   # label for the attachment
reusedEvidenceJson="$(mktemp)"
status_file="$(mktemp)"
if (check-evidence-for-reuse "${evidence_params[@]}" --output-path "${reusedEvidenceJson}" > "${status_file}"); then
    exit_code=0
    doi-publish --evidence-file "${reusedEvidenceJson}" --attachment-label "$output" --record-type "cradeploy" --url "$url" --attachment-output-path "$WORKSPACE/$output" || exit_code=$?
    if [ $exit_code == "0" ] && [ -s "$WORKSPACE/$output" ]; then
      save_result cra-cis-check "$WORKSPACE/$output" # for evidence v1
    fi
else
    # Run the scan
```
{: codeblock}
