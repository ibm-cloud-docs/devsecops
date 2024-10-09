---

copyright:
  years: 2024
lastupdated: "2024-05-24"

keywords: DevSecOps, inventory model, inventory, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring cloning of Git submodules
{: #cd-devsecops-clone-submodules}

The process of create a copy of your Git repository (repo) in your local computer is called a *Git cloning*. The copy that is created in your local computer is called as *Git Clone*.

By default, the Git clones in the pipelines do not clone the submodules in a repository. However, you can enable cloning the submodules in the user repositories (application repo and one-pipeline configuration repo) by setting either one of the opt-in flags.
{: shortdesc}

Currently, Git clone in pipelines uses HTTPS, SSH is not supported. To clone the submodules, the ``.gitmodules`` file in the Git repository must use either HTTPS URLs or relative URLs.
{: note}

The opt-in flags are as follows:

* `opt-in-clone-submodules`
* `opt-in-clone-remote-submodules`

## Opt-in parameters
{: #cd-devsecops-submodule-params}

| Parameter name | Description |
|-|-|
| `opt-in-clone-submodules` | Set to 1 to enable cloning of the submodules. Clones with the `--recurse-submodules` Git option |
| `opt-in-clone-remote-submodules` | Set to 1 to enable cloning of the submodules by using the status of the submodule's remote tracking branching. Clones with `--recurse-submodules --remote-submodules` Git options.|
{: caption="Optional parameters for cloning git submodules" caption-side="top"}
