# Modules

Modules are a neat way to allow multiple (possibly conflicting) versions of the same software to be installed. They also help make analyses more reproducible by allowing you to explicitly state which versions of what software you want to use.

## Basic commands

- `module avail`: Shows you all available software
- `module list`: Shows you which modules you're currently using. We pre-load some modules for our users that are really useful and not likely to interfere with anything (e.g. slurm to allow users to use our queue).
- `module load <modulename>`: Will let you use software that we have available. For instance, if you typed `module avail` and saw that we had FSL/5.0.0, FSL/5.0.10, and FSL/6.0.0 installed you could use `module load FSL/5.0.10` to load version 5.0.10. If you omit the version number either the newest version or the one marked 'default' will be loaded for you (e.g. `module load FSL` would have loaded FSL/6.0.0). **Be Careful!** If you rely on this feature the versions that are current / defaults could change out from under you. It's always better to specify exact versions.
- `module unload <modulename>`: Lets you unload software once you're done with it
- `module swap <modulename>`: Lets you change module versions. E.g. `module swap FSL/5.0.11` when you have 6.0.0 loaded will switch to the older version without you first having to unload 6.0.0.

There are also other commands available and various options you can set. You can see the full list of options by running `module help`

## Accessing our SCC modules

Our lab's modules aren't available by default on the SCC. To add them to the `module avail` list them you first need to run `module load /KIMEL/quarantine/modules/quarantine`

## Private Modules

Private modules are like regular modules but they're available only to the user that makes them. This means that users who are not system admins can install _some_ software of their own and load it as they need it. If you think you might need this, you can learn more at [Private Modules](/resources/Private-Modules)

## Additional Info

Knowing more isnt necessary to use the modules effectively, but if you're curious you can learn more about modules from [lmod's documentation](https://lmod.readthedocs.io/en/latest/). The module files we use are stored at `/opt/quarantine/modules/` if you want to see them. They can be written in [TCL](https://en.wikibooks.org/wiki/Tcl_Programming/Introduction) or [Lua](https://en.wikipedia.org/wiki/Lua_(programming_language)).
