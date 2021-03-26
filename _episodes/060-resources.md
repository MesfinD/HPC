---
title: "Using resources effectively"
teaching: 20
exercises: 10
questions:
- "How do we monitor our jobs?"
- "How can I get my jobs scheduled more easily and improve throughput?" 
objectives:
- "Understand how to look up job statistics."
- "Understand job size implications."
keypoints:
- "The better your resource request estimates, the better throughput you will see."
---

We now know most of the basic mechanics of getting research up and running on an HPC system.
We can log on, submit different types of jobs, use preinstalled software, 
and install and use software of our own.
What we need to do now is understand how we can use the systems effectively.

## Estimating required resources using the scheduler

Although we covered requesting resources from the scheduler earlier,
how do we know how much and what type of resources we will need in the first place?

Answer: we don't. 
Not until we've tried it ourselves at least once.
We'll need to benchmark our job and experiment with it before
we know how much it needs in the way of resources.

The most effective way of figuring out how much resources a job needs is to submit a test job,
and then ask the scheduler how many resources it used.
A good rule of thumb is to ask the scheduler for more time than your job can use.
This value is typically two to three times what you think your job will need.

> ## Resources for Computational Fluid Dynamics (CFD)
>
> Copy the Python 2D CFD application from the course website to the HPC system using the following command:
>
> ```
> [remote]$ wget https://epcced.github.io/hpc-intro/files/cfd.tar.gz
> ```
>
> Then unpack it using
>
> ```
> [remote]$ tar -xvf cfd.tar.gz
> ```
> 
> (`tar` is a bit like zip, it allows you to create/expand an archive file from multiple files. We will introduce it in a
> bit more detail in a future episode.)
>
> Create a job that runs the following commands in the directory containing the `cfd.py` program.
> 
> ```
> module load anaconda/python2
> python cfd.py 3 20000
> ```
> 
> You'll need to figure out a good amount of resources to ask for for this first "test run".
> You might also want to have the scheduler email you to tell you when the job is done.
>
> Hint: the job only needs 1 cpu and not too much time.
> The trick is figuring out just how much you'll need!
>
> Do not forget to check the `.e` file produced by the job to make sure there are no errors!
> You should also check the `.o` file produced by the job to make sure it contains the output
> from the CFD program.
{: .challenge}

Once the job completes, we can query the scheduler to see how long our job took.
We will use `qstat -x` to get statistics about our job.

By itself, `qstat -x -u yourUsername` shows all jobs that you have run on the system recently

```
[remote]$ qstat -x -u yourUsername
```
{: .bash}
```

indy2-login0: 
                                                            Req'd  Req'd   Elap
Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
--------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
324396.indy2-lo user     workq    test1       57348   1   1    --  00:01 F 00:00
324397.indy2-lo user     workq    test2       57456   1   1    --  00:01 F 00:01
324401.indy2-lo user     workq    test3       58159   1   1    --  00:00 F 00:00
324410.indy2-lo user     workq    test4       34027   1   1    --  00:05 F 00:05
324418.indy2-lo user     workq    test5       35243   1   1    --  00:05 F 00:01
```
{: .output}

Comparing the `Req'd Time` and `Elap Time` columns allows you to see if a particular 
job completed within the requested time. If the two values are the same then this 
usually means that the job hit the limit of specified walltime resources rather than
completing successfully (you will see a message in the `.e` file if you have hit the 
walltime limit). If the elapsed time is less than the requested time then 
your job completed (either successfully or not!) within the requested time. In this case,
you will need to check the output files from the job to understand if it ran successfully
or not.

## Measuring the statistics of currently running tasks

As we saw in the previous episode on the scheduler, you can use the `sstat` command to 
monitor how much time and how many nodes are being used by current jobs. To list
details of your jobs (queued and running), use `sstat -u yourUsernname` and to list the
details of all jobs in the queue use `sstat -a`.

## Benchmarking

The process above is used so that you have a good handle on the resources you need to 
request for your jobs and corresponds to the absolute minimum amount of *benchmarking* 
a user on a shared HPC system will need to do. However, you will often want to do more
than this minimum amount of benchmarking and we will discuss how to approach this
in one of the later episodes in this lesson.
