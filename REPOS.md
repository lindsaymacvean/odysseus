# Repository URLs

Reference clone URLs for **Odysseus** (project codename) repositories.

| Repo | URL | Role |
|------|-----|------|
| **odysseus** | https://github.com/lindsaymacvean/odysseus | AI Loom coordination workspace (this repo) |
| **ai-marketing-box** | https://github.com/bennettinnovations/ai-marketing-box | LinkedIn automation, deployed to Mac Studio via rsync |
| **eircode-scraper** | git@github.com:bennettinnovations/eircode-scraper.git | Eircode data scraping, runs on Mac Studio |

## Branch and workflow

- **odysseus**: Push direct to **main** (no PRs required).
- **ai-marketing-box**: PRs to **main**; CI deploys to Mac Studio via rsync.
- **eircode-scraper**: PRs to **main**.

## Work directory and cloning for any fix

For any fix that needs one or more of these repos cloned locally: create a run directory under `work/<purpose>-<date>/`, look up this file for clone URLs, and clone only the repos you need. See **[runbooks/general-fix.md](./runbooks/general-fix.md)** for the full pattern (create run dir → clone from REPOS.md → task work → back out).

---

## Clone examples

```bash
# Mac Studio apps
git clone https://github.com/bennettinnovations/ai-marketing-box.git
git clone git@github.com:bennettinnovations/eircode-scraper.git
```
