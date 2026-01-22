---

copyright:
  years: 2024, 2026
lastupdated: "2024-07-29"

keywords: DevSecOps, clone-repo, git, clone, repo, script

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# clone_repo
{: #devsecops-clone-repo}

Cloning a Git repository is a crucial step in pipelines, ensuring the source code is fetched securely and reliably. The cloning process can involve checking out specific branches, handling submodules, and even resetting to particular commits. This document outlines the two versions of the clone_repo function:

- clone_repo v1
- clone_repo v2

The cloning operation plays a crucial role in PR (Pull Request), CI (Continuous Integration), CC (Code Commit), and CD (Continuous Deployment) pipelines, where the repository needs to be cloned as part of the workflow. The method of cloning can be configured using the environment property `git-clone-version`, which is set to `v1` by default. This property allows users to choose between cloning methods `v1` and `v2`, providing flexibility in how the repository is fetched based on specific pipeline requirements.

# clone_repo v1() function
{: #devsecops-clone_repo}

The clone_repo() function is designed to clone a Git repository, optionally including submodules, with support for branch and commit ID selection, as well as retry mechanisms for handling connection timeouts. It also sanitizes error messages for security.

## Parameters:
{: #clone_repo-parameters}

| Parameter | Description |
| ---------|------------ |
|`repository` | The URL of the Git repository to be cloned. |
| `git_branch` | The branch to be checked out. If not provided, the default branch is used. |
| `directory_name` | The name of the directory where the repository will be cloned. |
| `token_path` | The path to the file containing the Git authentication token. |
| `use_submodules` | (Optional) Set to "1" to clone submodules, defaults to "0". |
| `commit_id` | (Optional) The specific commit ID to reset to after cloning. |
| `force-exit` | (Optional) <true\false> Force exit on failure. |

## Function Implementation:
{: #clone-repo-function-implemenatation}

- `Initialize Variables` : Set up environment variables and defaults.
- `Token and User Validation` : Ensure authentication details are available.
- `Clone Repository` : Clone the repository with optional retry mechanism.
- `Submodule Handling` : If submodules are enabled, set up the necessary environment and clone submodules.
- `Checkout Branch or Commit` : Check out the specified branch or commit.
- `Finalize and Export` : Set environment variables and print status messages.

## Return value:
{: #clone_repo-return-value}

The `clone_repo` function sets several environment variables/properties upon completion:

- `git_branch` : The branch that was checked out.
- `git_commit` : The commit ID that the repository was reset to.
- `directory_name` : The directory where the repository was cloned.
- `submodules_status` : Status of the submodules, if any.

## Function call:
{: #clone_repo-function-call}

```bash
clone_repo <repo url t> <branch e.g. master> <repo directory e.g. repo_directory> <token path e.g. /path/to/token> <if submodules to be used e.g. 1> <commit id e.g. abcd1234>
```
{: codeblock}

## Submodule cloning:
{: #clone_repo-submodule-cloning}

- If `USE_SUBMODULES` is set to `1`, the script checks for pipeline environment variables to determine if submodules should be cloned and how they should be configured.
- Submodule cloning only supports submodules declared with the `HTTP(S)` protocol in the `.gitmodules` file. The script relies on the provided Git authentication token, which is used in `HTTP(S)` URLs. Submodules declared with other protocols, such as `SSH`, won't work seamlessly because the script is not configured to handle SSH keys or other authentication methods.
- The variables `opt-in-clone-submodules` and `opt-in-clone-remote-submodules` play a crucial role in determining how the script handles the cloning of submodules.
- `opt-in-clone-submodules`: Controls whether submodules should be cloned during the repository cloning process.
  - If `opt-in-clone-submodules` is set to `1`, the script includes the `--recurse-submodules` parameter in the Git clone command, which instructs Git to initialize and update each submodule in the repository.
  - If not set or set to any other value, submodules will not be cloned, and the script will proceed with cloning the main repository only.
- `opt-in-clone-remote-submodules`: Extends the behavior of `opt-in-clone-submodules` by allowing submodules to be cloned from their remote repositories rather than using the local paths specified in the parent repository.
  - If `opt-in-clone-remote-submodules` is set to `1`, the script adds the `--remote-submodules` parameter alongside `--recurse-submodules`. This makes Git fetch and clone the submodules from their remote origins rather than relying solely on the paths specified in the parent repository.
  - If not set or set to any other value, the submodules will be cloned using the default local paths provided in the `.gitmodules` file of the parent repository.

### Important Notes:
- `Retry Mechanism` : The script retries the clone command up to 5 times with a 2-second delay between attempts if a "Connection timed out" error occurs.
- `Error Handling` : If authentication or other critical errors occur, the script exits with a non-zero status and provides guidance on resolving common issues.
- `Error Message Sanitisation`: The script sanitizes error messages to ensure sensitive information, such as authentication tokens, are not exposed in the output. This is done using sed to replace the sensitive parts of URLs with placeholders.


# clone_repo v2() function
{: #clone_repo-v2}

The clone_repo v2 function is an improved version of v1, supporting shallow cloning, and better error handling.

## Parameters
{: #clone_repo-v2-parameters}

| Parameter | Description |
| ---------|------------ |
| `repository`  | Git repository url. |
| `token-path` | (Optional) Path to the Git token file. |
| `directory` | (Optional) Target directory to clone the repository. |
| `commit-id` | (Optional) Specific commit ID to checkout. |
| `clone-submodules`  | (Optional) Clone and initialize submodules. |
| `depth` | (Optional) Depth for shallow clone. |
| `use-lfs` | (Optional) <true\false> Enable/disable Git LFS for the repository. |
| `force-exit`  | (Optional) <true\false> Force exit on failure. |
| `git-clone-options` | (Optional) Additional options for Git fetch. |


## Function Call
{: #clone_repo-v2-function-call}

```bash
clone_repo_v2 --repository <repo_url> --branch <e.g. master> --directory <e.g. repo_directory> --token-path <e.g. /path/to/token> --clone-submodules <if submodules to be used e.g. 1> --force-exit <false/true> --use-lfs <false/true> --depth ''
```
{: codeblock}


## Submodule Cloning
{: #clone_repo-v2-submodule-cloning}

Both v1 and v2 support submodule cloning.

## Environment property
{: #clone_repo-v2-environment-property}

The following environment properties allow customization of the Git cloning process, ensuring optimal performance and flexibility based on pipeline requirements. These properties should be set in CI (Continuous Integration), PR (Pull Request), or other pipelines as needed.

| Property | Description |
| ---------|------------ |
| `git-depth`| Specifies the depth for shallow cloning. If left empty, a full clone is performed. Setting this to a numeric value (e.g., 1) enables shallow cloning, fetching only the latest commit for faster performance. |
| `git-use-lfs`| Determines whether to use Git Large File Storage (LFS). `true/false`. |
| `git-options`| Allows to pass additional custom Git options eg: `--verbose --quiet`. |


The clone_repo v2 function introduces a more efficient and flexible approach to repository cloning, optimizing performance with features like shallow cloning, Git LFS support, and customizable Git options.
