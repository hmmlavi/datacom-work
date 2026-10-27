# GitHub Actions Troubleshooting

Use this guide when the **First workflow** fails or does not behave as expected.

## 1. The workflow did not start

- Open the repository's **Actions** tab and confirm Actions are enabled.
- Check that the workflow file is under `.github/workflows/` and uses a `.yml` or `.yaml` extension.
- Confirm the event matches the branch and action you performed. This repository's workflow runs on pushes to `main`, pull requests targeting `main`, and manual dispatch.
- For a manual run, open **Actions → First workflow → Run workflow** and select the intended branch.

## 2. A workflow run failed

1. Open **Actions** and select the failed run.
2. Open the failed job, then expand the first step marked with a red cross.
3. Read the error around the first failure rather than starting with the final summary.
4. Fix the cause, commit the change, and rerun the workflow.

## 3. A repository variable is empty

- Open **Settings → Secrets and variables → Actions → Variables**.
- Confirm the variable name matches the workflow exactly, including capitalization.
- Check that the variable is available at the repository, environment, or organization scope used by the workflow.
- This repository's `OWNER_TEAM` variable is optional and has a fallback, so an unset value may be expected.

## 4. A secret or token is unavailable

- Store sensitive values as Actions secrets; do not print their values in logs or commit them to the repository.
- Check that the workflow references the correct secret name.
- Review workflow permissions under **Settings → Actions → General** and the workflow's `permissions:` block.
- The built-in `GITHUB_TOKEN` is scoped to the repository and its permissions may need to be declared explicitly for write operations.

## 5. A pull-request run behaves differently from a push run

- Compare the event name, branch, and permissions shown in the run details.
- Fork-originated pull requests may receive restricted token permissions and may not have access to repository secrets.
- Never work around these restrictions by exposing secrets or executing untrusted code with elevated permissions.

## 6. Validate the fix

- Rerun the failed workflow, or use **Run workflow** if manual dispatch is enabled.
- Confirm every required job passes.
- Check the run summary and logs for the expected output.
- If the problem persists, include the failing step name and the relevant error message when asking for help. Remove tokens, secrets, and other sensitive values first.

## Related guides

- [Git and GitHub troubleshooting](git-troubleshooting.md)
- [GitHub Actions variables](github-actions-variables.md)
