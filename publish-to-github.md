# Publish this app to GitHub

Put your Streamlit bills chat on a **public** GitHub repository so you can share the link. These steps assume the app already runs locally (`rag.py`, `chat.py`, `pyproject.toml`, `uv.lock`, and the PDFs in `bills/`).

You are publishing to **your** GitHub account. Do not push to someone else's repository.

## Before you start

1. Create a free account at [github.com/signup](https://github.com/signup) if you do not have one.
2. Install Git. In a terminal, run:

```bash
git --version
```

If that fails:

- macOS: run `xcode-select --install`, or `brew install git`
- Windows: install [Git for Windows](https://git-scm.com/download/win), then use **Git Bash**
- Linux: `sudo apt install git` (Debian/Ubuntu) or your distro's package

3. Tell Git who you are. Use the name and email on your GitHub account. You only do this once per computer:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

4. Open a terminal **in this project folder** (the folder that contains `rag.py`).

```bash
pwd
ls
```

You should see `rag.py`, `pyproject.toml`, and a `bills/` folder.

## What gets published, and what stays on your machine

| Commit this | Leave this out |
|---|---|
| `rag.py`, `chat.py`, `hello.py` | `.venv/` (the virtual environment; it is hundreds of MB) |
| `pyproject.toml`, `uv.lock` | `__pycache__/` |
| `bills/*.pdf` | `.chroma/` (the local search index; the app rebuilds it) |
| `.gitignore` | `.env` and `.streamlit/secrets.toml` |

The Vocareum API key is typed into the Streamlit sidebar. Never paste it into a file, a commit message, or the GitHub website.

This folder already has a `.gitignore` that excludes the rows in the right-hand column. If yours is missing, create `.gitignore` with exactly these lines before you commit:

```gitignore
.venv/
__pycache__/
*.py[cod]
.chroma/
.env
.streamlit/secrets.toml
```

## 1. Start a repository

Skip this step if `git status` already works in this folder.

```bash
git init -b main
```

## 2. Check the file list before you commit

```bash
git status
```

Read the list. You want your Python files, `uv.lock`, `.gitignore`, and the PDFs. You do **not** want `.venv`, `__pycache__`, or `.chroma`.

If a forbidden folder appears, the `.gitignore` is missing or was added after those files were already tracked. Fix the ignore file, then run:

```bash
git rm -r --cached .venv __pycache__ .chroma
```

That removes them from Git only. The folders stay on your disk.

## 3. Save the first commit

```bash
git add .
git status
git commit -m "Add Streamlit RAG chat over legislative bills."
```

Run `git status` again after `git add .` and before `git commit`. That second look is the moment you catch a secret or a virtual environment.

`git status` should now say `nothing to commit, working tree clean`.

## 4. Create an empty public repository on GitHub

1. Go to [github.com/new](https://github.com/new).
2. **Repository name:** `bills-rag` (or another short name; letters, numbers, and hyphens only).
3. **Description:** `Streamlit RAG chat over legislative bills`
4. Choose **Public**.
5. Leave **Add a README**, **Add .gitignore**, and **Choose a license** unchecked. Your project already has its first commit. Extra files on GitHub make the first push fail.
6. Click **Create repository**.

Leave the next page open. GitHub shows the commands under **…or push an existing repository from the command line**. Use those if the remote URL differs from the one below. Replace `YOUR_USERNAME` with your GitHub username.

## 5. Connect your folder and push

```bash
git remote add origin https://github.com/YOUR_USERNAME/bills-rag.git
git push -u origin main
```

GitHub will ask you to sign in. In the browser, approve access for Git. A password typed into the terminal will be rejected. If the browser prompt does not appear, use a [personal access token](https://github.com/settings/tokens) as the password, or the GitHub CLI path below.

## 6. Confirm it is public

Open `https://github.com/YOUR_USERNAME/bills-rag`.

Check all of these:

- The badge next to the repo name says **Public**.
- `rag.py` and `bills/` are listed.
- `.venv` and `.chroma` are **not** listed.
- Searching the repo for your API key returns nothing.

Copy that URL. That is the link you submit.

## Later changes

After you edit the app:

```bash
git status
git add .
git commit -m "Describe what you changed and why."
git push
```

## Optional: create the repo with GitHub CLI

If you install the [GitHub CLI](https://cli.github.com/), you can replace steps 4 and 5 with:

```bash
gh auth login
gh repo create bills-rag --public --source=. --remote=origin --push --description "Streamlit RAG chat over legislative bills"
```

`gh` prints the new repository URL when the push finishes.

## Common errors

| What you see | What to do |
|---|---|
| `fatal: not a git repository` | You are in the wrong folder. `cd` into the folder that contains `rag.py`, then run `git init -b main`. |
| `Author identity unknown` | Run the `git config --global user.name` and `user.email` commands in **Before you start**. |
| `remote origin already exists` | This folder already points at a repo. Run `git remote -v`. If it is not yours, run `git remote remove origin`, then repeat step 5 with your URL. |
| `failed to push some refs` / `rejected` | The GitHub repo was created with a README. On [github.com/new](https://github.com/new), create a new empty repo, or delete the README commit on GitHub and push again. |
| `Authentication failed` or `Password authentication is not supported` | Sign in through the browser, `gh auth login`, or a personal access token. Your GitHub account password will not work. |
| `File ... is 100 MB or larger` | GitHub rejects files over 100 MB. These bill PDFs are small enough. Do not commit `.venv`. |
| Classmates can see your API key | Delete the key from the file, commit the fix, push, then **revoke that key** in Vocareum. Removing the line in a later commit does not erase it from older commits. |
