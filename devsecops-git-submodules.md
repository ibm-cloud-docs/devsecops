---

copyright: 
  years: 2023, 2023
lastupdated: "2023-02-23"

keywords: DevSecOps, IBM Cloud, git clone, submodules

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring cloning of Git submodules
{: #cd-devsecops-clone-submodules}

By default, the Git clone in the pipelines do not clone the submodules in a repository (repo). However, you can enable cloning the submodules in the user repositories (application repo and one-pipeline configuration repo) by setting either one of the opt-in flags.
{: shortdesc}

Currently, `git clone` in pipelines uses HTTPS, SSH is not supported. To clone the submodules, the ``.gitmodules`` file in the Git repository must use either HTTPS URLs or relative URLs.
{: note}

The opt-in flags are as follows:

* Set the opt-in flag `opt-in-clone-submodules` to 1 to enable cloning of the submodules.
* Set the opt-in flag `opt-in-clone-submodules-with-remote` to 1 to enable cloning of the submodules by using the status of the submodule’s remote-tracking branch to update the submodule, rather than the superproject’s recorded SHA-1.

## Opt-in parameters
{: #cd-devsecops-submodule-params}

| Parameter name | Description |
|-|-|
| `opt-in-clone-submodules` | Set to 1 to enable cloning of the submodules. Clones with the `--recurse-submodules` Git option |
| `opt-in-clone-submodules-with-remote` | Set to 1 to enable cloning of the submodules by using the status of the submodule's remote tracking branching. Clones with `--recurse-submodules --remote-submodules` Git options.|
{: caption="Table 1. Optional parameters for cloning git submodules" caption-side="top"}
