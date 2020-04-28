# Who is this for?
  - Anyone who will access the data stored in `/archive`
  - Anyone who will run pipelines that will output data into `/archive`
  - Anyone who will want to use datasets from `/archive` for private analysis

This document briefly describes the usage and purpose of [DataLad](https://www.datalad.org/), the system used in Kimel Lab for versioning and tracking both processed and unprocessed research data.

# Preamble: What *is* DataLad?

## Version Control: git and datalad

For a brief primer on version control using `git`, follow [this link](https://github.com/TIGRLab/documentation/wiki/Using-Git). Essentially, `git` is a system for tracking the changes made to text files. You should be using it to keep track of any code or scripts you write as part of this lab, and you should *certainly* be using it for any code on which you intend to collaborate with others.

What `git` is to code and plain text files, `datalad` is to large data files. In Kimel Lab, we use `datalad` to track all changes made to the datasets we have access to; in practice, this is (or should be) all files located in `/archive/data/[Study]`.

Practically, this serves the purpose of enhancing [reproducibility of research](https://github.com/TIGRLab/documentation/wiki/Tips-for-reproducible-research). `datalad` can be used to ‘*lock down*’ data files so no changes can be made to them except those which `datalad` records. It also allows us, via our [TIGR_PURR](https://tigrlab-nextflow-personal.readthedocs.io/en/latest/) system, to automatically track which version of a pipeline was used and what effects (if any) came from varying the versions of the software we use when processing data.

Most importantly for users, however, `datalad` allows the remote sharing of data, much as `git` does for code; this means that you can use `datalad` to quickly and painlessly download exactly the data you want, where you want, without also downloading very large amounts of data you *may not want*. If you’re only interested in working with the `T1w` files from some study, `datalad` allows you to remotely acquire *only*, say, `T1w` files (or any other subset of files easily describable from the command line), without also downloading the entire rest of the study dataset.

Finally, since you will also often be working with data here in the lab but *will not* have permission to push your results directly back into `/archive/data`, you should have your own private copy of the data in your `/projects` directory. Since it needlessly uses disk space for individual users to duplicatively copy study directories, `datalad` provides a convenient system to allow you to acquire only the data you wish to work with.

# In practice

## Install your first dataset, get your first data

To start using `datalad`, you need to be ready to use `git`. If you’re not already a regular `git` user, now is the time to [start](https://github.com/TIGRLab/documentation/wiki/Using-Git). To begin with, we’ll make a `.gitconfig` file. This file stores information regarding your identity (and other options that aren’t relevant here), which `datalad` needs to know about so it can track *who has done what to the data being shared*. If you don’t already have a `.gitconfig` file, running the following commands from the command line will create yours:

```sh
git config --global user.name "FirstName (optional: MiddleInitial) LastName"
git config --global user.email "your@email.com"
```

This will create the file in your home directory (‘`/home/[yourusername]/.gitconfig`’) and add to it the name and e-mail address provided. If you do not have a `.gitconfig`, some `datalad` commands will fail, since they check this information to associate you with the updates you make to datasets.

Now that we have a `.gitconfig` we can begin to run some `datalad` commands. Change directory to your `/projects` by typing `cd /projects/[yourusername]` now. We’re going to learn to ‘*install*’ a dataset:

```sh
datalad install -s file:///archive/data/ASDD/pipelines/bids_apps/ciftify
```

This tells `datalad` to install the dataset located at the source (`-s`)  `/archive/data/ASDD/pipelines/bids_apps/ciftify`, which contains the ciftify outputs for the ASDD study. This should result in some output like the following:

```log
[INFO   ] Cloning file:///archive/data/ASDD/pipelines/bids_apps/ciftify into '/projects/[yourusername]/ciftify'
install(ok): /projects/[yourusername]/ciftify (dataset)
```

You should now have a directory in your `/projects` by the name of `ciftify`; this is a ‘dataset’. In DataLad parlance, a dataset is *either* a curated, metadata-enriched store containing a collection of data, *or* it is a mirror of such a collection. In *both cases* the dataset always stores metadata information about where the original data is available from; even a dataset which *actually contains* all the data will keep track of all of its ‘*siblings*’: remote sites where additional, duplicate copies of the data are available.

The original `/archive/data/ASDD/pipelines/bids_apps/ciftify` directory contains just over 7GB of data, while your newly installed ‘mirror’ dataset of ciftify contains only about 950MB of data! Why? By default, `datalad` always avoids *actually moving data* whenever possible. Instead, it mirrors the original dataset’s directory and file structure, and *only* stores the corresponding metadata and links that point to the real original location of the data - this way, you can have the data *available to you*, without actually having to fetch all 7GB of it.

For such a small dataset as ASDD-ciftify, this hardly makes a difference, but for large datasets, the ability to lazily fetch only those data you really want becomes invaluable. Because of this intelligent laziness, `datalad install` is generally an extremely fast command, even when installing enormous datasets: all it actually copies is the metadata indicating where to fetch the data.

## What have we actually got?

Inside this new dataset is an exact copy of the directory tree found originally in `/archive/data/ASDD/pipelines/bids_apps/ciftify`. Furthermore, *some* data was, in fact, copied. Let’s take a look:

```sh
cd ciftify
ls -lh sub-CMHAEF011
```

```log
total 136K
-rw-r--r-- 1 [yourusername] kimel 128K Mar 10 15:31 cifti_recon_all.log
drwxr-xr-x 1 [yourusername] kimel 2.0K Mar 10 15:31 MNINonLinear
drwxr-xr-x 1 [yourusername] kimel  284 Mar 10 15:31 T1w
```

The top file, `cifti_recon_all.log`, is a text file and was, in fact, copied over from the `/archive`. Because text files are generally very small and extremely sparse compared to neuroscience imaging files and similar, our DataLad configuration ensures that they are *always* fetched whenever you install a dataset.

This also serves to make installed datasets more human-approachable; text files often contain metadata themselves, and can serve to allow you to read about the contents of the dataset you have just installed via a `README` file, or peruse log files to learn about the success or failure of recent pipelines run over this dataset, allowing you to more easily determine how worthwhile it is to pull specific, large binary data files. Let’s investigate some of those binaries now. Here are some `T1w`s:

```sh
ls -lh sub-CMHAEF011/T1w/T1w*
```

```log
lrwxrwxrwx 1 [yourusername] kimel 210 Mar 10 15:31 sub-CMHAEF011/T1w/T1w_brain.nii.gz -> ../../.git/annex/objects/3F/wk/SHA256E-s2039718--19d57cbf671b27a641fc4d17fdd72ab71f449510740a12ed94605e55779d2d6e.nii.gz/SHA256E-s2039718--19d57cbf671b27a641fc4d17fdd72ab71f449510740a12ed94605e55779d2d6e.nii.gz
lrwxrwxrwx 1 [yourusername] kimel 210 Mar 10 15:31 sub-CMHAEF011/T1w/T1w.nii.gz -> ../../.git/annex/objects/FW/gv/SHA256E-s3394192--6529db94d85ae8fc408b25889711ef9a9d53010a9c3f4b90f3bb2049ece2444e.nii.gz/SHA256E-s3394192--6529db94d85ae8fc408b25889711ef9a9d53010a9c3f4b90f3bb2049ece2444e.nii.gz
```

These rather imposing looking files are the links. DataLad uses them to track precise references to the original data, including its location and content. If you wanted to work with these data, however, you wouldn’t be able to - they don’t exist in this copy of the dataset yet, we only have the links that tell `datalad` how to acquire them. Let’s get them now!

```sh
datalad get sub-CMHAEF011/T1w/T1w*
```

```log
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF011/T1w/T1w_brain.nii.gz (file) [from origin...]
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF011/T1w/T1w.nii.gz (file) [from origin...]
action summary:
  get (ok: 2)
```

If you `ls -lh` those files again, you’ll see they’re still links, but the data for those two files *and nothing else* has now been copied from the `/archive` to your `/projects`. What if we want to get all the `T1w`s from ASDD-ciftify?

```sh
datalad get **/T1w*
```

```log
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF011/T1w/wmparc.nii.gz (file) [from origin...]
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF011/T1w/aparc.a2009s+aseg.nii.gz (file) [from origin...]
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF011/T1w/brainmask_fs.nii.gz (file) [from origin...]
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF011/T1w/aparc+aseg.nii.gz (file) [from origin...]
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF012/T1w/T1w.nii.gz (file) [from origin...]
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF012/T1w/aparc+aseg.nii.gz (file) [from origin...]
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF012/T1w/T1w_brain.nii.gz (file) [from origin...]
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF012/T1w/wmparc.nii.gz (file) [from origin...]
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF012/T1w/brainmask_fs.nii.gz (file) [from origin...]
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF015/T1w/T1w_brain.nii.gz (file) [from origin...]
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF012/T1w/aparc.a2009s+aseg.nii.gz (file) [from origin...]
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF015/T1w/aparc+aseg.nii.gz (file) [from origin...]
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF015/T1w/T1w.nii.gz (file) [from origin...]
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF015/T1w/aparc.a2009s+aseg.nii.gz (file) [from origin...]
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF015/T1w/wmparc.nii.gz (file) [from origin...]
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF015/T1w/brainmask_fs.nii.gz (file) [from origin...]
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF011/T1w (directory)
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF012/T1w (directory)
get(ok): /projects/[yourusername]/ciftify/sub-CMHAEF015/T1w (directory)
action summary:
  get (ok: 19)
```
The ‘`**`’ pattern (known, incidentally, as a ‘globstar’), tells `datalad` to recursively find everything in every directory below the current working directory, and the regular glob pattern ‘`*`’ after the ‘`T1w`’ tells `datalad` to limit its fetches to files with names beginning with `T1w`. As a general rule, any form of wildcard expansion or pattern matching which is available *from the bash shell* for matching filenames will work with `datalad`, making it extremely convenient and economical to fetch *all and only* the files you want to work with. Having fetched our `T1w`s, our local installation of the dataset still only contains 972MB of data. Nice!

## Working with dataset data

... TODO: more!
...       cleanup!