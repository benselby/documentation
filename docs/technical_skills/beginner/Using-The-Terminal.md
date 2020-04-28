# Using the Terminal

## Just Starting Out

A great resource for learning how to use linux and the command line is ['Linux Journey'](https://linuxjourney.com/). It has free interactive tutorials with examples and exercises. If you're new to the terminal the 'Command Line' and 'Text-Fu' lessons are a great introduction to all the basics you'd need to get comfortable. If you're interested in learning more about linux in general, though, they also have some tutorials on the file system and on more advanced topics like the kernel and processes!

In addition to 'Linux Journey' there's also this [Linux tutorial website](http://www.ee.surrey.ac.uk/Teaching/Unix/) which covers some topics not touched on and does a good job of explaining concepts.

Some useful pro-tips:

1. You can open a terminal with the shortcut ctrl + alt + t
2. For many commands you can use `man <command>` to get a manual page that explains more. And just about every program, even those without manual pages, has a built in help message that you can view with `<command> -h` or `<command> --help`
3. If you need to read or write text files on the command line `nano` is a really great editor to use, and super easy to get comfortable with. It lists all of its command shortcuts at the bottom of the window while you're using it.
4. If you're running a command and are not sure where it comes from you can do `which <command name>` to get the full path to where it's stored. For instance, `which dm_xnat_extract.py` would return `/archive/code/datman/bin/dm_xnat_extract.py`. This is really useful if you want to find a script so you can read through its code (if it's not a compiled program. If it's compiled you'll just see a bunch of unreadable gibberish)

## More Experienced - Writing BASH scripts

Once you're a bit comfortable you may want to start writing bash scripts to make your life easier. [This tutorial](https://ryanstutorials.net/bash-scripting-tutorial/) is a great resource. It explains a lot of stuff commonly glossed over, such as what a shebang (#!) is and why you would actually want to write `#!/bin/bash` at the top of your scripts (this is covered in the 'What is a Bash Script' part of the tutorial).

The Linux Documentation Project also has a really great set of [beginner](https://www.tldp.org/LDP/Bash-Beginners-Guide/html/) and [advanced](https://www.tldp.org/LDP/abs/html/) tutorials. Their tutorials can be a little intimidating (overwhelming?), but they cover _everything_ you'd want to know and have tons of examples for how to use things.
