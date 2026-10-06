# Tools available to agents

This file lists the CLI tools and commands agents can use when working in this workspace. Use it when you need to run builds, deploy, authenticate, or work with a specific component.

**Context:** For when to use which repo and workflow, see **[CONTEXT.md](./CONTEXT.md)**. For clone URLs and branch conventions, see **[REPOS.md](./REPOS.md)**.

---

## Tools assumed available

| Tool | Typical use |
|------|-------------|
| **Git** | Clone, branch, commit, push; runbooks assume `work/` and multi-repo workflows. |
| **gh** (GitHub CLI) | PRs, workflows, API/GraphQL. |
| **Node / npm / yarn** | JavaScript/TypeScript projects (install, build, test, lint). |
| **AWS CLI** | Prototype infra and services in personal account (see Authentication). |

### Product / context tools (under evaluation)

Not installed by default — see [docs/product-brief.md](./docs/product-brief.md):

| Tool | Notes |
|------|--------|
| **Obsidian** | Local vaults as a context source |
| **Notion** | Structured team docs |
| **Mermaid** | Diagrams in markdown |
| **Paperclip** | Confirm scope in discovery doc |
| **Hermes** | Confirm scope in discovery doc |

---

## Authentication

### Mac Studio (Tailscale)

The always-on box is a **Mac Studio (64GB)** — prototype for the replicable "sovereign buddy" hardware. Tailscale hostname: `mini-01-591`.

| Setting | Value |
|---------|-------|
| **Host** | `mini-01-591.tailb6b3a8.ts.net` |
| **IP** | `100.74.108.103` |
| **User** | `betony220` |
| **Password** | Read from `/Users/lindsaymacvean/Workarea/eircode-scraper/.secrets/remote.env` (`SSH_PASSWORD`) |

**Load credentials and connect:**

```bash
# Load credentials
set -a && source /Users/lindsaymacvean/Workarea/eircode-scraper/.secrets/remote.env && set +a

# Connect with sshpass
export SSHPASS="$SSH_PASSWORD"
sshpass -e ssh -o StrictHostKeyChecking=accept-new "${SSH_USER}@${SSH_HOST}"
```

**Prerequisites:** Tailscale must be running on both machines. Check with `tailscale status` if connection fails.

**On the Studio:**
- Working directory: `/Users/betony220/`
- Dashboard (if deployed): port `8765`

---

### AWS (Odysseus prototype)

| Setting | Value |
|---------|--------|
| **Account** | `203712223134` (personal) |
| **Profile** | `odysseus` |
| **Region** | `eu-west-1` |
| **IAM user** | `local_admin` (long-lived keys in `~/.aws/credentials`) |

Use this profile for all Odysseus prototype work. Do not use Ruralis or other org profiles unless the user explicitly asks.

```bash
export AWS_PROFILE=odysseus
aws sts get-caller-identity   # should show Account 203712223134
```

Credentials live only on the developer machine (`~/.aws/`). Never commit access keys or `.env` secrets to the repo.

**Note:** The `default` profile on this machine uses `aws login` to the same account but sessions expire; prefer `odysseus` for agents and scripts.

---

## Common commands (per component)

Use these when working inside a cloned repo or the corresponding directory in the workspace.

<!-- CUSTOMIZE: Add commands for each of your components -->
<!-- Example:

### Frontend (React web app)

```bash
cd frontend
yarn install && yarn dev  # Dev server at localhost:3000
yarn build && yarn test && yarn lint
```

### Backend (Node.js API)

```bash
cd backend
npm install
npm run dev      # Local dev server
npm run test     # Run tests
npm run deploy   # Deploy to staging
```

### Infrastructure (Terraform)

```bash
cd infrastructure
terraform init
terraform plan
terraform apply
```
-->

---

## Quick reference

| Need | Tool / command |
|------|----------------|
| SSH to Mac Studio | Load creds from `eircode-scraper/.secrets/remote.env`, then `sshpass -e ssh ${SSH_USER}@${SSH_HOST}` |
| GitHub PRs / API | `gh pr view`, `gh pr create`, `gh api`, `gh api graphql` |
| AWS (Odysseus) | `export AWS_PROFILE=odysseus` then `aws sts get-caller-identity` |
| AWS region | `eu-west-1` (set on `odysseus` profile) |
