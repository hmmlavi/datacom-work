# Variables in GitHub Actions (step 5)

A workflow is just YAML, so **every value you need at run time comes from a
variable**. There are four families, and knowing which is which is the key to
writing your first workflow.

---

## 1. `github` context variables — facts about this run

Injected automatically; describe *who/what/where* triggered the workflow.

| Variable | Example value |
| --- | --- |
| `github.repository` | `hmmlavi/datacom-work` |
| `github.repository_owner` | `hmmlavi` |
| `github.actor` | `hmmlavi` |
| `github.event_name` | `push`, `pull_request`, `workflow_dispatch` |
| `github.ref` | `refs/heads/main` |
| `github.ref_name` | `main` |
| `github.sha` | `7669193a755b4c99a99f6f2fb3ee26e97d76cc45` |
| `github.run_id` / `github.run_number` | `10512345678` / `4` |
| `github.workflow` / `github.job` | `First workflow` / `greet-and-report` |
| `github.server_url` + `github.repository` | builds the repo link |

Full list: <https://docs.github.com/en/actions/learn-github-actions/contexts>

## 2. Default environment variables — facts about the runner

Also automatic, but exposed as normal shell environment variables, so inside a
`run:` block you use `$NAME` (no `${{ }}` needed).

| Variable | Meaning |
| --- | --- |
| `RUNNER_OS` | `Linux`, `Windows`, `macOS` |
| `RUNNER_NAME` / `RUNNER_ARCH` | runner label / `X64`, `ARM64` |
| `GITHUB_WORKSPACE` | checked-out repo path |
| `GITHUB_SHA` / `GITHUB_REF` | same facts as the `github` context |
| `GITHUB_ACTIONS` | `true` — handy for "am I in CI?" checks |
| `HOME` / `PWD` / `PATH` | ordinary shell variables |

Full list: <https://docs.github.com/en/actions/learn-github-actions/variables#default-environment-variables>

## 3. Custom variables — values *you* define

Three places you can define them:

```yaml
on: push
env:                       # (a) workflow level — visible to every job
  TEAM_NAME: Datacom

jobs:
  build:
    runs-on: ubuntu-latest
    env:                   # (b) job level
      REGION: eu-west-1
    steps:
      - name: Greet
        env:               # (c) step level — narrowest scope wins
          GREETING: Hello
        run: echo "$GREETING from $TEAM_NAME in $REGION"
```

You can also store **configuration variables** in the GitHub UI so they are
reusable across workflows without hard-coding them:

- Repository → **Settings → Secrets and variables → Actions → Variables** tab
- Referenced as `${{ vars.MY_VARIABLE }}`
- Scope: repository, environment, or organisation (org/enterprise need permissions)

## 4. Secrets — sensitive values

Same UI, **Secrets** tab (`${{ secrets.MY_SECRET }}`). Masked in logs, never
echoed back in plain text.

- `secrets.GITHUB_TOKEN` — created automatically for every run, scoped to *this*
  repository only, expires when the job ends. Use it for API calls, releasing,
  commenting on PRs. Permissions are set with `permissions:` in the workflow.
- Your own secrets (e.g. `API_KEY`) — add them in Settings before referencing them.

## 5. Step outputs & job outputs — variables you create at run time

```yaml
- name: Capture
  id: stamp                       # 'id' lets other steps refer to it
  run: echo "now=$(date -u +%FT%TZ)" >> "$GITHUB_OUTPUT"

- name: Use it
  run: echo "Captured at ${{ steps.stamp.outputs.now }}"
```

---

## The two syntaxes — the #1 beginner trap

| Where | Syntax | Example |
| --- | --- | --- |
| **Expression** (evaluated by Actions *before* the shell sees it) | `${{ ... }}` | `${{ github.actor }}`, `${{ vars.TEAM }}`, `${{ secrets.TOKEN }}` |
| **Shell environment variable** (evaluated by bash/PowerShell) | `$NAME` | `$RUNNER_OS`, `$GITHUB_WORKSPACE` |

Rules of thumb:

1. `github.*`, `vars.*`, `secrets.*`, `steps.*.outputs.*`, `env.*` → **`${{ }}`**.
2. Anything already exported into the shell (`RUNNER_OS`, `GITHUB_SHA`, plus
   whatever you listed under `env:`) → **`$NAME`**.
3. **Never interpolate a secret directly into a script line.** Do this instead:

   ```yaml
   - name: Safe
     env:
       TOKEN: ${{ secrets.GITHUB_TOKEN }}
     run: curl -H "Authorization: Bearer $TOKEN" https://api.github.com/...
   ```

   Passing it through `env:` keeps the value out of the command text (which is
   itself logged) and lets GitHub mask it.
4. Prefer `${{ github.event_name }}` over `$GITHUB_EVENT_NAME` when you want a
   value *decided by Actions* — and prefer the env var when you want the shell to
   do the work. Both are valid; consistency is what matters.

### Links

- Variables — <https://docs.github.com/en/actions/learn-github-actions/variables>
- Contexts — <https://docs.github.com/en/actions/learn-github-actions/contexts>
- Expressions — <https://docs.github.com/en/actions/learn-github-actions/expressions>
- Using secrets — <https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions>
- Workflow syntax — <https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions>
