---

copyright:
  years:  2025, 2025
lastupdated: "2025-03-31"

keywords: Fine-Grained, Personal Access Tokens, permission, Classic token

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Understanding Personal Access Tokens (PATs)

Personal Access Tokens (PATs) are authentication credentials used to access and interact with repositories on platforms like GitHub. They serve as an alternative to passwords and are commonly used for automation, scripts, and API access. There are two types of PATs:
- Fine-Grained Personal Access Tokens
- Classic Personal Access Tokens

Each type offers different levels of security and flexibility.

## Fine-Grained Personal Access Tokens (More Secure & Restricted)
Fine-grained tokens provide better security because they allow you to precisely control access. However, they are more restrictive than classic tokens.

### Key Features:

1. **Limited to one owner** – These tokens only work for repositories owned by a specific user or organization. They won’t work if you need access to someone else’s repo.
2. **Repo-specific access** – You can select exactly which repositories the token applies to, reducing unnecessary access.
3. **Granular permission control** – You can define permissions per feature (e.g., read-only vs. write). This is safer than the broad permissions of classic tokens.

#### Minimum Required Permissions for Fine-Grained Tokens:

| Scope | Permissions |
| ---------|------------ |
| `Administration` | Read |
| `Commit statuses` | Read/Write |
| `Contents` | Read/Write |
| `Issues` | Read/Write |
| `Metadata` | Read |
| `Pull requests` | Read/Write |
| `Webhooks` | Read/Write |

## Personal Access Tokens (Classic) (Less Secure but More Flexible)

Classic tokens are more flexible but less secure because they provide broader access. They are still required in some situations where fine-grained tokens won’t work.

### Key Features:

1. **Required for writing to public repositories you don’t own** – If you need to push changes to a public repo not owned by you, only classic tokens work.
2. **Needed for outside collaborators** – If you are working on a private repository as an outside collaborator (not a full organization member), you need a classic token.

#### Minimum Required Permissions for Classic Tokens:

| Scope |
| --------- |
| `admin:org` |
| `admin:repo_hook` |
| `repo` |
| `user` |
