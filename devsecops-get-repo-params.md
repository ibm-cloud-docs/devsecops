---

copyright:
  years: 2024
lastupdated: "2024-01-24"

keywords: DevSecOps, token path, repo-token, repo-url, repo-params

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Retrieving repository information and tokens
{: #devsecops-get_repo_params}

## get_repo_params()
{: #get_repo_params}

The function `get-repo-params` is designed to retrieve the repo-related information, when provided the repo URL and Repo token path.

### Parameter
{: #get_repo_params-parameter}

The function `get-repo-params` requires the following parameters:

- `repo_url`
   The repository URL. Provide the correct url of the repo whose parameters need to be found.
- `repo_token_path`
   Path to the token associated with the repository.

### Output
{: #get_repo_params-return-value}

The `get-repo-params` function is provided by the pipeline. The function returns the following information:

- `repo_name`:  Name of the repository.
- `repo_owner`:  User or creator of the repo.
- `scm_type`:  Source Control Management type of the repo (Git, GRIT).
- `api_url`:  API URL linked with the Git service that hosts the repo.

### Function call
{: #get_repo_params-function-call}

```bash
   read -r <repo_name> <repo_owner> <scm_type> <api_url> \
    < <(get_repo_params "<repo_url>" "<token_path>")
```
{: codeblock}

 Use the underscore variable (_) in the read command as a placeholder for a variable whose value is being discarded or not used in subsequent parts of the script.

```bash
   read -r <repo_name> <_> <scm_type> <_> \
    < <(get_repo_params "<repo_url>" "<token_path>")
```
{: codeblock}

Use these variables as shell variables in your script.

#### Example
{: #get_repo_params-example}

```bash
   ### <repo_url> = $EVIDENCE_LOCKER_URL
   ### <token_path> = ${WORKSPACE}/$EVIDENCE_TOKEN_PATH

   source "${ONE_PIPELINE_PATH}"/tools/get_repo_params

   read -r EVIDENCE_REPO_NAME EVIDENCE_REPO_ORG EVIDENCE_SCM_TYPE EVIDENCE_API_URL \
   < <(get_repo_params "$EVIDENCE_LOCKER_URL" "${WORKSPACE}/$EVIDENCE_TOKEN_PATH")

   ### Printing the value of EVIDENCE_LOCKER_URL

   echo "EVIDENCE_LOCKER_URL $EVIDENCE_LOCKER_URL"
```
{: codeblock}


## get_repo_specific_token()
{: #devsecops-get_repo_specific_token}

The `get_repo_specific_token` function retrieves the token that is associated with the repo URL used earlier. A repository token authenticates and authorizes access to a specific repo.

It picks the value of (git-token-[repo_name]-[repo_org]) variable from the environment properties table.

### Example
{: #get_repo_specific_token_example}


let repo_url = "https://github.ibm.com/user_name/compliance-app"
where:
```bash
<repo_name> = compliance-app
<repo_org> = user_name
```
{: codeblock}

### Function call
{: #get_repo_specific_token-function-call}

```bash
   parameter
   ### repo_url
   token=$(get_repo_specific_token "<repo_url>")
```
{: codeblock}

### Output:
{: #get_repo_specific_token-output}

The following output is generated:

```bash
   echo "$repo_token"
```
{: codeblock}

#### Example:
{: #get_repo_specific_token-example}

```bash
   ### parameter:
   # <repo_url>: URL of repo of which token is required.

   source "${ONE_PIPELINE_PATH}"/tools/get_repo_params

   repo_url="https://github.ibm.com/user_name/compliance-app"
   token=$(get_repo_specific_token "$repo_url")

   ### Output
   # repo_token

   echo "Repository Token: $token"
```
{: codeblock}



## get_api_url()
{: #devsecops-get_api_url}


The `get_api_url` function determines the appropriate `API URL` for different types of source code repositories.

### Parameters:
{: #get_api_url-parameter}

- `repo_url`: The URL of the source code repository.

### Function call:
{: #get_api_url-function-call}

```bash
   api_url=$(get_api_url "<repo_url>")
```
{: codeblock}

### Return Value:
{: #get_api_url-return-value}

The function displays the `API URL` value for the given repository.

#### Example:
{: #get_api_url-example}

```bash

   source "${ONE_PIPELINE_PATH}/tools/get_repo_params"

   repo_url="https://github.ibm.com/user_name/compliance-app"
   api_url=$(get_api_url "$repo_url")

   echo "API URL for $repo_url is: $api_url"
```
{: codeblock}

## SCM Types and Corresponding API URLs:
{: #get_scm_types}

1. GitHub Integrated:
   - SCM Type: `github_integrated`
   - API URL: `https://github.ibm.com/api/v3`

2. GitHub Consolidated:
   - SCM Type: `githubconsolidated`
   - Integrated GitHub Repository:
     - API URL: `https://<repo_server_name>/api/v3` (where `<repo_server_name>` is extracted from the repository URL)
   - Non-Integrated GitHub Repository:
     - API URL: `https://api.github.com`

3. Hosted Git:
   - SCM Type: `hostedgit`
   - API URL: `https://<repo_server_name>/api/v4` (where `<repo_server_name>` is extracted from the repository URL)

If the SCM type is not one of the supported types, a warning message is displayed.


## get_repo_scm_type()
{: #devsecops-get_repo_scm_type}

This function determines the Source Code Management (SCM) type of a given repository URL.

### Parameter:
{: #get_repo_scm_type-parameter}

- `repo_url`: The URL of the repository for which the SCM type needs to be determined.

### Function call:
{: #get_repo_scm_type-function-call}

```bash
   scm_type=$(get_repo_scm_type "<repo_url>")
```
{: codeblock}

### Output:
{: #get_repo_scm_type-returns}

A label indicating the SCM platform, that might be one of the following:
      - For GitLab-hosted repositories: `gitlab`
      - For GitHub-integrated repositories: `github`
      - For other cases (defaulting to GitHub): `github`

#### Example Usage:
{: #get_repo_scm_type-example-usage}

```bash
   source "${ONE_PIPELINE_PATH}/tools/get_repo_params"

   repository_url="https://github.ibm.com/user_name/compliance-app"
   result=$(get_repo_scm_type "$repository_url")

   echo "SCM Type: $result"
```
{: codeblock}

This example displays `github` as output value because the repository URL provided earlier is integrated with GitHub.


## get_repo_owner()
{: #devsecops-get_repo_owner}


This function extracts the owner or subgroup information from the Git repository URL provided earlier.

### Parameter:
{: #get_repo_owner-parameter}

- `repo_url`: The URL of the Git repository.

### Function call:
{: #get_repo_owner-function-call}

```bash
   Owner_name=$(get_repo_owner "<repo_url>")
```
{: codeblock}

### Output:
{: #get_repo_owner-returns}

- The owner or subgroup information that is extracted from the repository URL.

#### Example Usage:
{: #get_repo_owner-example-usage}

```bash
   source "${ONE_PIPELINE_PATH}/tools/get_repo_params"

   repository_url="https://github.ibm.com/user_name/compliance-app"
   owner=$(get_repo_owner "$repository_url")

   echo "Repository Owner: $owner"
```
{: codeblock}

This example displays `user_name` as output because GitHub hosts the repository URL the "username" account or subgroup.

- If the owner is not found, it extracts the first segment of the URL as the owner.


## get_repo_name()
{: #devsecops-get_repo_name}

This function extracts the name of a Git repository from a given repository URL.

### Parameters:
{: #get_repo_name-parameters}

- `repo_url`: The URL of the Git repository.

### Function call:
{: #get_repo_name-function-call}

```bash
   Repo_name=$(get_repo_name "<repo_url>")
```
{: codeblock}

### Output:
{: #get_repo_name-returns}

 This functional call displays the name of the repository that is extracted from the repository URL as your output.

#### Example Usage:
{: #get_repo_name-example-usage}

```bash
   source "${ONE_PIPELINE_PATH}/tools/get_repo_params"

   repository_url="https://github.ibm.com/user_name/compliance-app"
   repo_name=$(get_repo_name "$repository_url")

   echo "Repository Name: $repo_name"
```
{: codeblock}

This example would output `compliance-app` since the provided repository URL is named "compliance-app" on GitHub.

If the name is not found, it attempts to use the second-to-last segment of the URL as the name.
{: #note}
