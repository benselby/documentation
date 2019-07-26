Overview
========

Git is a tool to help track changes in your work. It was originally
created for code but can be used with plain old text and other files as
well (as long as they're not too large, i.e. trying to commit nii files
is a bad idea). The basic idea is that as you change your work you
periodically 'commit' changes you want to keep. You can then 'checkout'
these previous commits if you later decide you want to undo some changes
and you can refer back to the messages you wrote when you committed them
to figure out why you made your changes. Git also makes it easier to
share your work with other people because it makes it easy to copy
('clone') someones work and later merge it back with the original if you
need to. Because of this, git has become the standard for programmers
and is often used by people working collaboratively on written
documents.

Basic work flow
---------------

To start using git open a terminal (ctrl + alt + t is a shortcut on
Ubuntu), change to the directory that you want git to manage and type
``git init``.

.. code:: bash

    $ cd /projects/dawn/git_example
    $ git init
    Initialized empty Git repository in /projects/dawn/git_example/.git

What this does is create a hidden folder ('.git') inside of the
directory that will hold all of git's info about your project. If you
now type ``git status`` git will give you a report of the current state
of your project ('repository' in git nomenclature). If your folder is
empty it will give something like:

.. code:: bash

    On branch master

    Initial commit

    nothing to commit (create/copy files and use "git add" to track)

If you add a file to this directory (I added one called 'example\_1')
and type ``git status`` again you'll now see that git reports it as an
untracked file:

.. code:: bash

    On branch master

    Initial commit

    Untracked files:
      (use "git add <file>..." to include in what will be committed)

        example_1

    nothing added to commit but untracked files present (use "git add" to track)

When a file is untracked git will not manage it in any way. If you make
changes you later regret git can't yet help you revert them. To get git
to start managing a file there are two steps.

1. ``git add <file>``. Use 'git add' to add as many files as you want to
   the 'staging area'. Everything that has been 'staged' with git add
   will be included in your next commit (But is **NOT** yet saved by
   git!). After you're done adding files you can type ``git status``
   again to see that git has listed them in the 'Changes to be
   committed:' section. \`\`\`bash On branch master

Initial commit

Changes to be committed: (use "git rm --cached ..." to unstage)

::

    new file:   example_1

\`\`\`

2. ``git commit``. When you're done adding files you have to tell git to
   commit them. Git will prompt you to enter a message describing your
   changes. All of the changes that you added to the 'staging area' then
   become part of one commit that you can go back to at a later time if
   you need to. What this means is that you should try to avoid
   committing unrelated things at the same time or you wont be able to
   revert your changes without rolling back everything. If your commit
   was successful you should see a message like below with a short
   summary of your commit message in place of 'My first commit'.

   .. code:: bash

       [master (root-commit) d2eb1ff] My first commit
        1 file changed, 1 insertion(+)
        create mode 100644 example_1

Once you've made at least one commit you can view the history of your
changes with ``git log``.

::

    commit d2eb1ff7fc593a9533526002140eba6ebe9de214
    Author: Dawn E. Smith <Dawn.Smith@camh.ca>
    Date:   Tue Jun 25 15:07:07 2019 -0400

        My first commit

The first line of this output is always a statement like "commit
d2eb1ff7fc593a9533526002140eba6ebe9de214". That long code is a hash and
it uniquely identifies each commit. If you have multiple files that
you've committed and just want to see the commit history of one specific
file you can use ``git log --follow <filename>``

You can think of each commit like taking a snapshot of your files at the
moment that you added them to the staging area. This means that if you
make changes to a git managed file, don't commit the changes, and then
make additional changes to the same file that you later regret, git wont
be able to restore to your intermediate changes. So commit often, make
each commit small / self contained (ask yourself 'if I regret something
later do I want to have to undo *all* off these changes?'), and make
your commit messages meaningful enough that you can figure out
relatively quickly which commit contains which changes.

Reverting to an old commit
--------------------------

The main benefit of git is the ability to jump back in time to a
specific version of your work. To do this you first need to figure out
what commit you want to jump back to. You can use ``git log`` to choose
one. You can also use ``git diff <commit hash 1> <commit hash 2>`` to
get a complete listing of all differences between two commits. Note that
this will list every change that occurred between those two 'snapshots'
of your work, so if you have say two dozen commits in between them
you'll see the combined list of differences from all two dozen commits.

Once you've chosen a commit to jump back to copy its hash and use
``git checkout -b <new branch name> <commit hash>``. The
``-b <new branch name>`` part of the command will create a new copy of
your project to work from (referred to as a branch) and start this copy
at the commit with the hash you gave. This means you can make and commit
new changes but you've still retained your original version from before
you jumped back in time in case you change your mind. You can see all
the branches your project has with ``git branch -v`` and you can switch
to any of them with ``git checkout <branch name>``. The branch 'master'
is a special branch that is meant to represent the canonical copy of
your project and is a branch that is created for you automatically when
you first used ``git init``. `This
link <https://it.atlassian.com/git/tutorials/using-branches>`__ has some
more explanation about branches and how to visualize them.

Adding a repo to github
-----------------------

At some point you'll probably want to start using github or some other
source code storage site. Github will hold a web based copy of your git
repository that other people can copy (clone in git nomenclature) from a
link they provide. It also allows you to easily back up your repository
somewhere else in case something happens to your local folder.

First you need to create an empty repository with the same name on
github. Github's documentation `explains how
here <https://help.github.com/en/articles/creating-a-new-repository>`__.
Once that's done github should put you on a page with a 'quick setup'
link at the top and some instructions for how to get started. You'll
likely want to use the 'push an existing repository from the command
line' section.

If so, you'll be using these commands:

.. code:: bash

    git remote add origin <your github url here>
    git push -u origin master

The first command adds a 'remote' with the nickname 'origin' that is
stored at the github url you used. A remote is what it sounds like: a
remote copy of the same repository. You can push (move your local
changes to the remote copy) or pull (move remote changes to your local
copy). The second command in the example uses push to move all of your
commits from your local copy to the destination copy on github to bring
it in to sync.
