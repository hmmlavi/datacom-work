# Git & GitHub troubleshooting guide

This page covers common errors you may hit while following the onboarding steps.
Run commands from **Git Bash** unless a section says otherwise.

## 1. `Author identity unknown`

Git does not know which name and email to attach to your commit.

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global --get user.name
git config --global --get user.email
```

Use an email associated with your GitHub account, or your GitHub-provided
`noreply` address. These settings label commits; they do not sign you in.

## 2. `Permission denied` or authentication failed

- For HTTPS remotes, complete the browser sign-in prompt from Git Credential
  Manager.
- GitHub account passwords are not accepted for Git-over-HTTPS. Use the
  credential manager or an appropriately scoped personal access token when
  prompted.
- For SSH remotes, verify that your public key is added to your GitHub account
  and test it with `ssh -T git@github.com`.

Check which remote your repository uses:

```bash
git remote -v
```

Never paste access tokens or private keys into an issue, commit, workflow log,
or chat.

## 3. `remote origin already exists`

A remote named `origin` is already configured. Inspect it before changing it:

```bash
git remote -v
git remote set-url origin https://github.com/OWNER/REPOSITORY.git
```

Replace the example URL with your own repository URL. If the existing remote is
correct, no change is needed.

## 4. `rejected ... fetch first` or `non-fast-forward`

The remote branch contains commits that your local branch does not have. First
check your working tree and branch:

```bash
git status
git branch --show-current
git pull --rebase
```

Resolve any conflicts, run `git status`, then continue the rebase if Git asks
you to. Only push after the local and remote histories are reconciled. Avoid
`git push --force` on shared branches; it can overwrite other people's work.

## 5. `src refspec main does not match any`

Common causes are that no commit exists yet or your current branch has a
different name.

```bash
git status
git branch --show-current
git log -1 --oneline
```

If the repository has no commit yet, add a file and create the first commit:

```bash
git add README.md
git commit -m "Add project README"
```

If your branch is not called `main`, push the branch name shown by
`git branch --show-current`, or intentionally rename it after checking your
repository's default-branch setup.

## 6. A GitHub Actions workflow did not run

Check these items in order:

1. Open the repository's **Actions** tab and inspect the latest run, if one exists.
2. Confirm the workflow YAML is under `.github/workflows/` and parses correctly.
3. Check whether the event and branch match the workflow's `on:` filters.
4. Check that Actions are permitted by repository or organization settings.
5. Open the failed job and read the first failing step; later errors may be
   consequences of that first failure.

For this repository, the **First workflow** can also be started manually from
**Actions → First workflow → Run workflow**.

## 7. A secret or variable is empty in a workflow

- Confirm its exact name under **Settings → Secrets and variables → Actions**.
- Secrets use `${{ secrets.NAME }}`; configuration variables use
  `${{ vars.NAME }}`.
- A secret may be unavailable to workflows triggered by certain events, such as
  workflows from forks.
- Pass secrets through a step's `env:` block rather than writing their value
  directly into a command.
- Do not print secret values to debug a workflow.

## Before asking for help

Include the command you ran, the complete error text, the relevant
(non-secret) workflow or configuration snippet, and what you already tried.
Remove tokens, passwords, private keys, and other sensitive values first.

Useful references:

- [Git documentation](https://git-scm.com/docs)
- [GitHub: troubleshooting Git](https://docs.github.com/en/get-started/using-git/troubleshooting-the-2-most-common-git-errors)
- [GitHub Actions: debugging workflows](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows)
