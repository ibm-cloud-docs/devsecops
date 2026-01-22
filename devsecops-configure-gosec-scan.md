---

copyright:
  years: 2023, 2026
lastupdated: "2026-01-21"

keywords: DevSecOps, gosec, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring GoSec scans
{: #devsecops-gosec}

Use [gosec](https://github.com/securego/gosec){: external} to inspect Golang source code in your scanned repositories.

## Required parameters
{: #devsecops-gosec-parms}

The pipeline needs the parameter in Table 1 to run the Gosec scan. For a full list of pipeline parameters, see [Pipeline parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm).


| Name | Type | Description | Required or Optional |
|--|--|--|--|
| `opt-in-gosec` | text   | To enable the gosec scan, set the property to any non empty value. To disable the scan, set it to an empty value or remove the property entirely. (Default is empty) | Required |
{: caption="gosec scan parameters" caption-side="top"}

## Enabling gosec scan for private Git repositories
{: #devsecops-gosec-private-repos}

If your project requires dependencies that are located in a private Git repository and therefore needs SSH-based authentication, you can enable the pipeline to retrieve the dependencies by providing optional parameters. For more information, see [Enable download of dependencies for private repositories](#devsecops-gosec-enable-download).

If a proxy must be specified for retrieving dependencies, you can specify its URL along with the credentials (username and token) by using pipeline parameters. For more information, see [Enable GOPROXY](#devsecops-gosec-enable-goproxy).

### Enable download of dependencies for private repositories
{: #devsecops-gosec-enable-download}

Downloading dependencies for repositories can be enabled by providing all of the following pipeline parameters.

| Name | Type | Description | Required or optional |
|--|--|--|--|
| `gosec-private-repository-url`    | text   | (deprecated) Your private repository base URL. | optional |
| `gosec-private-repository-host`    | text   | Your private repository host.  | optional |
| `gosec-private-repository-ssh-key`     | secret   | The SSH key for the private repository | optional |
| `gosec-private-repository-user` | text | User for the private repository with https access (default to `x-oauth-basic`) | optional |
| `gosec-private-repository-token` | secret | The token used for https access to private repository (default to the token configured for the first git integration in the toolchain whose repository URL is hosted on `gosec-private-repository-host`'s value) | optional |
{: caption="pipeline parameters" caption-side="top"}

### Enable GOPROXY
{: #devsecops-gosec-enable-goproxy}

If GOPROXY must be specified, all of the following parameters must be provided.

| Name | Type | Description | Required or optional |
|--|--|--|--|
| `gosec-proxy-virtual-repository-user`     | text   | The virtual repository user for gosec proxy | optional |
| `gosec-proxy-virtual-repository-token`     | secret   | The virtual repository token for gosec proxy | optional |
| `gosec-proxy-virtual-repository-url`     | text   | The virtual repository url for gosec proxy | optional |
{: caption="GOPROXY parameters" caption-side="top"}

## Optional parameters
{: #devsecops-gosec-params-opt}

The `gosec-scan-image` parameter can be used to specify a different gosec image to use, such as a custom image or a specific version of the official gosec image.

Usage of the `gosec-additional-flags` flag is shown in the [examples](#devsecops-gosec-examples) section below

| Parameter name | Default value | Description |
|-|-|-|
| `gosec-additional-flags` | text | Additional flags to be appended to the start of the gosec command. |
| `gosec-scan-image` | text | Specifies an alternative gosec image, including custom images or specific versions of the official image. |
{: caption="gosec optional parameters" caption-side="top"}

### Examples
{: #devsecops-gosec-examples}

Define the value of `gosec-additional-flags` as `-exclude-dir=<your_folder_1> -exclude-dir=<your_folder_2> -nosec=true` and the gosec command run is updated as:

```bash
./gosec -exclude-dir=<your_folder_1> -exclude-dir=<your_folder_2> -nosec=true -no-fail -fmt=json -out=gosec-results.json -stdout -verbose=text ./...
```
{: codeblock}

### Using your own configuration file
{: #devsecops-gosec-config-file}

If you want to modify the default configuration, create a `config.json` file in the target repository.
For more information on the analysis parameters in the configuration file, see [gosec configuration](https://github.com/securego/gosec#configuration){: external}.

### Using another static scan implementation
{: #devsecops-gosec-otherscan}

If you want to use your own static scan implementation, modify your `.pipeline-config.yaml` file and add your own custom script to the `static-scan` stage. For more information, see [Custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize).
