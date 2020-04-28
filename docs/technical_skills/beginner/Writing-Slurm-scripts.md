# Writing SLURM Scripts

## An introduction to writing Slurm scripts

To make use of Slurm you must employ _directives_ to define the parameters of you jobs. These directives can include many important details such as expected time-to-completion, partition assignment, job name, and resource allocation. In scripts intended for use with Slurm, such directives are provided at the top of the script immediately following the shebang. Here we'll cover the following commonly used Slurm directives:

* `--job-name`, to specify the name your job will take
* `--time`, to specify a time limit for your job
* `--partition`, to specify a partition for your job
* `--ntasks`, to specify a number of task to perform
* `--cpus-per-task`, to specify the size of tasks in a job
* `--array`, to specify the parameters of job parallelisation

### For example

If these directives seem similar to arguments that may be passed on the command line, that's because they are. Each Slurm directive can also be passed to sbatch when running a script on the command line; however doing so is inconvenient as it requires you to type the directives correctly each time. The preferred way to pass directives to sbatch is within the script itself. For example, here is a simple Slurm script:

```bash
#!/bin/bash
#SBATCH --job-name=hello-slurm-job_%A
#SBATCH --time=00:20:00
#SBATCH --partition=high-moby
#SBATCH --ntasks=2
#SBATCH --cpus-per-task=2
#SBATCH --output=/projects/myname/slurm_%j.out
#SBATCH --error=/projects/myname/slurm_%j.err

echo "Hello, the following script was executed on `hostname -s`."
```

In this script, the `job-name` directive ensures that your job is given a distinctive name. Here, the '%A' component ensures that, if your job requires multiple runs (called 'tasks'), each will have a unique number _as part of its name_.

The `time` directive specifies that the job should require no more than 20 minutes to completion. Should it, for whatever reason, exceed 20 minutes, it will be liable to be killed by Slurm, so this is an important directive to be correct about.

The `partition` directive specifies that the job should be eligible to run on the high-moby partition. The organisation of our local Slurm cluster makes partition selection slightly important as it ensures your job gets priority access to the subset of machines in the partition you selected. Don't worry, though, if you select no partition, your job will simply be eligible to run anywhere, with no special priority.

The `ntasks` directive specifies that this script wants to be run twice. Those two runs of your script may take place on any computer and in any order, so long as they both happen. Your job will not be considered to have finished until this many copies of it have run somewhere.

The `cpus-per-task` directive specifies that each copy of the script that runs will expect to have the use of two cpu cores. Like `time` this is fairly important because asking for compute resources is an important determinant of when and how your job will be allowed to run. Try not to over-request resources as it will deprioritise your jobs. This will be covered later in another subsection on Resource Allocation.

Finally, the `output` and `error` simply specify a path where the standard streams of the job will be sent. This too will be covered in a later section on Logging.

### Intro to arrays

These directives are the core of Slurm's usefulness as a queueing system. They and some others not covered here allow you to indicate the dimensions of your jobs, which allows Slurm to sensibly schedule your jobs alongside the jobs of potentially many other people, while granting at least some resources to everyone. Now we will cover a very important directive which you will fre
