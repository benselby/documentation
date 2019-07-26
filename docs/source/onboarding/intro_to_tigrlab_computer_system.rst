Introduction
============

Getting Started
---------------

- [ ] **Get a lab account:** Talk an admin (Dawn) and get a lab
  account set up so you can use all of our workstations.
- [ ] **Request access to the kimel-lab mailing list:** You can either
  subscribe `here <http://info2.camh.net/mailman/listinfo/kimel-lab>` __ or
  send an email to kimel-lab-join@info2.camh.net to be automatically
  enrolled. We use this mailing list to send important lab notices and
  to share interesting papers, upcoming deadlines, etc. Everyone in the
  lab should have access.
- [ ] **Create a `github account <http://github.com>`__ and send your
  username to tigrlab@camh.ca.** This account will be needed to gain
  access to our QC dashboard (if you end up doing quality control for
  any of our managed studies) and is used to add tickets to our `issue
  tracker <#issue-tracker>`__.
- [ ] **Get access to our Slack channel**: Talk to a lab member and ask
  for an invite! This one is optional, but we use our Slack to organize
  lab outings and just generally chat / ask each other questions so
  you'll be a bit out of the loop without it.
- [ ] ** Get access to the lab calendar**: https://calendar.google.com/calendar/embed?src=4tu2r08f1706gg9t8rps8rta98@group.calendar.google.com&ctz=America/Toronto&pli=1
- [ ] **Check out our PDF of resources and tips**. We've compiled a
  bunch of up-to-date and useful info as a reference and left it at
  ``/projects/kimel_resources_and_tips.pdf``
- [ ] **Browse through the rest of this page and this wiki!** Not all
  pages on the wiki are relevant to everyone, or relevant right when
  you start, but there is a lot scattered throughout its pages that
  will help acclimate you to the lab. Once you have your Github
  account, you may add/edit pages (please do!). Also please let us know
  if you see room for improvement in our wiki as you read through it.

Issue Tracker
-------------

If you notice something wrong with our systems (workstations, queue,
file system, software etc.) or our archive or external data you can open
a ticket here: http://github.com/TIGRLab/admin/issues We get emails when
a new issue is opened, and will respond as quickly as we can.

A couple of tips to help you write better issues:
- Try to use a descriptive title
- Provide as much detail as possible in the body.
- If you're having a problem with the archive or external data, try to include
relevant subject IDs and file system paths along with your explanation
of the problem so we can locate the data easily.
- If it's a software error try to include the error message or a screenshot
  of the issue.
- If you're asking for new software to be installed please provide a link
to the software
- If it's a problem with a specific workstation remember
to give the name of workstation (The name should always be on the
sticker on the top of the tower and after the '@' on your command line
prompt)
- If your queue jobs are failing try to include any relevant
logs you have, point us to the script you submitted, and show the
command you used to submit your jobs. If your jobs are failing only on
specific machines please provide the names of those machines.
- Assign your issue to someone (preferably a relevant person but don't worry too
much, we'll reassign it if there's someone better equipped to handle
your problem):
  - If your issue is **technical** (systems/queue/software)
then you should assign Dawn (DESm1th) and/or Kevin (kimjetwav)
  - If it's **data / study** related you should assign one (or more) of: Erin
(edickie), Jerry (jerdra), Michael (josephmje), or Gabi (gabiherman).

File System
-----------

Useful folders on the File System:
+ ``/projects``: Shared folder that
is backed up nightly and keeps a month’s back up log. Put your important
code/data here. + ``/scratch``: Shared folder with no back up. This is a
good place for working files that can easily be regenerated. Once you
know your work is good enough to backup, move it to ``/projects`` +
``/archive/data-2.0``: Contains data from past and current studies that
the lab manages. + See `Data
Organization <https://github.com/TIGRLab/documentation/wiki/Data-Organization>`__
and `Data
Naming <https://github.com/TIGRLab/documentation/wiki/Data-Naming>`__
for information on how the data is organized (in great detail!). +
``/archive/publications``: Contains a copy of the fully-analyzed data
used in a publication. + ``/external``: Contains data from
publicly-available datasets. + ``/home/${USERNAME}``: Your home folder,
which is **unique on each machine**. See below for more information.

Home Folder
^^^^^^^^^^^

While your ``/projects`` or ``/scratch`` are shared between machines the
``/home`` folders on each machine are local to that machine. This means
**anything in your home folder on machine X will not be available on
machine Y**. Your code, data, writing, and figures should therefore be
kept in ``/projects`` or ``/scratch``. This is particularly important
for the queue because when your job is sent to another node of the
cluster it won't be able to see any of the files **you** can see in your
home folder!

**.bashrc**

The bashrc file is a hidden file in your home directory (note the '.'
that prefixes the name: that's how Linux indicates a file is hidden. You
can view hidden files using ``ls -a``). Linux reads this file whenever
you open up a new terminal so you can add commands to it to set up
custom module loads, shortcuts, and environment variables. You can read
more about bashrc and some customization tips
`here <https://www.lifewire.com/bashrc-file-4101947>`__. However,
because our home directories are local to each machine you now have a
problem: If you switch machines all your bashrc configuration isn't
available on the new workstation! The best way to solve this is to do
the following:

1. Move your .bashrc to your projects folder
2. Make a new .bashrc in your home folder that contains just one line:

   .. code:: bash

       source /projects/YOUR-USERNAME-HERE/PATH-TO-YOUR-.BASHRC-HERE

   Every time you move to a new workstation you can get set up by
   modifying your bashrc on that machine to source your projects copy.

Tutorial for Unix and Linux operating system basics
---------------------------------------------------

www.ee.surrey.ac.uk/Teaching/Unix/

Useful commands for the terminal
--------------------------------

1. ``module available``: Check what modules are available.
2. ``module load`` or ``module unload``: Load or unload the module that
   needs to be used.
3. ``module list``: Show the modules that are currently loaded.
4. ``which``: Shows the full path of shell commands. E.g. ``which ls``
5. ``<command> --help``: Provides a description of the usage and
   function of a command. E.g. ``ls --help``

Printing
--------

All printing should be done on ``km-bizhub-368``, outside of room 101.
Printing is now automatic, and does not require credentials to be
entered at the computer or printer.
