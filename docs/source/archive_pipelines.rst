Who this is for
---------------

-  Lab members who help curate the ``archive`` data
-  Lab members interested in knowing more about what scripts are run on
   the data in ``/archive``
-  Lab members who help manage datman (If you don’t know what datman is,
   you don’t need this page yet!)

Intro
-----

This page describes the scripts that manage all of the data stored in
``/archive``. These scripts run every night and are the first place to
look if new data management steps need to be added, old steps need to be
removed / replaced, or something goes wrong. The scripts are stored in
``/archive/code/config``. There are usually two scripts per study:
``<study>_management.sh`` and ``<study>_analysis.sh``.

**The management scripts** run on our local cluster and handle the main
data management steps for each study.

**The analysis scripts** run on the SCC and are meant to produce the
outputs found in ``/archive/data/<study>/pipelines``. Currently, for
historical reasons, some scripts that produce pipelines folder outputs
are mixed into the management script. Eventually these will (should) be
moved to analysis when the scripts are updated and ready to run on the
SCC.

In ``/archive/code/bin`` there are two scripts, ``run.sh`` and
``runall_pipelines_scc.sh``, that are run nightly as a cron job on
tigrsrv. These scripts launch the management and analysis scripts,
respectively, for all studies. Everything is run as user clevis.

Manually running stages
-----------------------

If a subject needs to be sped through the pipelines a management or
analysis script can be manually run step by step. Ideally, you should
switch to clevis if you do this to prevent permissions problems. Certain
stages of the pipeline are optional or can be run out of order but some
stages require certain user intervention (e.g. signing off on QC, fixing
session name errors) or prerequisite stages, so consult the relevant
sections under `Management`_ and `Analysis`_ for any stages you wish to
run.

Module prerequisites
~~~~~~~~~~~~~~~~~~~~

Some modules must be loaded for any datman scripts to be run. Analysis
stages may require one of two datman versions on the SCC due to the fact
that some of our pipelines scripts are legacy code still in the process
of being updated. Individual steps in the pipeline may also have their
own additional module dependencies or variables that need to be set and
this should be copy pasted from the management/analysis script itself.

Management
^^^^^^^^^^

Management pipeline scripts require the version of datman stored in
``/archive/code/datman``, which can be loaded with packages.module as
shown below. The packages module also sets up access to the dashboard
for any scripts that require it as well as several other dependencies.

::

   module load /archive/code/config/packages.module
   source activate

Analysis
^^^^^^^^

To load any Kimel specific modules on the SCC you must first run:

::

   module load /KIMEL/quarantine/modules/quarantine

**Up to date pipelines** scripts (currently only \`dm_proc_fr

.. _Management: #management-pipeline
.. _Analysis: #analysis-pipeline
