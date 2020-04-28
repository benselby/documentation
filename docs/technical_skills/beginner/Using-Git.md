# Using Git

## Overview

Git is a tool to help track changes in your work. It was originally created for code but can be used with plain old text and other files as well (as long as they're not too large, i.e. trying to commit nii files is a bad idea). The basic idea is that as you change your work you periodically 'commit' changes you want to keep. You can then 'checkout' these previous commits if you later decide you want to undo some changes and you can refer back to the messages you wrote when you committed them to figure out why you made your changes. Git also makes it easier to share your work with other people because it makes it easy to copy ('clone') someones work and later merge it back with the original if you need to. Because of this, git has become the standard for programmers and is often used by people working collaboratively on written documents.

## Contents

- [Using Git](#using-git)
  - [Overview](#overview)
  - [Contents](#contents)
  - [Basic Work Flow](#basic-work-flow)
  - [Having Multiple Working Copies](#having-multiple-working-copies)
  - [Switching to an Older Version](#switching-to-an-older-version)
  - [Working with GitHub](#working-with-github)
  - [Setting your Email and Editor](#setting-your-email-and-editor)
  - [Cheatsheet](#cheatsheet)

## Basic Work Flow

To start using git open a terminal (ctrl + alt + t is a shortcut on Ubuntu), change to the directory that you want git to manage and type `git init`.

```bash
$ cd /projects/dawn/git_example
$ git init
Initialized empty Git repository in /projects/dawn/git_example/.git
```

What this does is create a hidden folder ('.git') inside of the directory that will hold all of git's info about your project. If you now type `git status` git will give you a report of the current state of your project ('repository' in git nomenclature). If your folder is empty it will give something like:

```bash
On branch master

Initial commit

nothing to commit (create/copy files and use "git add" to track)
```

If you add a file to this directory (I added one called 'example_1') and type `git status` again you'll now see that git reports it as an untracked file:

```bash
On branch master

Initial commit

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	example_1

nothing added to commit but untracked files present (use "git add" to track)
```

When a file is untracked git will not manage it in any way. If you make changes you later regret git can't yet help you revert them. To get git to start managing a file there are two steps.

1. `git add <file>`. Use 'git add' to add as many files as you want to the 'staging area'. Everything that has been 'staged' with git add will be included in your next commit (But is **NOT** yet saved by git!). After you're done adding files you can type `git status` again to see that git has listed them in the 'Changes to be committed:' section.

```bash
On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

	new file:   example_1
```

2. `git commit`. When you're done adding files you have to tell git to commit them. Git will prompt you to enter a message describing your changes. All of the changes that you added to the 'staging area' then become part of one commit that you can go back to at a later time if you need to. If your commit was successful you should see a message like below with a short summary of your commit message in place of 'My first commit'.

```bash
[master (root-commit) d2eb1ff] My first commit
 1 file changed, 1 insertion(+)
 create mode 100644 example_1
```

Once you've made at least one commit you can view the history of your changes with `git log`.

```
commit d2eb1ff7fc593a9533526002140eba6ebe9de214
Author: Dawn E. Smith <Dawn.Smith@camh.ca>
Date:   Tue Jun 25 15:07:07 2019 -0400

    My first commit
```

The first line of this output is always a statement like "commit d2eb1ff7fc593a9533526002140eba6ebe9de214". That long code is a hash and it uniquely identifies each commit. If you have multiple files that you've committed and just want to see the commit history of one specific file you can use `git log --follow <filename>`

You can think of each commit like taking a snapshot of your files at the moment that you added them to the staging area. This means that if you make changes to a git managed file, don't commit the changes, and then make additional changes to the same file that you later regret, git wont be able to restore to your intermediate changes. So commit often, make each commit small / self contained (ask yourself 'if I regret something later do I want to have to undo _all_ of these changes?'), and make your commit messages meaningful enough that you can figure out relatively quickly which commit contains which changes.

## Having Multiple Working Copies

One of the benefits of git is that it allows you to easily make another copy of your work at a specific point in time and make changes without affecting the original copy. However, it will appear as though you only have one copy at a time on the file system. Each copy is a called a 'branch'. You can view all the branches you have with `git branch -v`.

'master' is a special branch that represents the main/canonical copy of your project. It's created automatically for you when you run `git init` and unless you manually create more branches for yourself it will be the only one you have. To create a new branch you can use `git checkout -b <branch name>`. Once the command has finished running you can run `git branch -v` again to see that it has been added to your list of branches and that you're now using the new one.  [This link](https://it.atlassian.com/git/tutorials/using-branches) has some more explanation about branches and how to visualize them.

As an example, for my datman repo I get the following output when I look at my branches:

```bash
dawn@golgi:/projects/dawn/modules/datman$ git branch -v
  cleanup      f89bc1c Removed comment
  master       6a6f6b4 Fixed fact that session is not require to be a number
  page_regen   526d00a Use updated get_subject method
* pha_renaming b1ea103 Updated doc strings and started work on pha_rename
  py3          f89bc1c Removed comment
```

The left column is the name of each branch. The '*' to the left of one of the branch names will indicate which branch you're currently working on. In my case I was working on the 'pha_renaming' branch. The second column is the hash of the last commit on that branch but truncated to only show the first few characters. The third column is a truncated version of the commit message from the last commit made.

If you look at my output you'll notice that 'cleanup' and 'py3' both have the same truncated hash and same commit message. This is because they haven't been changed since I created those branches. They represent two identical copies of datman under different names. This illustrates an important point: all of the history of your commits up until the point where the branch was created stays with your branch.

If you've made some changes and decide you want your branch to become your master copy you can 'merge' two branches together. To do this check out the version you want to keep as your 'main' copy (this will usually be your master branch) with `git checkout <branch name>`. And then attempt the merge with `git merge <other branch>`. Git will try to combine all changes you've made on either branch, and the history of all your commits from each, automatically. If different changes were made to the same line of text though you'll have to manually indicate which version of the differences you want to keep.

## Switching to an Older Version

The biggest benefit of git is the ability to jump back in time to a specific version of your work. To do this you first need to figure out what commit you want to jump back to. You can use `git log` to choose one. You can also use `git diff <commit hash 1> <commit hash 2>` to get a complete listing of all differences between two commits. Note that this will list every change that occurred between those two 'snapshots' of your work, so if you have say two dozen commits in between them you'll see the combined list of differences from all two dozen commits.

Once you've chosen a commit to jump back to copy its hash and use `git checkout -b <new branch name> <commit hash>`. The `-b <new branch name>` part of the command will create a new copy of your project to work from (referred to as a branch) and start this copy at the commit with the hash you gave. This means you can make and commit new changes but you've still retained your original version from before you jumped back in time in case you change your mind.

## Working with GitHub

At some point you'll probably want to start using github or some other source code storage site. Github will hold a web based copy of your git repository that other people can copy (clone in git nomenclature) from a link GitHub provides. It also allows you to easily back up your repository somewhere else in case something happens to your local folder.

First you need to create an empty repository with the same name on github. Github's documentation [explains how here](https://help.github.com/en/articles/creating-a-new-repository). Once that's done github should put you on a page with a 'quick setup' link at the top and some instructions for how to get started. You'll likely want to use the 'push an existing repository from the command line' section.

If so, you'll be using these commands:

```bash
git remote add origin <your github url here>
git push -u origin master
```

The first command adds a 'remote' with the nickname 'origin' that is stored at the github url you used. A remote is what it sounds like: a remote copy of the same repository. You can push (move your local changes to the remote copy) or pull (move remote changes to your local copy). The second command in the example uses push to move all of your commits from your local copy to the destination copy on github to bring it in to sync.

## Setting your Email and Editor

Git has a number of config variables that you can toy with. You can see all your current settings with `git config -l`.
You may want to change the default editor that git uses (it uses vim by default). If so you can do this with `git config --global core.editor nano`, which will change it to the more user friendly nano.

You may also want to set your name and email to ensure you receive proper 'credit' for your commits if you're pushing your work to GitHub (your git config email has to match your email on GitHub or your contributions to various projects may not always update correctly). You can do this with:

```
git config --global user.name "FirstName LastName"
git config --global user.email your@email.com
```

## Cheatsheet

There are a number of really good command reference cheatsheets that exist. One of my favorites is Github's [which is found here](https://education.github.com/git-cheat-sheet-education.pdf)
