Transferring Large Datasets
===========================

Transfer files via the SCC. The RIC network (our home network) should
only be used to transfer 100-200 GB a day max. For example, to copy data
to SciNet via the SCC:

.. code:: sh

    # First, create an empty folder on the SCC 
    jp@tigrlab:~$ ssh scc mkdir mnt

    # Mount the folder of interest (e.g. /projects/jp)
    # The port 9876 can be any number between 1024-66535
    jp@tigrlab:~$ ssh -R 9876:localhost:22 scc sshfs jp@localhost:/projects/jp mnt -p 9876

    # From a separate terminal, connect to SciNet datamover node
    jp@tigrlab:~$ ssh scinet
    jp@scinet-login:~$ ssh datamover1

    # from the Scinet datamover node, rsync your data from the scc
    jp@scinet-datamover:~$ rsync -az scc:mnt/myproject /scratch/.../ 

Affiliations
============

The official affiliation you should list for work done in the TIGR lab
is: > Kimel Family Translational Imaging-Genetics Research Lab, Research
Imaging Centre, Campbell Family Mental Health Research Institute, Centre
for Addiction and Mental Health, Toronto, Canada

This is a reminder that the University of Toronto and the TAHSN
hospitals (and their research institutes) have agreed that faculty
members should list their U of T affiliation, in addition to their
hospital affiliation, in all their publications and public
presentations. The Dean’s Office, Faculty of Medicine has suggested that
the following formats be used to acknowledge the University department
in all publications and public presentations:

-  Short form - Hospital or Research Institute, Department of
   Psychiatry, University of Toronto, street address
-  Long form - Research Institute/Clinical Program, Hospital (and, where
   appropriate, Corporation, i.e., UHN), Department of Psychiatry,
   University of Toronto, street Address

SOP tracking documents
======================

Found on the CAMH ``T`` drive:
``T:\Geriatric Mental Health Program\Memory Clinic Research & Neurogeriatric Research\Neurogeriatric Research\Kimel\``
