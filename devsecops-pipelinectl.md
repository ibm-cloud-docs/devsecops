---

copyright:
  years: 2021
lastupdated: "2021-09-15"

keywords: DevSecOps, pipelinectl

subcollection: devsecops

---

{:shortdesc: .shortdesc}
{:table: .aria-labeledby="caption"}
{:external: target="_blank" .external}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:note: .note}
{:deprecated: .deprecated}
{:important: .important}
{:download: .download}
{:help: data-hd-content-type='help'}
{:support: data-reuse='support'}

# pipelinectl
{: cd-devsecops-pipelinectl}

`pipelinectl` is a lightweight key-value store that can be used in [Tekton](https://tekton.dev/){: external} pipelines to share data between tasks and the compliance automation scripts.
{:shortdesc}

For more information about where this tool is used, see [Setting up your pipeline scripts](/docs/devsecops?topic=devsecops-cd-devsecops-setup-pipeline-scripts).

## Usage
{: #pipelinectl-usage}

`pipelinectl` provides a single binary. Its behavior depends on its name (à la [busybox](https://www.busybox.net/about.html){: external}). When invoked as `pipelinectl`, the program must be provided as the first argument, for example `pipelinectl get_data`.

Available aliases/methods:

- [set_env](#set_env)
- [get_env](#get_env)
- [save_file](#save_file)
- [load_file](#load_file)
- [save_repo](#save_repo)
- [list_repos](#list_repos)
- [load_repo](#load_repo)
- [save_result](#save_result)
- [list_results](#list_results)
- [load_result](#load_result)
- [save_artifact](#save_artifact)
- [list_artifacts](#list_artifacts)
- [load_artifact](#load_artifact)

### set_env
{: #set_env}

```bash
set_env <key> # reads <value> from `stdin`
set_env <key> <value>
```
{: codeblock}

If the `<value>` argument is missing, it will read it from the standard input.
Saves an arbitrary string that can be retrieved later on with [`get_env`](#get_env).

Example:

```bash
# set value provided as argument
set_env app-name "my-app-name"

# set value provided via stdin
echo "my-app-name" | set_env app-name
set_env my-api-key < /config/my-api-key
```
{: codeblock}

### get_env
{: #get_env}

```bash
get_env <key> [default]
```
{: codeblock}

Print the stored configuration value (in this order):

* If `set_env` was used previously with `key`, it retrieves that value
* It tries to read the file `$CONFIG_DIR/$key` (`CONFIG_DIR` defaults to `/config`)
* It prints the specified default value (if there's any)
* It prints an error message, and returns a non-zero exit code

Example:

```bash
get_env app-name "default-app-name"
```
{: codeblock}

### save_file
{: #save_file}

```bash
save_file <key> <value>
```
{: codeblock}

Saves an arbitrary file that can be retrieved later on with [`load_file`](#load_file).
Directories are not supported.

Example:

```bash
save_file some_config ./config.yaml
```
{: codeblock}

### load_file
{: #load_file}

```bash
load_file <key>
```
{: codeblock}

Prints the saved file to `stdout`.

Example:

```bash
load_file some_config > some_config.yaml
```
{: codeblock}

### save_repo
{: #save_repo}

```bash
save_repo <key> [<prop>=<value> ...]
```
{: codeblock}

Registers a new repository with the pipeline or updates an existing one.

Supported properties:

- `url`: The URL which can be used to clone the repository.
- `path`: Location of the cloned repository relative to the workspace root.

Other property names can be used as well, but in order to avoid naming collisions, they should be prefixed with a service-specific identifier, for example instead of using `foo`, `my-service.foo` should be used.

Example:

```bash

save_repo app_ui "url=${REPO_URL}" "path=app_ui_repo"
save_repo app_ui "branch=${REPO_BRANCH}"
save_repo app_ui "commit=${REPO_SHA}"

# any additional property can be added
save_repo app_ui "commit=${REPO_SHA}"
```
{: codeblock}

### list_repos
{: #list_repos}

```bash
list_repos
```
{: codeblock}

Lists the `<key>` of the stored repos to `stdout`.

Example:

```bash
list_repos
# returns the list of stored repository keys to stdout for example:
#  app_ui
#  app_repo
```
{: codeblock}

### load_repo
{: #load_repo}

```bash
load_repo <key> <prop>
```
{: codeblock}

Prints the given `<prop>` of repository `<key>` to `stdout`.

Example:

```bash
REPO_SHA=$(load_repo app_ui commit)
```
{: codeblock}


Used together with `list_repos`

```bash
#
# iterate over all repos and read their URLs
#
while read -r key; do
  url=$(load_repo $key url)
done < <(list_repos)
```
{: codeblock}

### save_result
{: #save_result}

```bash
save_result  <path>
```
{: codeblock}

Saves an arbitrary test, scan result file for a given stage. Later this can be retrieved with `load_result`. Data is saved with the workspace-relative path as key.

Example:

```bash
#
# save the contents of the file ./results/mocha_results.json
# as an entry named "mocha_results.json" for the "test" stage
#
save_result test ./results/mocha_results.json

#
# save the contents of the file ../data/coverage.xml
# as an entry named "coverage.xml" for the "test" stage
#
save_result test ../data/coverage.xml
```
{: codeblock}

### list_results
{: #list_results}

```bash
list_results 
```
{: codeblock}

Lists the saved file names for a given stage.

Example:

```sh
list_results test
# mocha_results.json
# coverage.xml
```
{: codeblock}

### load_result
{: #load_result}

```bash
load_result  <file>
```
{: codeblock}

Prints the saved file keys to `stdout`. A key is the workspace-relative path of the provided file path in `save_result`. To get the exact list of keys, use `list_results`.

Example:

```bash
load_result test mocha_results.json
```
{: codeblock}

Used together with `list_results`

```bash
#
# iterate over all results stored for "test"
# and write them to the filename they were registered with
#
while read -r filename; do
  load_result test "$filename" > "./$filename"
done < <(list_results test)
```
{: codeblock}

### save_artifact
{: #save_artifact}

```bash
save_artifact <key> [<prop>=<value> ...]
```
{: codeblock}

Registers a new build artifact with the pipeline or updates an existing one.

#### Container images
{: #pipelinectl-container-images}

Supported properties:

- `type`: Must be `image`
- `name`: fully qualified image name, for example something that can be used by `docker pull`

`name` should also contain the tag for the image.
{: tip}

Example:

```bash
save_artifact ui_service "name=us.icr.io/team_namespace/ui_service:2.4.3"
save_artifact ui_service "type=image"

# any additional property can be added
save_artifact ui_service "tags=latest,2.4.3,feat-something"

# later, when the image was signed, and we have signature data
save_artifact ui_service "signature=${SIGNATURE}"
```
{: codeblock}

Other artifact types to be added later.


### list_artifacts
{: #list_artifacts}

```bash
list_artifacts
```
{: codeblock}

Lists the `<key>` of the stored artifacts to `stdout`.

Example:

```bash
list_artifacts
# returns the list of stored artifact keys to stdout for example:
#
# ui_service
# app_service
```
{: codeblock}



### load_artifact
{: #load_artifact}

```bash
load_artifact <key> <prop>
```
{: codeblock}

Prints the given `<prop>` of artifact `<key>` to `stdout`.

Example:

```bash
SIGNATURE=$(load_artifact ui_service signature)
```
{: codeblock}

Used together with `list_artifacts`

```bash
#
# iterate over all artifacts and read their image names
#
while read -r key; do
  image=$(load_artifact $key name)
done < <(list_artifacts)
```
{: codeblock}


## Low-level methods
{: #low-level methods}

These methods are only exposed for the sake of completeness, they should be used in only rare occasions.

### put_data
{: #put_data}

```bash
put_data <key> <prop> <value>
```
{: codeblock}

Sets `prop` to `value` for the entry defined by `key`.

### get_data
{: #get_data}

```bash
get_data <key>
get_data <key> <prop>
```
{: codeblock}

Prints `prop` of the entry defined by `key`. If `prop` is not provided, it returns all of the `prop`s for the given `key`. Returns a non-zero exit code when `key` has no `prop`.


### save_string (deprecated)
{: #save_string}

`save_string` is deprecated, use `set_env` instead.
{: deprecated}

```bash
save_string <key> <value>
```
{: codeblock}

Saves an arbitrary string that can be retrieved later on with `load_string`.

### load_string (deprecated)
{: #load_string}

`load_string` is deprecated, use `get_env` instead.
{: deprecated}

```bash
load_string <key>
```
{: codeblock}

Prints the string stored in `key`.
