Who this is for
===============

-  Anyone doing quality control for the lab

Contents
========

| `Intro <https://github.com/TIGRLab/documentation/wiki/QC---dashboard/_edit#idea-intro>`__
| `Logging
  in <https://github.com/TIGRLab/documentation/wiki/QC---dashboard/_edit#--logging-in>`__
| `Navigating the
  Dashboard <https://github.com/TIGRLab/documentation/wiki/QC---dashboard#computer-navigating-the-dashboard>`__
| :white\_small\_square:`Overview <https://github.com/TIGRLab/documentation/wiki/QC---dashboard#one-overview->`__
| :white\_small\_square:`Session
  QC <https://github.com/TIGRLab/documentation/wiki/QC---dashboard#two-session-qc->`__
| :white\_small\_square:`Sessions <https://github.com/TIGRLab/documentation/wiki/QC---dashboard#three-sessions->`__
| :white\_small\_square:`Phantoms <https://github.com/TIGRLab/documentation/wiki/QC---dashboard#four-phantoms->`__
| :white\_small\_square:`Subjects <https://github.com/TIGRLab/documentation/wiki/QC---dashboard#five-subjects->`__
| `Posting a GitHub Issue, Blacklisting, and Signing
  Off <https://github.com/TIGRLab/documentation/wiki/QC---dashboard#posting-a-github-issue-blacklisting-and-signing-off>`__
| `Communication <https://github.com/TIGRLab/documentation/wiki/QC---dashboard#e-mail--communication>`__
| `Feedback <https://github.com/TIGRLab/documentation/wiki/QC---dashboard#--feedback>`__
| `Important
  links <https://github.com/TIGRLab/documentation/wiki/QC---dashboard#important-links>`__

--------------

:tada: Intro
------------

The QC Dashboard was developed in the Kimel Lab and released in December
2016 in an effort to better conduct MRI QC. It provides a unified
interface for examining individual participant data, tracking
longitudinal trends, and communicating issues with others.

--------------

:unlock: Logging in
-------------------

The Dashboard is found at http://srv-dashboard.camhres.ca/. At present,
the Dashboard is internal to CAMH, meaning that it can only be accessed
from within the TransformingLives network, or via the Citrix VPN
(MobaXTerm only).

The Dashboard authenticates users via GitHub, which means that, if you
do not have a GitHub account, you will need to `make
one <https://github.com>`__. Email us at
`tigrlab@camh.ca <tigrlab@camh.ca>`__ with your GitHub user name, and
we'll add you with the appropriate permissions. Once we've set you up,
go to the `Dashboard <http://srv-dashboard.camhres.ca/>`__, where you
should see a `login
screen <https://github.com/TIGRLab/documentation/blob/master/2.png?raw=true>`__
where you'll need to enter your GitHub user name and password, and, just
for the first time, you'll need to `authorize the
application <https://github.com/TIGRLab/documentation/blob/master/2.png>`__.
Once you do so, you should be in!

--------------

:computer: Navigating the Dashboard
-----------------------------------

On the Dashboard's landing page, you will see a list of the studies that
you have been granted access to.

| Each study has 5 tabs that contain different information for your
  review:
| :one: Overview :two: Session QC :three: Sessions :four: Phantoms
  :five: Subjects

:one: Overview |overview|
~~~~~~~~~~~~~~~~~~~~~~~~~

The ``Overview`` tab is just that; it provides some basic READ-ME-type
information about a given study, including full study name, and PI and
study RA contact information.

You should add any information here that you think is essential for
others conducting QC to see, or will be important for those conducting
analyses in the future to know.

--------------

:two: Session QC |sessionqc|
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``Session QC`` tab will indicate all of the scans that have yet to
be signed off on at a given point in time. Our system looks for new
scans once per day and adds to this list. When a scan is signed off on,
it will be removed from the list in real-time.

If a scan that you expect to see, on the basis of your knowledge of the
MRI schedule or a 'scan completed' email, doesn't appear on the list the
day *after* the scan was completed, something has gone wrong, likely
with the naming convention that our system relies on. Email your QC
contact or post an issue on our `GitHub issues
page <https://github.com/tigrlab/admin/issues>`__.

Clicking on one of the listed scans will bring you to an html QC
document. This document has 3 distinct components: (i) a header, which
lists all expected acquisition types, (ii) a link to Tech Notes, and
(iii) visualizations of all collected data. Reviewing the html QC
document is where the bulk of the QC work happens. We need to do three
main things: (i) evaluate scan completeness, (ii) evaluate scan quality,
and (iii) look out for any unexpected scan parameters (i.e., dicom
header differences).

:thought\_balloon: Evaluating Scan Completeness.
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

An important aspect of QC is to make sure that we have all our data -
and a lot can go wrong here. Basically, we need to track down any
missing data that really exists, and have an explanation for any data
that really is absent. First, review the header of the QC document. If
you see "Missing" in the 'Notes' column, cross-reference with the Tech
Notes (and RA notes, if applicable). If the Tech Notes indicate that
that acquisition was not collected, that explains why we don't have it -
you'll make a note of it when you sign off (discussed further below),
and that's that. If the Tech Notes indicate that the acquisition *was*
collected, something has gone wrong somewhere along the way, and we will
have to hunt the file down. Second, review the body of the QC document,
i.e., the visualizations. If you see a file name that is not proceeded
by associated images, it may be that something is wrong with the file
itself or our image generation. Usually, where the images should be will
be encased by a border or filled with blank space, but not always.

Please note that, at present, we do not have visualizations for FMAPS
and MRS scans, so it should be expected that those file names are not
proceeded by any images.

:thought\_balloon: Evaluating Scan Quality
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In addition to making sure our data is complete, we need to have an idea
of the data's quality. Poor quality might be the result of participant
behaviour (motion, metal), MRI Tech error (FOV positioning, header
differences), and/or scanner/computational problems (usually related to
head coil and reconstruction). We have several automatic processes that
help identify problematic data at later processing and analysis stages,
by some things are best - and most quickly - detected by eye.

When we evaluate scan quality, we're essentially taking a quick look
over the data to ensure that everything looks as it should, and
notifying the appropriate parties if it doesn't. More specifically, we
are looking out for (i) images that are not biologically plausible, (ii)
geometric-looking distortions, and (iii) obvious and unexpected
pathology. Common issues of this sort are nicely illustrated
`here <https://github.com/TIGRLab/documentation/files/530007/qual-qc-manual.pdf>`__.
If you're unsure if what you're seeing is a problem, post an issue on
our `GitHub issues page <https://github.com/tigrlab/admin/issues>`__

:thought\_balloon: Interpreting header differences.
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Header differences flag any unexpected difference between the parameters
of a given scan and an earlier, "gold standard" scan that is known to
represent correct parameters. Some differences aren't consequential.
However, some differences are integral, as diverging parameters of some
kinds mean that data cannot be compared across subjects. Gradually, we
are expanding our list of differences that can be safely ignored so that
only important header difference flags are raised. If your QC documents
indicate any header differences, please email your QC contact or post an
issue on our `GitHub issues
page <https://github.com/tigrlab/admin/issues>`__.

In some unusual cases, flagged header differences that we know to be
important *can* be ignored. For example, when a scan is repeated (due to
scanner issues or participant behaviour), some scan parameters may be
altered by the MRI Tech for want of time. Flags that can be ignored in
this circumstance will related to number of excitations (NEX) value, and
include ``NumberOfAverages`` and ``PercentSampling``.

--------------

:three: Sessions |sessions|
~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``Sessions`` tab displays a paginated list of sessions, which can be
filtered by using the search box in the top-right corner. Clicking on a
session name will bring you to that session's html QC page. All sessions
are displayed here, including both those that have and have not been
QC'd (a checkmark icon beside the participant ID indicates a session has
been signed off on, whereas the pencil and paper icon indicates it is
still outstanding). The ``Sessions`` tab contains both human subject
data and phantom data (if applicable).

--------------

:four: Phantoms |phantoms|
~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``Phantoms`` tab displays data from a given study's non-human
phantom data collection. Phantoms are not conducted in all studies;
thus, this tab might be blank in your study. If data exists, the
following information is available:

+-------+--------------+--------------+----------------+
| scan  | tag          | what it is   | appearance     |
| type  |              |              |                |
+=======+==============+==============+================+
| fMRI  | rdc          | Measure of   | Should be low  |
|       |              | local        |                |
|       |              | spatial      |                |
|       |              | correlation  |                |
|       |              | due to       |                |
|       |              | scanner      |                |
|       |              | noise        |                |
+-------+--------------+--------------+----------------+
| fMRI  | sfnr         | Signal-to-fl | Should be high |
|       |              | uctuation-no |                |
|       |              | ise          |                |
|       |              | ratio        |                |
+-------+--------------+--------------+----------------+
| DTI   | AVENyqratio  | Nyquist      | Should be high |
|       |              | signal-to-no |                |
|       |              | ise          |                |
|       |              | ratio        |                |
+-------+--------------+--------------+----------------+
| T1    | c1           | T1 contrast  | Should be      |
|       |              | measure      | constant over  |
|       |              |              | time           |
+-------+--------------+--------------+----------------+
| T1    | c2           | T1 contrast  | Should be      |
|       |              | measure      | constant over  |
|       |              |              | time           |
+-------+--------------+--------------+----------------+
| T1    | c3           | T1 contrast  | Should be      |
|       |              | measure      | constant over  |
|       |              |              | time           |
+-------+--------------+--------------+----------------+
| T1    | c4           | T1 contrast  | Should be      |
|       |              | measure      | constant over  |
|       |              |              | time           |
+-------+--------------+--------------+----------------+

--------------

:five: Subjects |subjects|
~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``Subjects`` tab provides a comparison of various MRI metrics across
all participants (i.e., over time). It's important to review this sort
of data as (i) it provides a quick, visual indication if one particular
session differs drastically from others (i.e., an outlier), (ii) if
there's some sort of gradual but important shift in data over time, and,
if applicable, (iii) if there's a bias between sites.

You will then need to select a scan type, subtype, and metric type, and
at least one site, if applicable. Once all required options have been
selected, a graph will appear, showing the values of that metric for all
human subjects in the given study (if no graph appears, this means that
there is no data for the options you selected). Scrolling the mouse
wheel on the graph will allow you to expand and contract the x-axis and
make out individual points more clearly. You can also hover over the
site name in the legend (at the bottom of the graph) to highlight data
for only that site. Importantly, by clicking on a data point of interest
in the graph, you can navigate to that session's QC page.

The ``Subjects`` tab presents the following information about human
sessions:

+-------+--------------+----------------+--------------+
| scan  | tag          | what it is     | appearance   |
| type  |              |                |              |
+=======+==============+================+==============+
| fMRI  | ScanLength   | Number of TRs  | Should be    |
|       |              | (repetition    | constant     |
|       |              | time)          |              |
+-------+--------------+----------------+--------------+
| fMRI  | mean\_fd     | Average        | Should be    |
|       |              | framewise      | low          |
|       |              | displacement,  |              |
|       |              | i.e., a        |              |
|       |              | measure of     |              |
|       |              | head motion    |              |
+-------+--------------+----------------+--------------+
| fMRI  | mean\_sfnr   | Signal-to-fluc | Should be    |
|       |              | tuation-noise  | high         |
|       |              | ratio          |              |
+-------+--------------+----------------+--------------+
| DTI   | #ndirs       | Number of      | Should be    |
|       |              | encoding       | constant     |
|       |              | directions     |              |
+-------+--------------+----------------+--------------+
| DTI   | meanRELrms\* | Average        | Should be    |
|       |              | relative root  | low          |
|       |              | mean square of |              |
|       |              | displacement,  |              |
|       |              | i.e., a        |              |
|       |              | measure of     |              |
|       |              | head motion    |              |
+-------+--------------+----------------+--------------+
| DTI   | tsnr\_bX     | Temporal       | Should be    |
|       |              | signal-to-nois | high         |
|       |              | e              |              |
|       |              | ratio          |              |
+-------+--------------+----------------+--------------+

\* Please note that if a DTI sequence has a single B0, meanRELrms will
be meaningless, and should be ignored.

You will also notice a "Remove Outliers" button below the graph. By
pressing this button, the graph will be replotted with the selected
settings, except that all points which have a value greater than 2
standard deviations from the mean value will be excluded. The main use
of this button is for situations where a small number of outliers are
skewing the y-axis, making it impossible to discern between other values
visually.

--------------

Posting a GitHub Issue, Blacklisting, and Signing Off
-----------------------------------------------------

:information\_desk\_person: Posting a GitHub Issue
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you encounter something during QC that you don't know how to resolve
(e.g., an acquisition scan hasn't rendered, the TechNotes are missing,
the QC page displays important-seeming header differences, you have
found an artifact), you should post a GitHub issue. Click the
``Create GitHub Issue`` button in the top-right corner of the session
overview page, and a pop-up will appear; make sure you leave the session
name in the textbox (but you may add further details, in brief, in the
issue title before pressing OK). Another pop-up will appear, allowing
you to explain the issue with this session in more detail. If
successful, an "Issue '[session name]' created!" message will appear.
Returning to this session page in the future, the
``Create GitHub Issue`` button will be replaced with
``Go to open GitHub Issue``. Kimel lab staff monitor the issue-tracker
constantly. All future discussion about this session should take place
on GitHub, and the issue should be closed by you or the Staff member you
are communicating with when it is resolved.

*Note:* The GitHub issue tracker is a public forum; it can be viewed by
anyone, even those outside the lab. Accordingly, **it is essential to
exclude personal health information (PHI) and any identifying
information from all discussions**, and maintain confidentiality and
professionalism in your issue descriptions and comments.

:no\_good: Blacklisting data
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Sometimes, the QC process determines that some data should be discarded,
or "blacklisted". The first, most common example of when this is the
case is if the Tech Notes indicate that a particular scan was
re-acquired (usually due to scanner malfunction or participant
behaviour). If the second acquisition was successful, the Tech Notes
might recommend that the scan's first instance should be "discarded".
You'll see another indication of this as "Repeat" will be noted beside
the scan's second instance in the 'Notes' column of the QC document
header. The second situation in which you might want to delete data is
if an acquisition's quality is so poor that it's unusable without
question, even if the particular scan has not been re-acquired. You can
blacklist by clicking on the pencil icon beside the series you would
like to blacklist. You need to enter a reason for blacklisting.

On some rare occasions, it might be the case that a given participant's
entire scan should be discarded - for example, the scanner might have
been malfunctioning and *all* data might be useless, or the participant
might rescind consent, and request that all their data be deleted. If
you are blacklisting more than a couple of acquisitions, please post a
general GitHub issue
`here <https://github.com/TIGRLab/documentation/admin>`__ or email
`tigrlab@camh.ca <tigrlab@camh.ca>`__, as we may need to determine if
other scans need to be examined more closely than usual (in the first
case) or delete the data from elsewhere (in the second).

*Note:* If you are familiar with QC in the terminal (our prior system)
as opposed to the Dashboard, you will recall that we used to both
blacklist and delete data; that is no longer the case. The blacklist is
now operating such that all sessions indicated on it are automatically
removed and prevented from re-appearing in our file system. So, for all
intents and purposes, blacklisting data via the Dashboard is equivalent
to both blacklisting and deleting on the old system.

:pen: Signing off
~~~~~~~~~~~~~~~~~

Once you are certain that (i) the participant's scan in our system is as
complete as it will ever be and (ii) you have clarified/resolved any
issues, you can sign off on the scan. Do so by clicking the pencil icon
in the ``Session QC`` tab. Do not sign off on a scan until all Issues
have been resolved, as the act of signing off triggers various
processing pipelines.

--------------

:e-mail: Communication
----------------------

It's essential to keep in mind that several of the issues you encounter
while QCing may be of interest to others. For instance, if you notice
that many participants exhibit extensive motion during an experimental
task, you should inform the study RA that they might better emphasize
the importance of staying still. If you notice MRI Tech or
scanner-caused artifacts, you need to inform us and the MRI Centre, so
that these artifacts are scrutinized elsewhere, and corrected. If you
find that a protocol is often not completed for lack of time, you should
ensure that the PI knows the frequency with which final series are being
dropped. Or, if a particular participant's scan is virtually unusable
due to poor quality (especially if the poor quality is *not* a result of
participant behaviour), the PI might want to be notified, so that the
possibility of rescanning the participant can be considered. These are
but a few examples. The important point is that you communicate any
findings that bear on others reliably and quickly.

--------------

:wrench: Feedback
-----------------

Please, please, please give us some, about anything that you like, don't
like, aren't sure how to do, or want to see. You can file a GitHub issue
directly on the lab's page
`here <https://github.com/TIGRLab/documentation/admin>`__ or email
`tigrlab@camh.ca <tigrlab@camh.ca>`__.

--------------

Important links
---------------

| `QC
  manual <https://github.com/TIGRLab/documentation/files/530007/qual-qc-manual.pdf>`__
| `TIGRlab QC staff
  contacts <https://docs.google.com/spreadsheets/d/1MTuU7Y7hqAHbiHMP2NsnrW1wZ9UZrmz4AQpJCQ1-_BQ/edit?usp=sharing>`__
| `TIGRlab GitHub 'issues'
  page <https://github.com/tigrlab/admin/issues>`__
| `TIGRlab 'scan completed' survey <http://tinyurl.com/scancompleted>`__
| `TIGRlab XNAT database <https://xnat.imaging-genetics.camh.ca>`__
| `CAMH REDCap account request
  form <https://edc.camhx.ca/redcap/surveys/?s=W8KRWYEJMX>`__

.. |overview| image:: https://cloud.githubusercontent.com/assets/13733654/20948416/11d0c1a8-bbe2-11e6-9a35-440d689e55a2.png
.. |sessionqc| image:: https://cloud.githubusercontent.com/assets/13733654/20948434/23fdc088-bbe2-11e6-9272-a0e0bad0004b.png
.. |sessions| image:: https://cloud.githubusercontent.com/assets/13733654/20948439/2afdc838-bbe2-11e6-9830-eca6890fcc22.png
.. |phantoms| image:: https://cloud.githubusercontent.com/assets/13733654/20948445/32bb9ac8-bbe2-11e6-81ca-1cd89d518c2d.png
.. |subjects| image:: https://cloud.githubusercontent.com/assets/13733654/20948375/d6648280-bbe1-11e6-8098-8d8dda6c6210.png
