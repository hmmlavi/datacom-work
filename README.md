# datacom-work

Onboarding repository for GitHub basics: install and configure Git, create a
repository, clone it, understand **variables**, and run a **GitHub Actions
workflow**.

**Repository link:** <https://github.com/hmmlavi/datacom-work>
**Actions tab:** <https://github.com/hmmlavi/datacom-work/actions>

---

## Onboarding checklist

| # | Task | Status | Where |
| --- | --- | --- | --- |
| 1 | Create a new repository | :white_check_mark: | <https://github.com/hmmlavi/datacom-work> |
| 2 | Read the terminal user guide | :white_check_mark: | [git-scm.com — The Command Line](https://git-scm.com/book/en/v2/Getting-Started-The-Command-Line) |
| 3 | Download & install Git + configure identity | :white_check_mark: | [docs/git-setup-windows.md](docs/git-setup-windows.md) |
| 4 | Clone the empty repository locally | :white_check_mark: | [docs/git-setup-windows.md → §4](docs/git-setup-windows.md#4-clone-the-empty-repository-to-your-machine-step-4) |
| 5 | Understand variables | :white_check_mark: | [docs/github-actions-variables.md](docs/github-actions-variables.md) |
| 6 | Create the first GitHub Actions workflow | :white_check_mark: | [.github/workflows/first-workflow.yml](.github/workflows/first-workflow.yml) |

---

## Step 3 in short — configure Git (Windows / Git Bash)

```bash
git --version                     # confirm the install
git config --list                 # check for an existing user.name / user.email
q                                 # exit the list if it opened a pager

git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"   # same email as GitHub

git config --list                 # verify
```

Full walkthrough, including the installer screens and the credential-manager
prompt: **[docs/git-setup-windows.md](docs/git-setup-windows.md)**.

## Step 4 in short — clone the (empty) repository

```bash
mkdir -p ~/Documents/repos && cd ~/Documents/repos
git clone https://github.com/hmmlavi/datacom-work.git
cd datacom-work
echo "# datacom-work" > README.md
git add README.md
git commit -m "Initial commit"
git push -u origin main
```

## Step 6 — the workflow

`.github/workflows/first-workflow.yml` runs on:

- `push` to `main`
- `pull_request` targeting `main`
- `workflow_dispatch` — manually, from **Actions → First workflow → Run workflow**

It prints one labelled line per variable family so you can read the log and see
exactly where each value came from:

1. **`github` context** — `github.actor`, `github.ref_name`, `github.sha`, `github.run_number`, …
2. **Default environment variables** — `$RUNNER_OS`, `$GITHUB_WORKSPACE`, `$GITHUB_SHA`, …
3. **Custom variables** — workflow-level `env:`, job-level `env:`, step-level `env:`, plus `${{ vars.OWNER_TEAM }}` from the repo settings (optional, has a fallback)
4. **Secrets** — `${{ secrets.GITHUB_TOKEN }}` passed safely through `env:` and used for a GitHub API call

It also demonstrates **step outputs** (`id:` + `$GITHUB_OUTPUT`) and writes a
job **summary** to the run page.

### Try it

1. Open <https://github.com/hmmlavi/datacom-work/actions>.
2. Select **First workflow** → **Run workflow** → type a greeting → run.
3. Open the job and compare the printed values with
   [docs/github-actions-variables.md](docs/github-actions-variables.md).

Optional: add a configuration variable named `OWNER_TEAM` under
**Settings → Secrets and variables → Actions → Variables** and re-run to see
`${{ vars.OWNER_TEAM }}` resolve.

---

## Repository layout

```text
.
├── .github/
│   └── workflows/
│       └── first-workflow.yml        # step 6 — first GitHub Actions workflow
├── docs/
│   ├── git-setup-windows.md          # steps 2–4 — install, configure, clone (Windows/Git Bash)
│   └── github-actions-variables.md   # step 5 — the four kinds of variables
└── README.md
```
