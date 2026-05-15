# Repository URLs

Reference clone URLs for **Odysseus** (project codename) repositories.

| Repo | URL | Role |
|------|-----|------|
| **odysseus** | https://github.com/lindsaymacvean/odysseus | AI Loom coordination workspace (this repo) |

Application repositories will be added here as the project grows.

## Branch and workflow

- **odysseus**: Trunk-based; work via PRs into **main**.

## Work directory and cloning for any fix

For any fix that needs one or more of these repos cloned locally: create a run directory under `work/<purpose>-<date>/`, look up this file for clone URLs, and clone only the repos you need. See **[runbooks/general-fix.md](./runbooks/general-fix.md)** for the full pattern (create run dir → clone from REPOS.md → task work → back out).

---

## Clone examples

```bash
# Application repos (add as they are created)
# git clone https://github.com/lindsaymacvean/odysseus-api.git
# git clone https://github.com/lindsaymacvean/odysseus-web.git
```
