# Private Modules

Private modules can be used to install your own software without sudo permissions and to help you manage versions of software you need just for yourself.

## Setting up your Private Modules
1. Choose where to store your software/code. It's generally a good idea to put everything in projects or scratch so it's accessible from everywhere (and therefore usable by your queue jobs). Make a folder in this spot. You can call it whatever you like, but generally 'modules' is a good self-descriptive choice. 
2. Within the first folder make another folder which will hold the module files. You'll have one module file per installed piece of software and they'll all be stored in this sub-folder. "privatemodules" or "modulefiles" is a good choice of name for this folder, but again you can call it whatever you like.
3. In your bashrc file add the following (Note - your bashrc file should be at `~/.bashrc` and if it doesn't already exist you can just make a blank file and add this):
```bash
if [ -e "/projects" ]; then
    module use <path to your module file folder>
fi
```
**NOTE:** if you put your modules in /scratch instead you'll need to change the first line to say `-e "/scratch"`. Also note that the path you provide should be to the subfolder where your module files will go, not the parent folder where the software itself will go (i.e. use the path you created in step 2, **not** the one from step 1).

## Creating a Module File
A module file is just a text file with commands that tell your terminal how to make some piece of software usable. When you load a module file it will execute these commands and when you unload it the module system knows how to undo each command properly to restore your terminal to its prior state. 

For most software all that needs to be done is to add the location of any executable files to the user's path and perhaps set a few environment variables, but it will differ based on what you're setting up so you'll have to read the install instructions (or the install script for your software) and try to translate that into a set of module commands. Some commonly used commands are below, and you can look at our module files at `/opt/quarantine/modules/` for examples of what complete module files look like. We also have some module files at `/opt/quarantine/modules/archive_modules` that give examples of how to get a python package working directly from a git repo.

### Changing the path
You have two commands you can use: `prepend-path` if you want your new path to be searched before other paths (this is usually what you want), or `append-path` if you want it searched after. If you dont know which one you should choose, or why this would even matter, you should use `prepend-path` :)

**Format:**
```bash
prepend-path <PATHVARNAME> <PATHTOUSE>
append-path <PATHVARNAME> <PATHTOUSE>
```

**Example:**

This will add all the scripts from datman to the users command line. It's the equivalent of running `export PATH=/archive/code/datman/bin:$PATH` on the command line
```bash
prepend-path PATH /archive/code/datman/bin
```
This does the same thing, but will set the datman bin folder as the last path to search when looking for commands. It's equivalent to running `export PATH=$PATH:/archive/code/datman/bin`
```bash
append-path PATH /archive/code/datman/bin
```

### Setting Environment Variables
The command to use here is `setenv`. 

**Format:**
```bash
setenv <VARNAME> <VARVALUE>
```

**Example:**

This sets a shell variable named 'DM_CONFIG' that points to datman's master config file. It's the equivalent of running `export DM_CONFIG=/archive/code/config/tigrlab_config.yaml` on the command line.

```bash
setenv DM_CONFIG /archive/code/config/tigrlab_config.yaml
```

### Setting Temp Vars in the Module File Itself
If you want a variable to use only in your module file (i.e. you want to avoid duplicating paths and stuff in your file) you can use `set`. You can then use the variable in other module commands using the bash syntax for variables: `${varname}`

**Format:**
```bash
# To make the variable
set <VARNAME> <VARVALUE>

# To use the variable
${<VARNAME>}
```

**Example:**

This will create a variable called 'datman' set to '/archive/code/datman', usable only by the module file.
```bash
# Making the variable
set datman /archive/code/datman

# Using the variable in another command
prepend-path PATH ${datman}/bin
```
### Loading Other Modules Inside your Module
Your module file can load other existing modules if your software is dependent on another package or if you'd just like to save time by grouping certain modules together in a new module file. You can do this by just writing a normal module load statement inside the file itself, e.g. `module load connectome-workbench/1.3.2` inside our ciftify/2.3.3 module.

### Preventing Non-Compatible Modules from Being Loaded at the Same Time
If your module definitely will not work with another one of your module files (e.g. two modules that each need their own separate virtual environment) you can prevent the module system from loading the non-compatible modules, and have it emit a warning message if someone tries to load them together, by using `conflict <module file name>`. 

### Making Python Code Importable
To be able to import a python package that you're working on (without pip installing it) you need to modify the python path to include the folder that holds your python code. You can do that with the `prepend-path` and `append-path` commands.

**Format:**
```bash
prepend-path PYTHONPATH <path to your folder of python code>
```

**Example:**

Adding this to a module file would allow you to run `import ciftify` in your other python scripts without having to pip install ciftify.
```bash
prepend-path PYTHONPATH /archive/code/ciftify
```

### Activating a Virtual Environment Automatically
If you have a virtual environment that you always have to activate before you can use a software package (see our module file `/opt/quarantine/modules/ciftify/2.3.3` for an example where we've needed this) you can do this with the `puts stdout` command. One thing to keep in mind, though, is that if you use `puts stdout` to do something the module system wont know how to undo it. You have to manually add this 'undo' behavior yourself by checking which module command was run on the file and then telling the module system what to do for that command. See the example below.

**Example:**

This will automatically activate ciftify's virtual environment (stored at `/opt/quarantine/ciftify/2.3.3/build`) when the module is loaded and automatically deactivate it when the module is unloaded.
```bash
# module-info mode <STATUS> checks which module command was used
if { [module-info mode load] } {
    # Do this when the module is being loaded
    puts stdout "source /opt/quarantine/ciftify/2.3.3/build/bin/activate;"
} elseif { [module-info mode remove ] } {
    # Do this when the module is being unloaded or swapped out
    puts stdout "deactivate;"
}
```
**NOTE:** You have to enclose any commands for `puts stdout` in quotation marks and you have to end your command in a semi-colon or your module will crash when you try to use it.

