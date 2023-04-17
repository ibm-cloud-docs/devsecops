---

copyright:
  years: 2021, 2023
lastupdated: "2023-04-17"

keywords: DevSecOps, pipelinectl

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# pipelinectl
{: #devsecops-pipelinectl}

`pipelinectl` is a lightweight key-value store that can be used in DevSecOps pipelines to share data between tasks and the compliance automation scripts.
{: shortdesc}

For more information about where this tool is used, see [Adding test and build steps to pipelines](/docs/devsecops?topic=devsecops-cd-devsecops-add-pipeline-steps).


  
## Usage
{: #pipelinectl-usage}

`pipelinectl` provides a single binary. Its behavior depends on its name (as in [busybox](https://www.busybox.net/about.html){: external}). When invoked as `pipelinectl`, the program must be provided as the first argument, for example `pipelinectl get_data`.

Available aliases and methods:

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
- [put_data](#put_data)
- [get_data](#get_data)
- [serialize](#serialize)
- [deserialize](#deserialize)

### set_env
{: #set_env}

```bash
# <key>: The name of the environment variable e.g. pipeline-namespace, app-name
# <value>: Value of the key
set_env <key> # reads <value> from `stdin`
set_env <key> <value>
```
{: codeblock}

If the `<value>` argument is missing, `set_env` reads it from the standard input.
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
# <key>: The name of the environment variable e.g. pipeline-namespace, app-name
get_env <key> [default]
```
{: codeblock}

Print the stored configuration value (in this order):

* If `set_env` was used previously with `key`, it retrieves that value
* It tries to read the file `$CONFIG_DIR/$key` (`CONFIG_DIR` defaults to `/config`)
* It prints the specified default value (if there's any)
* It prints an error message, and returns a nonzero exit code

Example:

```bash
get_env app-name "default-app-name"
```
{: codeblock}

### save_file
{: #save_file}

```bash
# <key>:  File name
# <value>: Path, where the file will be stored
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
# <key>:  File name
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
# <key>:  Key of the repository e.g. repository name
# <prop>: Type of the property, e.g. url, branch, commit etc.
# <value>: Value of the property
save_repo <key> [<prop>=<value> ...]
```
{: codeblock}

Registers a new repository with the pipeline or updates an existing one.

Supported properties:

- `url`: The URL that can be used to clone the repository.
- `path`: Location of the cloned repository relative to the workspace root.

Other property names can be used as well, but to avoid naming collisions, they must be prefixed with a service-specific identifier, for example instead of using `foo`, use `my-service.foo`.

Example:

```bash
save_repo app_ui "url=${REPO_URL}" "path=app_ui_repo"
save_repo app_ui "branch=${REPO_BRANCH}"
save_repo app_ui "commit=${REPO_SHA}"

# any additional property can be added
save_repo app_ui "commit=${REPO_SHA}"
```
{: codeblock}

#### Using stdin as a value source
{: #stdin-value-source}

Values can be provided from stdin, if the following conditions are true:

- The content is streamed for the command
- One property does not have a value and `=`

Example:

```bash
command_with_large_output | save_repo app_ui "issues"

# this also works with multiple properties,
# but stdin can provide value for only a single one
command_with_large_output | save_repo app_ui "issues" "result=success" "commit=${REPO_SHA}"
```
{: codeblock}

If multiple values are missing with `=` the command exits with an error, because it cannot determine 
which property belongs to the value on `stdin`.

Properties without a value but still appending `=` have an empty string as a value.

```bash
save_repo app_ui "bar="
load_repo app_ui bar # returns an empty string
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
# <key>: Key of the repository, e.g. repository name
# <prop>: Name of the property, e.g. commit, branch, url
load_repo <key> <prop>
```
{: codeblock}

Prints the `<prop>` of repository `<key>` to `stdout`.

Example:

```bash
REPO_SHA=$(load_repo app_ui commit)
```
{: codeblock}


Used together with `list_repos`

```bash
#
# iterate over all repos and print their URLs
#
while read -r key; do
  url=$(load_repo $key url)
  echo "Repository saved as '$key' is at: '$url'"
done < <(list_repos)
```
{: codeblock}

Outputs the following lines to the console:

```text
 Repository saved as 'my-frontend' is at: 'github.com/my-team/frontend'
 Repository saved as 'my-backend' is at: 'github.com/my-team/backend'
```
{: screen}

### save_result
{: #save_result}

```bash
# <stage>: Stage name e.g. test, detect-secrets, static-scan
# <path>: Path where will be stored the file, string
save_result  <stage> <path>
```
{: codeblock}

Saves an arbitrary test, scan result file for a stage. Later this file can be retrieved with `load_result`. By default, data is saved with the workspace-relative path as key.

Using the `PIPELINECTL_USE_PATH_AS_KEY` feature flag, data is saved with the provided path as the key.

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

#
# Using the `PIPELINECTL_USE_PATH_AS_KEY` environment variable
# save the contents of the file ../data/coverage.xml
# as an entry named "../data/coverage.xml" for the "test" stage
#
PIPELINECTL_USE_PATH_AS_KEY=1 save_result test ../data/coverage.xml
```
{: codeblock}

### list_results
{: #list_results}

```bash
# <stage>: Stage name
list_results <stage>
```
{: codeblock}

Lists the saved file names for a stage.

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
# <stage>: Stage name e.g. test, detect-secrets, static-scan
# <file>: File name e.g. mocha_results.json
load_result <stage> <file>
```
{: codeblock}

Prints the saved file keys to `stdout`. By default, a key is the workspace-relative path of the provided file path in `save_result`. Using the `PIPELINECTL_USE_PATH_AS_KEY` feature flag, a key is the path of the provided file path in `save_result`. To get the exact list of keys, use `list_results`.

Example:

```bash
load_result test mocha_results.json

#
# Using the `PIPELINECTL_USE_PATH_AS_KEY` environment variable
PIPELINECTL_USE_PATH_AS_KEY=1 load_result test ../data/coverage.xml
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
# <key>: Key of the artifact e.g. app-image, baseimage etc.
# <prop>: Type of property e.g. name, type, tags, signature
# <value>: Value of the property
save_artifact <key> [<prop>=<value> ...]
```
{: codeblock}

Registers a new build artifact with the pipeline or updates an existing one.

#### Container images
{: #pipelinectl-container-images}

Some suggested properties that you can use:

- `type`: Must be `image`.
- `name`: A fully qualified image name. For example, something that can be used by `docker pull`.
- `signature`: A valid signature.
- `digest`: A `sha256` digest.
- `source`: For example, `http://<some-git-url>/blob/<commithash>/<path-to-file>`

Any properties can be set on top of these properties.

The `name` property must also contain the tag for the image.
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


#### Using stdin as a value source
{: #stdin-value-source-save-artifact}

Values can be provided from stdin, if the following are true:

- The content is streamed for the command
- One property does not have a value and `=`

Example:

```bash
command_with_large_output | save_artifact ui_service "issues"

# this also works with multiple properties,
# but stdin can provide value for only a single one
command_with_large_output | save_artifact ui_service "issues" "result=success" "signature=${SIGNATURE}"
```
{: codeblock}

If multiple values are missing with `=` the command exits with an error, because it cannot determine which property belongs to the value on stdin.

Properties without a value but still appending `=` have an empty string as a value.

```bash
save_artifact ui_service "bar="
load_artifact ui_service bar # returns an empty string
```
{: codeblock}

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
# <key>: Name of the artifact e.g. app-image, baseimage etc.
# <prop>: Type of property e.g. name, type, tags, signature
load_artifact <key> <prop>
```
{: codeblock}

Prints the `<prop>` of artifact `<key>` to `stdout`.

Example:

```bash
SIGNATURE=$(load_artifact ui_service signature)
```
{: codeblock}

Used together with `list_artifacts`

```bash
#
# iterate over all artifacts and print their image names
#
while read -r key; do
  image=$(load_artifact $key name)
  echo "Artifact saved as '$key' is named: '$image'"
done < <(list_artifacts)
```
{: codeblock}

Outputs the following lines to the console:

```text
 Artifact saved as 'ui_service' is named: 'us.icr.io/team_namespace/ui_service:2.4.3'
 Artifact saved as 'backend_service' is named: 'us.icr.io/team_namespace/backend_service:2.4.3'
```
{: screen}

### serialize
{: #serialize}

Serialize `pipelinectl` data into a transferable JSON file to be used as payload for pipeline webhook triggers. It can serialize repositories set by `save_repo`, artifacts set by `save_artifact`, and environment variables set by `set_env`.

(Optional) flags:

```bash
--all-repos         # all the repository information set by `pipelinectl`
--all-artifacts     # all the artifacts information set by `pipelinectl`
```
{: codeblock}

Example:

The following code saves all the repositories, all the artifacts and `<env_variable1>`, `<env_variable2>` into the `foo.json` file:

```bash
pipelinectl serialize --all-repos --all-artifacts <env_variable1> <env_variable2> > foo.json
```
{: codeblock}

> This command is not an alias, you need `pipelinectl` explicitly

### deserialize
{: #deserialize}

Deserialize the `pipelinectl` from JSON to files, so `pipelinectl` can work in the triggered pipeline. Use the JSON that is serialized by the `pipelinectl serialize` command as the argument.

Example:

```bash
pipelinectl deserialize ./foo.json
```
{: codeblock}

> This command is not an alias, need `pipelinectl` explicitly

## Low-level methods
{: #low-level methods}

These methods are only exposed for the sake of completeness. Use the methods only on rare occasions.

### put_data
{: #put_data}

```bash
# <key>: Name of the data
# <prop>: Type of property e.g. name, type, tags, signature
# <value>: Value of the property
put_data <key> <prop> <value>
```
{: codeblock}

Sets `prop` to `value` for the entry defined by `key`.

### get_data
{: #get_data}

```bash
# <key>: Key of data
# <prop>: Type of property e.g. name, type, tags, signature
# <value>: Value of the property
get_data <key>
get_data <key> <prop>
```
{: codeblock}

Prints `prop` of the entry that is defined by `key`. If `prop` is not provided, it returns all of the `prop`s for the `key`. Returns a nonzero exit code when `key` has no `prop`.

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

Prints the string that is stored in `key`.
