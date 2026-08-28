# Set up CDet_replay with personal submodule forks

These instructions configure `CDet_replay` so that each repository's `origin`
points to your GitHub fork while `upstream` points to the source repository.

## 1. Fork the repositories

On GitHub, fork these repositories into your account:

- `btspaude/CDet_replay`
- `btspaude/CDet`
- `btspaude/SBS-offline`
- `btspaude/SBS-replay`
- `btspaude/jlab-HPC`
- `JeffersonLab/panguin`

Keep the original repository names when creating the forks.

## 2. Configure the `github:user` URL alias

The parent repository's `.gitmodules` file uses URLs such as:

```text
github:user/SBS-offline.git
```

Configure Git to translate `github:user/` into your personal GitHub SSH URL.
Replace `YOUR_USERNAME` with your GitHub username:

```sh
git config --global \
  url."git@github.com:YOUR_USERNAME/".insteadOf \
  "github:user/"
```

Verify the alias:

```sh
git config --global --get-regexp '^url\.'
```

Expected output includes:

```text
url.git@github.com:YOUR_USERNAME/.insteadof github:user/
```

This alias must be configured before initializing the submodules.

## 3. Clone your parent fork

```sh
cd "$HOME"

git clone \
  "git@github.com:YOUR_USERNAME/CDet_replay.git" \
  CDet_replay

cd CDet_replay
```

Add the source parent repository as `upstream`:

```sh
git remote add upstream git@github.com:btspaude/CDet_replay.git
```

Verify the parent remotes:

```sh
git remote -v
```

## 4. Initialize the submodules

Synchronize the local submodule configuration and clone every submodule:

```sh
git submodule sync --recursive
git submodule update --init --recursive
```

The `github:user` URL alias makes the following submodules clone from your
forks automatically:

- `CDet`
- `SBS-offline`
- `SBS-replay`
- `jlab-HPC`

The `panguin` entry uses a direct Jefferson Lab URL, so it is reconfigured
separately below.

## 5. Configure submodule remotes

Run these commands from the root of `CDet_replay`.

### CDet

```sh
git -C CDet remote set-url origin \
  "git@github.com:YOUR_USERNAME/CDet.git"

git -C CDet remote add upstream \
  git@github.com:btspaude/CDet.git
```

### SBS-offline

```sh
git -C git-repo/sbs_devel/SBS-offline remote set-url origin \
  "git@github.com:YOUR_USERNAME/SBS-offline.git"

git -C git-repo/sbs_devel/SBS-offline remote add upstream \
  git@github.com:brash99/SBS-offline.git
```

### SBS-replay

```sh
git -C git-repo/sbs_devel/SBS-replay remote set-url origin \
  "git@github.com:YOUR_USERNAME/SBS-replay.git"

git -C git-repo/sbs_devel/SBS-replay remote add upstream \
  git@github.com:brash99/SBS-replay.git
```

### jlab-HPC

```sh
git -C git-repo/sbs_devel/jlab-HPC remote set-url origin \
  "git@github.com:YOUR_USERNAME/jlab-HPC.git"

git -C git-repo/sbs_devel/jlab-HPC remote add upstream \
  git@github.com:brash99/jlab-HPC.git
```

### panguin

The committed `.gitmodules` entry points directly to
`JeffersonLab/panguin`. Override it locally and configure your fork:

```sh
git config \
  submodule.git-repo/panguin.url \
  "git@github.com:YOUR_USERNAME/panguin.git"

git -C git-repo/panguin remote set-url origin \
  "git@github.com:YOUR_USERNAME/panguin.git"

git -C git-repo/panguin remote add upstream \
  https://github.com/JeffersonLab/panguin.git
```

If an `upstream` remote already exists, use `remote set-url` instead of
`remote add`:

```sh
git -C PATH_TO_SUBMODULE remote set-url upstream UPSTREAM_URL
```

## 6. Verify every remote

```sh
git remote -v

git submodule foreach --recursive '
  echo
  echo "Repository: $displaypath"
  git remote -v
'
```

For each forked repository:

- `origin` should point to `YOUR_USERNAME`.
- `upstream` should point to the source repository.

Verify the checked-out submodule commits:

```sh
git submodule status --recursive
```

## 7. Configure the analysis environment

The setup scripts expect `JLAB_INSTALL_DIR` to be the parent repository.

For `zsh` or `bash`:

```sh
export JLAB_INSTALL_DIR="$HOME/CDet_replay"
source "$JLAB_INSTALL_DIR/setup_cdet_pd.sh"
```

For `tcsh`:

```tcsh
setenv JLAB_INSTALL_DIR "$HOME/CDet_replay"
source "$JLAB_INSTALL_DIR/setup_cdet_pd.csh"
```

## 8. Add navigation aliases

### zsh or bash

Add this block to `~/.zshrc` or `~/.bashrc`:

```sh
export JLAB_INSTALL_DIR="$HOME/CDet_replay"

alias gocdet='cd "$JLAB_INSTALL_DIR" && source "$JLAB_INSTALL_DIR/setup_cdet_pd.sh"'
alias gocdetcode='cd "$JLAB_INSTALL_DIR/git-repo/sbs_devel/SBS-offline" && pwd'
alias gocdethpc='cd "$JLAB_INSTALL_DIR/git-repo/sbs_devel/jlab-HPC" && pwd'
alias gocdetdb='cd "$JLAB_INSTALL_DIR/git-repo/sbs_devel/SBS-replay/DB" && pwd'
alias gocdetreplay='cd "$JLAB_INSTALL_DIR/git-repo/sbs_devel/SBS-replay/replay" && pwd'
alias gocdetrun='cd "$JLAB_INSTALL_DIR/run_replay_here" && pwd'
alias gocdetscripts='cd "$JLAB_INSTALL_DIR/git-repo/sbs_devel/SBS-replay/scripts/cdet" && pwd'
```

Reload the configuration:

```sh
source ~/.zshrc
```

Use `source ~/.bashrc` instead when using Bash.

### tcsh

Add this block to `~/.tcshrc`:

```tcsh
setenv JLAB_INSTALL_DIR "$HOME/CDet_replay"

alias gocdet        'cd "$JLAB_INSTALL_DIR"; source "$JLAB_INSTALL_DIR/setup_cdet_pd.csh"'
alias gocdetcode    'cd "$JLAB_INSTALL_DIR/git-repo/sbs_devel/SBS-offline"; pwd'
alias gocdethpc     'cd "$JLAB_INSTALL_DIR/git-repo/sbs_devel/jlab-HPC"; pwd'
alias gocdetdb      'cd "$JLAB_INSTALL_DIR/git-repo/sbs_devel/SBS-replay/DB"; pwd'
alias gocdetreplay  'cd "$JLAB_INSTALL_DIR/git-repo/sbs_devel/SBS-replay/replay"; pwd'
alias gocdetrun     'cd "$JLAB_INSTALL_DIR/run_replay_here"; pwd'
alias gocdetscripts 'cd "$JLAB_INSTALL_DIR/git-repo/sbs_devel/SBS-replay/scripts/cdet"; pwd'
```

Reload the configuration:

```tcsh
source ~/.tcshrc
```

The aliases perform these actions:

| Alias | Destination |
|---|---|
| `gocdet` | Parent repository and CDet environment setup |
| `gocdetcode` | SBS-offline |
| `gocdethpc` | jlab-HPC |
| `gocdetdb` | SBS-replay database |
| `gocdetreplay` | SBS-replay replay directory |
| `gocdetrun` | Local replay working directory |
| `gocdetscripts` | CDet analysis scripts |

## 9. Check repository status safely

`CDet_replay` is a parent repository containing several independent Git
worktrees. Checking only the parent status is insufficient.

Check the parent:

```sh
git status --short
```

Check every submodule:

```sh
git submodule foreach --recursive '
  echo
  echo "Repository: $displaypath"
  git status --short
'
```

## 10. Update from upstream

Update a submodule by fetching and merging its upstream branch inside that
submodule. For example:

```sh
cd "$JLAB_INSTALL_DIR/git-repo/sbs_devel/SBS-replay"

git fetch upstream
git switch main
git merge upstream/main
git push origin main
```

Confirm the actual default branch before using `main`:

```sh
git remote show upstream
```

After a submodule moves to a new commit, record that commit in the parent:

```sh
cd "$JLAB_INSTALL_DIR"

git status
git add git-repo/sbs_devel/SBS-replay
git commit -m "Update SBS-replay submodule"
git push origin main
```

Always commit and push work inside the submodule before committing its new
submodule pointer in `CDet_replay`.
