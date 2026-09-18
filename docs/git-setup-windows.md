# Git setup & configuration — Windows (Git Bash)

Covers steps **2–4** of the onboarding checklist: install Git, configure your
identity, create the repository on GitHub, and clone it to your machine.

Every command below is typed into **Git Bash** (search "Git Bash" in the Windows
Start menu after installing). Press <kbd>ENTER</kbd> after each line.

---

## 1. Install Git

1. Download the installer from <https://git-scm.com/download/win>.
2. Run it and accept the defaults. The important screens:
   - **Adjusting your PATH environment** → choose *"Git from the command line and
     also from 3rd-party software"* (this is what makes `git` work in PowerShell/CMD too).
   - **HTTPS transport backend** → *"Use the OpenSSL library"*.
   - **Line ending conversions** → *"Checkout Windows-style, commit Unix-style line endings"*.
   - **Terminal emulator** → *"Use MinTTY"* (the default Git Bash terminal).
3. Verify the install:

   ```bash
   git --version
   ```

   You should see something like `git version 2.45.1.windows.1`.

---

## 2. Configure your identity (per the Word/PDF document in Resources)

Git stamps every commit with a name and an email. **The email must be the same
one you use for GitHub**, otherwise your commits will not be attributed to your
GitHub profile.

### Check what is already configured

```bash
git config --list
```

- If a `user.name` and a `user.email` appear **and both are correct** → you can
  move on to the next step (cloning).
- Otherwise, exit the list and set them:

  1. Type `q` and <kbd>ENTER</kbd> to exit the list.
  2. Set your name (replace `Samuel Haven` with **your** name):

     ```bash
     git config --global user.name "Samuel Haven"
     ```

  3. Set your email (replace it with **your** GitHub email):

     ```bash
     git config --global user.email "samuel.haven@gmail.com"
     ```

### Re-check

```bash
git config --list
```

or just the two values:

```bash
git config --global user.name
git config --global user.email
```

> **Notes**
> - Keep the double quotes — your name contains a space.
> - `--global` writes to `C:\Users\<you>\.gitconfig` and applies to every
>   repository on this machine. Drop `--global` (while inside a repository) to
>   set the identity for that one repository only — useful if you commit with a
>   work account and a personal account from the same computer.
> - If your GitHub account uses a private/noreply email
>   (`12345678+username@users.noreply.github.com`), use that value; find it under
>   GitHub → **Settings → Emails**.
> - Also worth setting once:
>   ```bash
>   git config --global init.defaultBranch main
>   git config --global core.autocrlf true
>   ```

---

## 3. Create a new repository on GitHub (step 1)

1. Sign in at <https://github.com> and click **+** (top-right) → **New repository**.
2. Fill in:
   - **Repository name** — short, lowercase, hyphens (e.g. `datacom-work`).
   - **Description** — optional one-liner.
   - **Visibility** — *Public* (or *Private*, per your team's policy).
3. For a truly **empty** repository, leave *all* initialisation options
   **unchecked** (no README, no `.gitignore`, no licence). This is what lets you
   use the "Cloning an empty repository" path below.
4. Click **Create repository**. GitHub shows the *Quick setup* page with your new
   repository URL, e.g.

   ```text
   https://github.com/<your-username>/datacom-work.git
   ```

---

## 4. Clone the empty repository to your machine (step 4)

Start from **"Cloning an empty repository"** on GitHub's Quick setup page.

### Option A — clone, then push (recommended)

```bash
# create a folder for your work and enter it
mkdir -p ~/Documents/repos && cd ~/Documents/repos

# clone the empty repository (creates the folder datacom-work)
git clone https://github.com/<your-username>/datacom-work.git

cd datacom-work

# add your first file, stage it, commit it
echo "# datacom-work" > README.md
git add README.md
git commit -m "Initial commit"

# publish it to GitHub
git push -u origin main
```

> `-u` (short for `--set-upstream`) links your local `main` to `origin/main`, so
> later you only need `git push` / `git pull`.

### Option B — the exact commands from GitHub's Quick setup page

If you already have files in an existing folder:

```bash
cd <your-existing-folder>
git init -b main
git remote add origin https://github.com/<your-username>/datacom-work.git
git add .
git commit -m "Initial commit"
git push -u origin main
```

### Authentication

The first `git push` opens a browser window — sign in to GitHub and approve
**Git Credential Manager**. Your credentials are then cached, so you will not be
asked again. (Password authentication was removed in 2021; if a tool asks for a
password, use a **Personal Access Token** instead:
GitHub → **Settings → Developer settings → Personal access tokens**.)

### Verify

```bash
git status
git log --oneline
git remote -v
```

Refresh the repository page in your browser — your `README.md` should be there.

---

## 5. Everyday commands cheat sheet

| Command | What it does |
| --- | --- |
| `git status` | Shows changed / staged / untracked files |
| `git add <file>` / `git add .` | Stage one file / everything |
| `git commit -m "message"` | Record the staged changes |
| `git push` | Send commits to GitHub |
| `git pull` | Fetch and merge changes from GitHub |
| `git checkout -b my-feature` | Create and switch to a new branch |
| `git switch main` | Go back to the `main` branch |
| `git log --oneline --graph` | Compact history |
| `git diff` | Unstaged changes |

---

## Links used for this step

- Git downloads — <https://git-scm.com/downloads>
- Git terminal user guide — <https://git-scm.com/book/en/v2/Getting-Started-The-Command-Line>
- GitHub: creating a repo — <https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-new-repository>
- GitHub: cloning a repo — <https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository>
- `git config` reference — <https://git-scm.com/docs/git-config>
