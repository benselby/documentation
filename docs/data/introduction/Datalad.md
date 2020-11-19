# Who is this for?
  - Anyone who will access the data stored in `/archive`
  - Anyone who will run pipelines that will output data into `/archive`
  - Anyone who will want to use datasets from `/archive` for private analysis

This document briefly describes the usage and purpose of [DataLad](https://www.datalad.org/), the system used in Kimel Lab for versioning and tracking both processed and unprocessed research data.

# Preamble: What *is* DataLad?

## Version Control: `git` and `datalad`

For a brief primer on version control using `git`, follow [this link](/technical_skills/beginner/Using-Git). Essentially, `git` is a system for tracking the changes made to text files. You should be using it to keep track of any code or scripts you write as part of this lab, and you should *certainly* be using it for any code on which you intend to collaborate with others.

What `git` is to code and plain text files, `datalad` is to large data files. In Kimel Lab, we use `datalad` to track all changes made to the datasets we have access to; in practice, this is (or should be) all files located in `/archive/data/[Study]`.

Practically, this serves the purpose of enhancing [reproducibility of research](/technical_skills/beginner/Tips-for-reproducible-research). `datalad` can be used to ‘*lock down*’ data files so no changes can be made to them except those which `datalad` records. It also allows us, via our [TIGR_PURR](https://tigrlab-nextflow-personal.readthedocs.io/en/latest/) system, to automatically track which version of a pipeline was used and what effects (if any) came from varying the versions of the software we use when processing data.

Also importantly for users, `datalad` allows the remote sharing of data, much as `git` does for code; this means that you can use `datalad` to quickly and painlessly download exactly the data you want, where you want, without also downloading very large amounts of data you *may not want*. If you’re only interested in working with the `T1w` files from some study, `datalad` allows you to remotely acquire *only* `T1w` files, without also downloading the entire rest of the study dataset.

Finally, since you will also often be working with data here in the lab but *will not* have permission to push your results directly back into `/archive/data`, you should have your own private copy of the data in your `/projects` directory. Since it needlessly uses disk space for individual users to duplicate entire study directories, `datalad` provides a convenient system to allow you to acquire only the data you wish to work with.

# In practice

## Install your first dataset, get your first data

To start using `datalad`, you need to be ready to use `git`. If you’re not already a regular `git` user, now is the time to [start](/technical_skills/beginner/Using-Git). To begin with, we’ll make a `.gitconfig` file. This file stores information regarding your identity (and other options that aren’t relevant here), which `datalad` needs to know about so it can track *who has done what to the data being shared*. If you don’t already have a `.gitconfig` file, running the following commands from the command line will create yours:

```sh
git config --global user.name "FirstName (optional: MiddleInitial) LastName"
git config --global user.email "your@email.com"
```

This will create the file in your home directory (‘`~/.gitconfig`’) and add to it the name and e-mail address provided. If you do not have a `.gitconfig`, some `datalad` commands will fail, since they check this information to associate you with the updates you make to datasets.

Once we have a `.gitconfig` we can begin to run some `datalad` commands. Change directory to your `/projects` by typing `cd /projects/[yourusername]` now. We provide a `datalad` quarantine module, which you should also load now. We’re going to learn to ‘*install*’ a dataset:

```sh
module load datalad
datalad install -s file:///archive/data/ASDD/pipelines/bids_apps/ciftify ASDD_ciftify
```

This tells `datalad` to install the dataset located at the source (`-s`)  `/archive/data/ASDD/pipelines/bids_apps/ciftify`, which contains the ciftify outputs for the ASDD study. This should result in some output like the following:

```log
[INFO   ] Cloning file:///archive/data/ASDD/pipelines/bids_apps/ciftify into '/projects/[yourusername]/ASDD_ciftify'
install(ok): /projects/[yourusername]/ASDD_ciftify (dataset)
```

You should now have a directory in your `/projects` by the name of `ASDD_ciftify`; this is a ‘dataset’. In DataLad parlance, a dataset is *either* a curated, metadata-enriched store containing a collection of data, *or* it is a mirror of such a collection. In *both cases* the dataset always stores metadata information about where the original data is available from; even a dataset which *actually contains* all the data will keep track of all of its ‘*siblings*’: remote datasets where additional, duplicate copies of the data are available.

The original `/archive/data/ASDD/pipelines/bids_apps/ASDD_ciftify` directory contains just over 7GB of data, while your newly installed ‘mirror’ dataset of ciftify contains only about 950MB of data! Why? By default, `datalad` always avoids *actually moving data* whenever possible. Instead, it mirrors the original dataset’s directory and file structure, and *only* stores the corresponding metadata and links that point to the real original location of the data - this way, you can have the data *available to you*, without actually having to fetch all 7GB of it.

For such a small dataset as ASDD_ciftify, this hardly makes a difference, but for large datasets, the ability to lazily fetch only those data you really want becomes invaluable. Because of this intelligent laziness, `datalad install` is generally an extremely fast command, even when installing enormous datasets: all it actually copies is the metadata indicating where to fetch the data.

### What have we actually got?

Inside this new dataset is an exact copy of the directory tree found originally in `/archive/data/ASDD/pipelines/bids_apps/ciftify`. Furthermore, *some* data was, in fact, copied. Let’s take a look:

```sh
cd ASDD_ciftify
ls -lh sub-CMHAEF011
```

```log
total 136K
-rw-r--r-- 1 [yourusername] kimel 128K Mar 10 15:31 cifti_recon_all.log
drwxr-xr-x 1 [yourusername] kimel 2.0K Mar 10 15:31 MNINonLinear
drwxr-xr-x 1 [yourusername] kimel  284 Mar 10 15:31 T1w
```

The top file, `cifti_recon_all.log`, is a text file and was, in fact, copied over from the `/archive`. Because text files are generally very small and extremely sparse compared to neuroscience imaging files and similar, our DataLad configuration ensures that they *are* fetched whenever you install a dataset, as long as they’re smaller than a 1kB (this size was chosen because it is small enough that getting such files will still be quick even if there are very many of them).

Doing it this way also serves to make installed datasets more human-approachable; text files often contain metadata themselves, and can serve to allow you to read about the contents of the dataset you have just installed via a `README` file, or peruse small log files to learn about success or failure of recent pipelines run over this dataset, allowing you to more easily determine how worthwhile it is to pull specific, large binary data files. Furthermore, text files which are large than 1kB (in the context of datasets, that is) usually contain large amounts of repetitive text, and therefore usually ought to be pulled explicitly anyway. Let’s investigate some of the binary files in this dataset now. Here are some `T1w`s:

```sh
ls -lh sub-CMHAEF011/T1w/T1w*
```

```log
lrwxrwxrwx 1 [yourusername] kimel 210 Mar 10 15:31 sub-CMHAEF011/T1w/T1w_brain.nii.gz -> ../../.git/annex/objects/3F/wk/SHA256E-s2039718--19d57cbf671b27a641fc4d17fdd72ab71f449510740a12ed94605e55779d2d6e.nii.gz/SHA256E-s2039718--19d57cbf671b27a641fc4d17fdd72ab71f449510740a12ed94605e55779d2d6e.nii.gz
lrwxrwxrwx 1 [yourusername] kimel 210 Mar 10 15:31 sub-CMHAEF011/T1w/T1w.nii.gz -> ../../.git/annex/objects/FW/gv/SHA256E-s3394192--6529db94d85ae8fc408b25889711ef9a9d53010a9c3f4b90f3bb2049ece2444e.nii.gz/SHA256E-s3394192--6529db94d85ae8fc408b25889711ef9a9d53010a9c3f4b90f3bb2049ece2444e.nii.gz
```

These rather imposing looking files are links. DataLad uses them to track precise references to the original data, including its location and content. If you wanted to work with these data, however, you wouldn’t be able to - they don’t exist in this copy of the dataset yet, we only have the links that tell `datalad` how to acquire them. Let’s get them now!

```sh
datalad get sub-CMHAEF011/T1w/T1w*
```

```log
get(ok): sub-CMHAEF011/T1w/T1w.nii.gz (file) [from origin...]
get(ok): sub-CMHAEF011/T1w/T1w_brain.nii.gz (file) [from origin...]
action summary:
  get (ok: 2)
```

If you `ls -lh` those files again, you’ll see they’re still links, but the data for those two files *and nothing else* has now been copied from the `/archive` to your `/projects`. What if we want to get all the `T1w`s from ASDD_ciftify?

```sh
datalad get **/T1w*
```

```log
get(ok): sub-CMHAEF015/T1w/T1w_brain.nii.gz (file) [from origin...]
get(ok): sub-CMHAEF011/T1w/aparc+aseg.nii.gz (file) [from origin...]
get(ok): sub-CMHAEF011/T1w/aparc.a2009s+aseg.nii.gz (file) [from origin...]
get(ok): sub-CMHAEF011/T1w/brainmask_fs.nii.gz (file) [from origin...]
get(ok): sub-CMHAEF011/T1w/wmparc.nii.gz (file) [from origin...]
get(ok): sub-CMHAEF015/T1w/T1w.nii.gz (file) [from origin...]
get(ok): sub-CMHAEF015/T1w/aparc+aseg.nii.gz (file) [from origin...]
get(ok): sub-CMHAEF015/T1w/aparc.a2009s+aseg.nii.gz (file) [from origin...]
get(ok): sub-CMHAEF015/T1w/brainmask_fs.nii.gz (file) [from origin...]
get(ok): sub-CMHAEF015/T1w/wmparc.nii.gz (file) [from origin...]
  [13 similar messages have been suppressed]
get(ok): sub-CMHAEF011/T1w (directory)
get(ok): sub-CMHAEF015/T1w (directory)
get(ok): sub-CMHAEF012/T1w (directory)
action summary:
  get (notneeded: 5, ok: 26)
```

#### Aside: ‘globstar’

The ‘`**`’ pattern (known, incidentally, as a ‘globstar’), tells `datalad` to recursively find everything in every directory below the current working directory, and the regular glob pattern ‘`*`’ after the ‘`T1w`’ tells `datalad` to limit its fetches to files with names beginning with `T1w`. As a general rule, any form of wildcard expansion or pattern matching which is available *from the bash shell* for matching filenames will work with `datalad`, making it extremely convenient and economical to fetch *all and only* the files you want to work with. Having fetched our `T1w`s, our local installation of the dataset still only contains 1002MB of data. Nice!

If the globstar didn’t work, just throw `shopt -s globstar` into your `~/.bashrc` file, and `source /etc/profile`. It’s a useful shell option that might not be set for your shell. You can also just run `shopt -s globstar` to turn it on for this particular shell session, but not future ones. Do this now, as we'll be using globstar to match subsets of files for the rest of his demo.

## Working with dataset data

So we’ve run `datalad install` to install the dataset in `ASDD_ciftify`, and ran `datalad get` on all the `T1w` files into the local copy of our dataset. Everything else other than text files is still just a link and some metadata. That's what we want. Let's say we want to make some changes that systematically modify the `T1w` in a not particularly useful, but pedagogically simple way. Notice we've added a question mark to our pattern - this is just so that we don't try to act on any directories.

``` sh
shred -vxzn0 **/T1w?*
```

For the curious, this action will exactly replace the contents of these files with NULs, i.e. it will overwrite the data with binary zeroes.

If you tried this at home, you won't need me to tell me that they will have all failed with stderr of "Permission denied". This is because, of course, DataLad locks down the files it's saved. In order to do this and totally bork our `T1w`s, we'll need to unlock them first! There are multiple ways of doing this, but we'll focus on only two salient ones: **`datalad unlock`** and **`datalad run`**.

### `datalad unlock`

Working with files in this way is a three step process. In the first step we unlock the files we want to operate on; in the second step, we perform the desired operation on those unlocked files; and in the third step, we save the new copies of those files to the dataset. Note that this is *technically* only necessary if we're going to be *updating those files in place*. `datalad unlock` is not necessary if the files won’t be changed.

``` sh
datalad unlock **/T1w?*

shred -vxzn0 **/T1w?*

datalad save **/T1w?*
```

More realistically, in a job script, rather than including pattern matching such has `**/T1w?*` for every single command expected to modify a dataset file, we'll want to use some command to produce a file list we want to operate on, and store that list in a variable. This is important for readability, but also because actions taken between the `datalad unlock` and the `datalad save` commands might cause the `**/T1w?*` filename pattern to match *a different set of files depending on when it runs*.

That is to say, because this is stateful, state management is important. If your command might *create* new files, make sure your script makes a new variable that includes the newly created filenames; likewise if your job *deletes* unlocked files, make sure your variable *dis*includes files that won't be there after your job is done. If files are renamed, make sure you unlock them under the old name and save them under the new name. Using a matching pattern like `**/T1w?*` each time provides no guarantees of correct behaviour, if you will be changing the number or name of files in the dataset.

``` sh
flist=`realpath -ms --relative-to=${dataset} **/T1w?*`

datalad unlock $flist
shred -vxzn0 $flist
datalad save $flist
```

Note that in the above example, we explicitly provide `$flist` without "doublequotes", since doing so effectively coerces the newlines produced by `realpath` to spaces, which makes it more suitable for shell parsing.

In the above example, the `-s` flag allows `realpath` to grab the symlink's name, rather than following the symlink into the underlying dataset object store located in `${dataset}/.git/annex/objects`, which we *don't* want to act on, and the `-m` example allows `realpath` to grab the symlink names *even if there is no underlying git annex object*, which is to say, *even if we haven't run `datalad get` to fetch these files yet*!

### `datalad run`

Much of this unlocking and re-saving overhead can be automated with the `datalad run` command. `datalad run` acts to wrap some other command, accepting a parameter list of filesystem inputs and outputs, flagged on the command line with `-i` and `-o`, respectively. It will then ensure the outputs are saved (and possibly unlocked ahead of time, if they already existed).

``` sh
datalad run -o '**/T1w?*' "shred -vxzn0 {outputs}"
```

In this example, we've used pattern matching inside 'singlequotes'. Ordinarily this does not work except that in this case these patterns are simply being passed literally to DataLad which performs its own expansion on them, assigning the results of that expansion to internal arrays which can be addressed in the command line passed to `datalad run` via the parameters `{outputs}` and `{inputs}`. If the expanded pattern matched more than one file (as we'd normally expect/hope), the individual files can be indexed, in lexicographic order, in the normal way (e.g. via `{inputs[0]}` being the lexicographical first filename match, and so on).

Essentially, using `datalad run` in this way is effectively much like handling these file list values yourself in a script, except you offload the issue onto DataLad. In exchange for trusting DataLad to correctly handle the inputs and outputs, a successful `datalad run` command will make a `git commit` to the dataset with a commit message featuring a json describing the action taken to perform that commit's changes, looking something like this:

``` json-with-comments
commit f50fd28ea90f63c9daa9f7d8dd78eef7df6e3ff2 (HEAD -> master)
Author: Kevin J Witczak <kevin_witczak@camh.ca>
Date:   Fri Aug 21 18:13:59 2020 -0400

    [DATALAD RUNCMD] shred -vxzn0 sub-CMHAEF015/T1w/T1w_brain...

    === Do not change lines below ===
    {
     "chain": [],
     "cmd": "shred -vxzn0 {outputs}",
     "dsid": "12c50458-a7e9-4586-b772-a5dfdb63d6c4",
     "exit": 0,
     "extra_inputs": [],
     "inputs": [],
     "outputs": [
      "**/T1w*"
     ],
     "pwd": "."
    }
    ^^^ Do not change lines above ^^^
```

Performing actions with `datald run` therefore allows you perform `datalad rerun` commands, in which DataLad parses the commit message and attempts to re-perform the same command. *Iff* no changes were made as the result of performing this command again, `datalad rerun` makes no new commit and the dataset remains on the current commit of the current branch. `datalad rerun` takes a single unflagged argument, the commit id of the a commit made by `datalad run` (if no commit id is provided, it will use the last commit made by `datalad run` on the current branch). Other options exist to both allow, for instance, branches to produced or checked out as a result of or as a prerequisite to the performance of a `datalad run` command. These are flagged with the `-b` or `--branch` parameter and the `--onto` flag, respectively.

## Creating and deleting datasets

Finally, we'll look at creating a new dataset. This can be done with the `datalad create` command.

``` sh
datalad create new_dataset
```

``` sh
[INFO   ] Creating a new annex repo at /projects/[yourusername]/new_dataset
[INFO   ] Scanning for unlocked files (this may take some time)
create(ok): /projects/[yourusername]/new_dataset (dataset)
```

This will create or accept a directory and initialise it as a DataLad dataset. If a directory already exists and it already contains files, the `--force` option must be used. **Be careful when doing so, since providing no directory name will cause it to attempt to dataladen the current working directory.**

DataLad also provides a series of standard config options which can be applied at init time. These can be applied with the `datalad create -c` option. Multiple `-c` flags may be used for the same dataset. These apply attributes to the dataset’s `.gitattributes` file in in order to specify various usage cases. Two useful ones, which we’ll cover briefly, are the `bids` config and the `text2git` config.

The `bids` config adds the following to the dataset’s attributes:

``` git-attributes
* annex.backend=MD5E
**/.git* annex.largefiles=nothing
.bidsignore annex.largefiles=nothing
CHANGES annex.largefiles=nothing
README annex.largefiles=nothing
code/** annex.largefiles=nothing
dataset_description.json annex.largefiles=nothing
```

While the `text2git` config adds the following:

``` git-attributes
* annex.backend=MD5E
**/.git* annex.largefiles=nothing
* annex.largefiles=((mimeencoding=binary)and(largerthan=0))
```

Crucially, datasets in our lab should **not** use `* annex.backend=MD5E` as an attribute. Unfortunately, DataLad has eliminated the intereface option to specify a backend in recent versions, despite having selected a cryptographically insecure backend. All archive datasets in TIGRLab use `SHA256E`, the *encouraged* backend by both git and git annex. When *creating any dataset*, the following action must be taken, **even if you did not specify a configuration with the `-c` flag**:

``` sh
datalad create new_dataset
sed -i 's/MD5/SHA256/' new_dataset/.gitattributes
```

This can be performed at any time **before any data has been saved with `datalad save`**. Any work you do with a personally maintained dataset will be expected to be in, or upgraded to, the `SHA256E` backend before it is accepted into the archive.

Finally, if you choose the `text2git` config option pushes text files into a regular git repo rather than locking and annexing them can result in large `datalad install` operations, since files like `*.gii`s, which are text files that contain huge amounts of data and may, in fact, make up the bulk of a dataset’s size, get **pulled automatically when installing the dataset**. As such, please perform the following action when using this config option:

``` sh
datalad create -c text2git new_dataset
sed -i 's/and/or/;s/=0/=1024/;s/MD5/SHA256/' new_dataset/.gitattributes
```

Doing so will ensure that only small text files are placed directly in the repo, ensuring against massive, potentially very data-intensive `datalad install` operations. The combination of `text2git` and `bids` is what is used on the `/archive` datasets, along with the `sed` modifications for specifying the 1kB size of the text files to be allowed and the `SHA256E` backend.

### Deleting

And, ultimately, let’s say you installed a dataset, got some data, did some work, but you don’t need it any more. *Or* you cloned a dataset multiple times by accident. You now want to delete your copies of the dataset, which DataLad *doesn’t want to let you do*. You *could* try unlocking every single file and `rm`ing them by hand, but this is tedious and slow. The correct way to delete a dataset you have installed from somewhere else is to do the following:

```sh
cd ${dataset}
datalad remove *
git annex uninit
cd ..
rm -rf ${dataset}
```

In this case, we are telling datalad to eliminate all local copies of data from where you installed it, and the `git annex uninit` command tells `git annex`, the underlying engine which powers DataLad, to delete the locked repository annex. Once this is gone, the entire dataset folder can be deleted as normal.

*Note that this will not work for datasets which you created or own the original copy of.* `datalad` complains strongly when deleting the last known version of any data anywhere. Ordinarily this won’t come up, as you won’t ever be deleting the contents of the archive, only **your local copy of archive data**. If you create your own dataset, a dataset which is never cloned or installed to another location; you **must use `git annex drop --force`**, as this compels `git annex` to ignore its internal data safety checks and kill the last known existing copies of whatever was in your dataset. Use this only when you know for certain that you will never need something again.
