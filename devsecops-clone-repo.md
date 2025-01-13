---

copyright:
  years: 2024, 2025
lastupdated: "2024-07-29"

keywords: DevSecOps, clone-repo, git, clone, repo, script

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# clone_repo() function
{: #devsecops-clone_repo}

The clone_repo() function is designed to clone a Git repository, optionally including submodules, with support for branch and commit ID selection, as well as retry mechanisms for handling connection timeouts. It also sanitizes error messages for security.

## Parameters:
{: #clone_repo-parameters}

- `repository` : The URL of the Git repository to be cloned.
- `git_branch` : The branch to be checked out. If not provided, the default branch is used.
- `directory_name` : The name of the directory where the repository will be cloned.
- `token_path` : The path to the file containing the Git authentication token.
- `use_submodules` : (Optional) Set to "1" to clone submodules, defaults to "0".
- `commit_id` : (Optional) The specific commit ID to reset to after cloning.

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
clone_repo <repo url e.g. https://github.ibm.com/owner/repo.git> <branch e.g. master> <repo directory e.g. repo_directory> <token path e.g. /path/to/token> <if submodules to be used e.g. 1> <commit id e.g. abcd1234>
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
