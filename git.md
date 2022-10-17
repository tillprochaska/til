# Git

## Rebase des ersten Commits eines Repositorys

Git unterstützt seit einiger Zeit die `--root`-Option, mithilfe derer ein interaktiver Rebase auch für den ersten Commit durchgeführt werden kann:

```
git rebase -i --root
```

## Using the built-in Git web UI on macOS

Git ships with a [built-in web UI](https://git-scm.com/book/en/v2/Git-on-the-Server-GitWeb) that can be used to browse a Git repository’s history, tags, commits etc. in a similar way to GitHub and GitLab.

In order to use it on macOS, install Lighttpd:

```shell
brew install lighttpd
```

Then navigate to a Git repository and run:

```shel
git instaweb
```
