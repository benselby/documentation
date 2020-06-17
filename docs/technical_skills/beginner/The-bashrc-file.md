# The .bashrc File

The bashrc file is a hidden file that sits in your home directory (at `~/.bashrc`). It can hold any command you'd type into the terminal directly and is run automatically when you start the terminal. It's a great place to use aliases to set shortcuts or to auto-load modules you always need or set any environment variables you might always want defined.

If you're not familiar with aliases, they allow you to give a nickname to a bash command so that you can quickly and easily run certain commands you use a lot. For instance, if you have a very long path to a directory you frequently work out of you could make an alias to change you to that directory with a very short keyword instead. It can be really powerful and is pretty much just limited by what you can think up to do with it.

The format for setting an alias is: `alias <name>="command(s)"`. You can also run multiple commands under one alias by just throwing a semi-colon after each command within the quotation marks.

A few examples:

```bash
# Change to the spins nifti dir just by typing 'spins'
alias spins="cd /archive/data/SPINS/data/nii"

# Echo the number of subjects in the spins nifti folder that aren't phantoms just by typing 'sub_count'
alias sub_count="echo `ls /archive/data/SPINS/data/nii | grep SPN01 | grep -v PHA | wc -l`"

# Load a list of modules you might commonly use all at once by typing a keyword (in this case 'load_modules')
alias load_modules="module load FSL; module load connectome-workbench; module load freesurfer; module load ciftify/2.3.3;"
```

If you end up using private modules you can also use the bashrc file to add your modules automatically to your list of available modules without having to type `module use <path to your module files>` every time you open a terminal (see [Private Modules](/resources/Private-Modules) for more info).

One thing to note, though: If you put all of this in your bashrc file in your home directory then as soon as you move to another machine you'll lose all your predefined configuration. One solution to this is to put your .bashrc file in your projects (so it's backed up and available everywhere) and then in the bashrc file in your home directory (`~/.bashrc`) add `source <path to the bashrc in your projects>`. **NOTE:** Don't just make a symlink named .bashrc in your home that points to the one in your projects! If the network ever goes down you wont be able to log in at all!
