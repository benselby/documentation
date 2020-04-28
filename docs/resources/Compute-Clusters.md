# Available Clusters
We have three clusters available to us:
- [Our local cluster](#local-cluster)
- [The SCC](#the-scc)
- [Scinet](#scinet)

All three clusters use Slurm. A quick overview of slurm commands can be [found here](https://www.rc.fas.harvard.edu/resources/documentation/convenient-slurm-commands/). Their official documentation [is here](https://slurm.schedmd.com/)

## Submitting jobs
Commands available:
  - sbatch. Built in to slurm and available on all three clusters. Docs: `man sbatch` or the official [docs here](https://slurm.schedmd.com/sbatch.html). 
  - qbatch. A python package installed on our cluster and the SCC. Makes it a bit easier to submit jobs by automatically setting certain settings. Docs: Official [docs here](https://github.com/pipitone/qbatch)

## Checking on your jobs
`sacct`. Without any options it will show a list of your submitted jobs only. If you use `sacct -a` it will show you all jobs currently submitted so you can get an overview of how busy the queue is.

## Cancelling jobs
Cancel a specific job:
`scancel <job id>`
Cancel all of your jobs:
`scancel -u <username>`

# Accessing the clusters

## Local Cluster
You don't need to do anything special to access our cluster. All our computers are set up to submit jobs to it. It runs on our workstations, three dedicated compute nodes (40 cores, 128MB each) and a dedicated GPU computing node (4xGeForce Titan GPUs) and is the smallest of the three clusters available.

## The SCC
Anyone with a lab account can get access to CAMH's Specialised Computing Cluster by [submitting a request here](https://edc.camhx.ca/redcap/surveys/?s=XMNAYAWLW9). After you get an account you can log in with `ssh <username>@scclogin.camhres.ca`. Generally the username and password you use will be the same as the ones you use to log in to our workstations. Their full documentation is [available here](http://info2.camh.net/scc/index.php/Main_Page). This cluster is larger than ours, and is entirely dedicated to running jobs, so it can churn through a lot more work. But it's also used by multiple labs at CAMH so it can be busier than ours.

## Scinet
Our instructions for registering for an using SciNet can be [found here](https://github.com/TIGRLab/admin/wiki/SciNet). Scinet is the largest of all three clusters, but is widely (and heavily) used so you're more likely to have to wait. You'll also have to be more careful about how you structure your jobs and what resources you request because they're more strict.

Once you have an account you can login with `ssh <scinet username>@niagara.scinet.utoronto.ca`
