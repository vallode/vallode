---
title: "Dotfiles solution"
description: "How I store and restore my configuration files."
pubDate: 2022-01-22
updatedDate: 2022-01-22
---

I wanted to describe the way I keep my dotfiles sorted and use this as a way of remembering how I do it for future me's benefit.

I follow the "home directory bare git repository" method. It's quite simple and nifty. There are a few kinks in this method but it ticks all my boxes overall.

## Repository setup

First, we will initialise a new git repository in our user home directory:

```bash
git init --bare $HOME/.dotfiles
```

Here [git init](https://git-scm.com/docs/git-init) simply creates a `.git` directory and prepares a git repository. `--bare` tells git to create a barebone repository with no working tree. `$HOME/.dotfiles` is just the directory name for git, instead of `.git` which could pose to be confusing.

Some prefer to name their git repository `.config`, I use dotfiles because it's easier to remember the function.

For the repository setup to be complete, we need two more things:

```bash
alias dotfiles='/usr/bin/git --git-dir=$HOME/.dotfiles/ --work-tree=$HOME'
dotfiles config --local status.showUntrackedFiles no
```

The alias is simple: `/usr/bin/git` is where the git executable is, `--git-dir=$HOME/.dotfiles/` tells git to use our dotfiles git directory, `--work-tree=$HOME` associates the entire `$HOME` directory with your git repository. You can _also_ alias this with git itself:

```bash
git config --global alias.dotfiles '!git --git-dir=$HOME/.dotfiles/ --work-tree=$HOME'
```

This allows you to use `git dotfiles <any git command>` freely anywhere, too.

The `dotfiles config` command is just a quality of life improvement, we change the config of our dotfiles repository to not show any untracked files, otherwise we would have a _very_ messy git status.

That's it! You can go about adding all your dotfiles/configuration files to your git repository now:

```bash
dotfiles remote add origin REMOTE_URL
dotfiles add .zshrc
dotfiles commit -m "Add .zshrc"
dotfiles push
```

You might have to set your git email and user if you don't have it set up globally:

```bash
dotfiles config --local user.email YOUR@EMAIL.ORG
dotfiles config --local user.name YOUR_NAME
```

## Cloning

Cloning your repository, or syncing it, on another computer is fairly simple too:

```bash
git clone --bare REPO_URL $HOME/.dotfiles
alias dotfiles='/usr/bin/git --git-dir=$HOME/.dotfiles/ --work-tree=$HOME'
dotfiles checkout
```

## Caveats

- Tab autocompletion is funky

  Autocompletion just doesn't work right, it's a bit frustrating but something I'm looking into.

## Other resources

Naturally, I did not come up with this method. You can find the "origin" on [this hackernews post](//web.archive.org/web/20211111004149/https://news.ycombinator.com/item?id=11070797) and more in-depth details on [the arch wiki](//wiki.archlinux.org/title/Dotfiles).
