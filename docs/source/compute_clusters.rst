Local Cluster
-------------

Our cluster (using Grid Engine) is made up from our workstations. About
15 nodes, ~160 cores.

Submitting Jobs
---------------

-  ``qsub`` is your friend. Use it like so:

   .. code:: sh

      qsub -V -cwd <command>     # where <command> is your command line to run

   There are many many options for ``qsub``, check the ``man`` page for
   details.

-  A more friendly way of submitting jobs is available from the
   ``SGE-extras`` module:

   .. code:: sh

       sge_batch <command>

``sge_batch`` is a tool used to submit a bunch of jobs through ``qsub``,
the submission system for Sun Grid Engine’s (SGE) scheduler system. It
lives in the ``SGE-extras`` module.

::

   module load SGE-extras/1.0

In the simplest case (each job uses less than 4 GB of ram and you don’t
care about logs):

::

   sge_batch some_command -l option1 -o option2

Logging
~~~~~~~

``sge_batch`` can combine ``STDOUT`` and ``STDERR`` logs with the ``-k``
flag. Specify the output with the ``-o`` flag.

::

       sge_batch -k -o somelog.log some_command -l option1 -o option2

Requesting Resources
~~~~~~~~~~~~~~~~~~~~

In order to prevent your *super important science* from not freezing all
of the other computers, we’ve set a 4 GB / job limit on the queue. This
means that unless you specifically ask for more than 4 GB of RAM, your
job will be `KILLed`_ automatically.

You can overcome this with the ``-l`` flag and some options (``-l`` is
used to pass a large number of options to ``qsub``\ … `see -l in the
qsub MAN page`_). The options to set are ``mem_free``, ``h_vmem``, and
``virtual_free``. As an example, let’s say we want 12 gigabytes of RAM
for a particular job. The correct command would be:

::

   sge_batch -l mem_free=20,h_vmem=20G,virtual_free=20G some_command -l option1 -o option2

Managing your jobs
------------------

-  See the status of your jobs: ``qstat``
-  See detailed info about your job (including error messages):
   ``qstat -f <jobid>``
-  See an updating display of job status: ``watch qstat``
-  Delete a job: ``qdel 193295`` to delete the job with ID 193295 (or
   ``qdel '*'`` to delete all jobs)
-  clear a job’s error state (so that it is re-run): ``qmod -c <jobid>``
-  Look at the following log file on ``srv-sge`` for error messages:
   ``/var/spool/gridengine/qmaster/messages``.

Error Codes
~~~~~~~~~~~

-  ``qw``: pending (user hold)
-  ``hqw``: pending (system/user+system hold)
-  ``hRwq``: pending, user/system hold, re-queue
-  ``r``: running
-  ``t``: transferring
-  ``Rr``: running, re-submit
-  ``Rt``: transferring, re-submit
-  ``s, st``: job suspended
-  ``S, tS``: queue suspended
-  ``T, tT``: queue suspended by alarm
-  ``Rs, Rts, RS, RtS, RT, RtT``: all suspended with re-submit
-  ``Eqw, Ehqw, EhRqw``: all pending states with error
-  ``dr, dt, dRr, dRt, ds, dS, dT, dRs, dRS, dRT``: all running and
   suspended states with deletion

Disabling/enabling computers from the queue
-------------------------------------------

If you need your workstation in order to do local, intensive processing
you can remove it from the queue

.. _KILLed: https://www.youtube.com/watch?v=68DBxTm4ULs
.. _see -l in the qsub MAN page: http://gridscheduler.sourceforge.net/htmlman/htmlman1/qsub.html
