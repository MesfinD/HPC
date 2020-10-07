---
title: "Scheduling jobs"
teaching: 45
exercises: 30
questions:
- "What is a scheduler and why are they used?"
- "How do we submit a job?"
objectives:
- "Submit a job and have it complete successfully."
- "Understand how to make resource requests."
- "Submit an interactive job."
keypoints:
- "The scheduler handles how compute resources are shared between users."
- "Everything you do should be run through the scheduler."
- "A job is a lot more than just a shell script! It is workflow for all your computational work. This is what your share with collaborators and documents what you did to produce your computational results that are making up your research outcomes and allows for others to easily reproduce and validate your research."
- "If in doubt, request more resources than you will need."
---

An HPC system is a SHARED resource it might have thousands of nodes but it could also have thousands of users.
How do we decide who gets what and when?
How do we ensure that a task is run with the resources it needs?
This job is handled by a special piece of software called the scheduler.
On an HPC system, the scheduler manages which jobs run where and when.

> ## Job scheduling roleplay
> 
> Your instructor will divide you into groups taking on 
> different roles in the cluster (users, compute nodes 
> and the scheduler).  Follow their instructions as they 
> lead you through this exercise.  You will be emulating 
> how a job scheduling system works on the cluster.  
> 
> [Notes for the instructor here](../guide)
{: .challenge}

The scheduler used in this lesson is PBS Pro.
Although PBS Pro is not used everywhere, 
running jobs is quite similar regardless of what software is being used.
The exact syntax might change, but the concepts remain the same.

## Running a batch job

The most basic use of the scheduler is to run a command non-interactively. Any command (or series of commands) that you want to run on the cluster is 
called a *job*, and the process of using a scheduler to run the job is called *batch job submission*.  

In this case, the job we want to run is just a shell script. Let's create a demo shell script to run as a test.

> ## Creating our test job
> 
> Using your favorite text editor, create the following script and run it.
> Does it run on the compute nodes or the login node?
>
>```
>#!/bin/bash
>
> echo 'This script is running on:'
> hostname
> sleep 120
>```
{: .challenge}

If you completed the previous challenge successfully, 
you probably realize that there is a distinction between 
running the job through the scheduler and just "running it".
To submit this job to the scheduler, we use the ``qsub`` command
(assuming our script is called *example-job.sh*):

``` 
[remote]$ qsub -q <ResID> -A y15 example-job.sh
```
{: .bash}
```
318747.indy2-login0
```
{: .output}

(We have to specify a *budget* to charge our jobs time to; this is what the ``-A y15``
option is for. Your Instructor will tell you if you need to use a different budget code.)

And that's all we need to do to submit a job.  Our work is done -- now the 
scheduler takes over and tries to run the job for us.  While the job is waiting 
to run, it goes into a list of jobs called the *queue*.

To check on our job's status, we check the queue using the command ``qstat``.

```
[remote]$ squeue -u -u yourUsername
```
{: .bash}
```

indy2-login0: 
                                                            Req'd  Req'd   Elap
Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
--------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
319011.indy2-lo user     workq    example-jo  21884   1   1    --  96:00 R 00:00
```
{: .output}

We can see all the details of our job, most importantly that it is in the "R" (Running) state.
Sometimes our jobs might need to wait in a queue, "Q" (Queued) state or have an error.
The best way to check our job's status is with ``qstat``.
Of course, running ``qstat`` repeatedly to check on things can be a little tiresome.
To see a real-time view of our jobs, we can use the ``watch`` command.
``watch`` reruns a given command at 2-second intervals. 
Let's try using it to monitor another job.

```
[remote]$ qsub -q <ResID> -A y15 example-job.sh
[remote]$ watch qstat -u yourUsername
```
{: .bash}

You should see an auto-updating display of your job's status.
When it finishes, it will disappear from the queue.
Press ``Ctrl-C`` when you want to stop the ``watch`` command.

## Output from a job

You may be wondering where the output from your job goes. When you type the `hostname` command
at the terminal the output comes straight back to you, but a job cannot do this as you may not 
even be logged in when the job runs.

By default, each PBS job creates two files based on the job script name; one with `.o` and the
job ID appended and one with `.e` and the job ID appended. For the job we submitted above, with
the script called `example-job.sh` and the job ID `319011`, PBS will create the files:

- example-job.sh.o319011
- example-job.sh.e319011

These files contain the output that would have been printed to the terminal if you used the commands
in the job script interactively rather than in a batch job. The `.o` file contains output
to  *standard out (or stdout)*; this output is usually the output you expect when the command 
ran as expected (e.g. the name of the host from `hostname`). The `.e` file contains output to
*standard error (or stderr)*; this includes any error messages that would have been printed (e.g.
if the `hostname` command could not be found, this error would be in this file).

It is usually a good idea to check the contents of the `.e` file to see if anything went wrong with
your job (although, more often, people actually check the expected output and then only go and 
check for errors if something looks odd!).

## Customizing a job

The job we just ran used all of the scheduler's default options.
In a real-world scenario, that's probably not what we want.
The default options represent a reasonable default.
Chances are, we will need more cores, more memory, more time, 
among other special considerations.
To get access to these resources we must customize our job script.

Comments in Unix (denoted by `#`) are typically ignored.
But there are exceptions.
For instance the special `#!` comment at the beginning of scripts
specifies what program should be used to run it (typically `/bin/bash`).
Schedulers like PBS Pro also have a special comment used to denote special 
scheduler-specific options,
though these comments differ from scheduler to scheduler.
PBS Pro's special comment is `#PBS`.
Anything following the `#PBS` comment is interpreted as an instruction to the scheduler.

Let's illustrate this by example. 
By default, a job's name is the name of the script,
but the `-N` option can be used to change the name of a job.

Submit the following job (`qsub -q <ResID> -A y15 example-job.sh`):

```
#!/bin/bash
#SBATCH -N new_name

echo 'This script is running on:'
hostname
sleep 120
```

```
[remote]$ squeqe -u yourUsername
```
{: .bash}
```
indy2-login0: 
                                                            Req'd  Req'd   Elap
Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
--------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
319011.indy2-lo user     workq    new_name   21884   1   1    --  96:00 R 00:00
```
{: .output}

Fantastic, we've successfully changed the name of our job!

One consequence of changing the name of the job is to change the name of the `.o`
and `.e` files produced by PBS. Now they are the job name appended by `.o`/`.e` and
the job ID rather than the script name. In this case they will be:

- new_name.o319011
- new_name.e319011

> ## Setting up email notifications
> 
> Jobs on an HPC system might run for days or even weeks.
> We probably have better things to do than constantly check on the status of our job
> with `qstat`.
> Looking at the documentation for `qsub` (use `man qsub`, `Space` to scroll down,
> `u` to scroll up, `q` to exit)
> can you set up our test job to send you an email when it finishes?
> 
> Hint: you will need to use the `-m` and `-M` options.
{: .challenge}

### Resource requests

But what about more important changes, such as the number of cores and runtime for our jobs?
One thing that is absolutely critical when working on an HPC system is specifying the 
resources required to run a job.
This allows the scheduler to find the right time and place to schedule our job.
If you do not specify requirements (such as the amount of time you need), 
you will likely be stuck with your site's default resources,
which is probably not what we want.

The following PBS options show how to control resource requests:

- `-l select=<nnodes>:ncpus=<cores per node>` - how many nodes and cores per node does your job need? 
- `-l walltime=<hours:minutes:seconds>` - How much real-world time (walltime) will your job take to run?
- `-l place=scatter:excl` - For parallel jobs, how to distribute the processors across compute nodes
   (required on Cirrus when you run parallel jobs).

Note that just *requesting* these resources does not make your job run faster!  We'll 
talk more about how to make sure that you're using resources effectively in a later 
episode of this lesson.  

> ## Submitting resource requests
>
> Submit a job that will use 2 nodes, 36 cores per node, and 5 minutes of walltime.
{: .challenge}

> ## Job environment variables (compute nodes)
>
> When PBS runs a job, it sets a number of environment variables for the job.
> One of these will let us check which compute nodes have been assigned to our job.
> The `PBS_HOSTFILE` variable is set to the name of the file containing the list of
> compute nodes assigned to our job.
> Using the `PBS_HOSTFILE` variable, 
> modify your job so that it prints the list of compute nodes assigned to our job.
{: .challenge}

> ## Job environment variables (directory)
>
> A key job enviroment variable in PBS is `PBS_O_WORKDIR` that contains the path of
> the directory from which the job was submitted. To understand why this is important
> modify your job submission script to print out the directory that it runs in by 
> default by using the `pwd` command (this prints the current working directory).
>
> Now, use the `PBS_O_WORKDIR` environment within your job script to make sure that 
> the commands you are using run in the directory that you submitted the job from
> and verify that this has worked using `pwd` again.
{: .challenge}

You almost always want your job scipt to execute as if it was in the directory from
which the job was submitted so you will generally make sure you use the `PBS_O_WORKDIR`
environment variable in all your job scripts. If you encounter errors with files or
executables not being found it is often worth checking that the job script is executing
in the location you expect!

Resource requests are typically binding. If you exceed them, your job will be killed.
Let's use walltime as an example.  We will request 30 seconds of walltime, 
and attempt to run a job for two minutes.

```
#!/bin/bash
#PBS -N timeout
#PBS -l walltime=0:0:30

echo 'This script is running on:'
hostname
sleep 120
```

Submit the job and wait for it to finish. 
Once it is has finished, check the `.e` (stderr) file.
```
[remote]$ qsub -q <ResID> -A y15 timeout.sh
[remote]$ watch qstat -u yourUsername
[remote]$ cat timeout.e319076 
```
{: .bash}
```
=>> PBS: job killed: walltime 49 exceeded limit 30
```
{: .output}

Our job was killed for exceeding the amount of resources it requested.
Although this appears harsh, this is actually a feature.
Strict adherence to resource requests allows the scheduler to find the best possible place
for your jobs.
Even more importantly, 
it ensures that another user cannot use more resources than they've been given.
If another user messes up and accidentally attempts to use more time than they have been 
allocated PBS will kill the job. Other jobs will be unaffected.
This means that one user cannot mess up the experience of others,
the only jobs affected by a mistake in scheduling will be their own.

## Cancelling/deleting a job

Sometimes we'll make a mistake and need to cancel/delete a job.
This can be done with the `qdel` command.
Let's submit a job and then cancel it using its job number.

```
[remote]$ qsub -q <ResID> -A y15 example-job.sh
[remote]$ qstat -u yourUsername
```
{: .bash}
```
319078.indy2-login0

indy2-login0: 
                                                            Req'd  Req'd   Elap
Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
--------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
319078.indy2-lo user     workq    example-jo   3567   1   1    --  96:00 R 00:00
```
{: .output}

Now cancel the job with it's job number. 
Absence of any job info indicates that the job has been successfully canceled.

```
[remote]$ qdel 319078
[remote]$ qstat -u yourUsername
```
{: .bash}
```
[No output as there are no jobs]
```
{: .output}

## Other types of jobs

Up to this point, we've focused on running jobs in batch mode.
PBS also provides the ability to run tasks as a one-off or start an interactive session.

There are very frequently tasks that need to be done semi-interactively.
Creating an entire job script might be overkill, 
but the amount of resources required is too much for a login node to handle.
A good example of this might be building a genome index for alignment with a tool like [HISAT2](https://ccb.jhu.edu/software/hisat2/index.shtml).
Fortunately, we can run these types of tasks as a one-off with `qsub --`.

`qsub --` runs a single command on the compute nodes and then exits.
Let's demonstrate this by running the `hostname` command with `qsub --`.
(We can delete a `qsub --` job using `qdel` as described above.)

```
[remote]$ qsub -q <ResID> -A y15 -- /bin/hostname
[remote]$ cat STDIN.o319085
```
{: .bash}
```
319085.indy2-login0

r1i3n0
```
{: .output}

Note that, unlike when we use it on the interactive command line, we had
to provide the full path to the command: `/bin/hostname` rather than `hostname`.
This is because the PBS environment inside this job does not contain the information
to find the command. You can find the full path of a command by using the `which` 
command:

```
[remote]$ which hostname
```
{: .bash}
```
/bin/hostname
```
{: .output}

`qsub --` accepts all of the same options as `qsub`.
However, instead of specifying these in a script, 
these options are specified on the command-line when starting a job.
To submit a job that uses 2 nodes (36 cores per node) for instance, 
we could use the following command

```
[remote]$ qsub -l select=2:ncpus=36 -l place=scatter:excl -q <ResID> -A y15 -- /bin/echo "This job will use 2 nodes."
```
{: .bash}
```
This job will use 2 nodes.
```
{: .output}

### Interactive jobs

Sometimes, you will need a lot of resource for interactive use.
Perhaps it's our first time running an analysis 
or we are attempting to debug something that went wrong with a previous job.
Fortunately, we can start an interactive job with `qsub`:

```
[remote]$ qrun -I -q <ResID> -A y15
```
{: .bash}
```
qsub: waiting for job 319086.indy2-login0 to start
qsub: job 319086.indy2-login0 ready

[compute]$ 
```
{: .output}

You should be presented with a bash prompt.
Note that the prompt will likely change to reflect your new location, 
in this case the compute node we are logged onto.
You can also verify this with `hostname`.

When you are done with the interactive job, type `exit` to quit your session.


## User Status on Slurm

* scontrol show partition

```
scontrol show partition
```
{: .bash}
* Display the accounts created:
```
sacctmgr show account
sacctmgr show account -s   # Show also associations in the accounts
```
{: .bash}
* List users by:

```
sacctmgr show user
sacctmgr show user -s
```
{: .bash}
* Display all Association records by:
```
sacctmgr show associations
```







