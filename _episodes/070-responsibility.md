---
title: "Using shared resources responsibly"
teaching: 15
exercises: 5
questions:
- "How can I be a responsible user?"
- "How can I protect my data?"
- "How can I best get large amounts of data off an HPC system?"
objectives:
- "Learn how to be a considerate shared system citizen."
- "Understand how to protect your critical data."
- "Appreciate the challenges with transferring large amounts of data off HPC systems."
- "Understand how to conver many files to a single archive file using tar."
keypoints:
- "Be careful how you use the login node."
- "Your data on the system is your responsibility."
- "Plan and test large data transfers."
- "It is often best to convert many files to a single archive file before trasferring."
---

One of the major differences between using remote HPC resources and your own system 
(e.g. your laptop) is that they are a shared resource. How many users the resource is
shared between at any one time varies from system to system but it is unlikely you
will ever be the only user logged into or using such a system.

We have already mentioned one of the consequences of this shared nature of the resources:
the scheduling system where you submit your jobs, but there are other things you need 
to consider in order to be a considerate HPC citizen, to protect your critical data
and to transfer data 

## Be kind to the login nodes

The login node is often very busy managing lots of users logged in, creating and editing files
and compiling software! It doesn’t have any extra space to run computational work.

Don’t run jobs on the login node (though quick tests are generally fine). A “quick test” is
generally anything that uses less than 4GB of memory, 4 CPUs, and 10 minutes of time. If you
use too much resource then other users on the login node will start to be affected - their
login sessions will start to run slowly and may even freeze or hang. 

> ## Login nodes are a shared resource
>
> Remember, the login node is shared with all other users and your actions could cause
> issues for other people. Think carefully about the potential implications of issuing
> commands that may use large amounts of resource.
>
{: .callout}

You can always use the command `ps ux` to list the processes you are running on a login
node and the amount of CPU and memory they are using. The `kill` command can be used along
with the *PID* to terminate any processes that are using large amounts of resource.

```
[remote]$ module load python/anaconda3
[remote]$ python cfd.py 100 10000 &
[remote]$ ps ux
```
{: .bash}
```
[1] 61091

USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
user     56164  0.0  0.0 142392  2136 ?        S    14:31   0:00 sshd: user@pts/84
user     56165  0.1  0.0 114640  3296 pts/84   Ss   14:31   0:00 -bash
user     61091 87.5  0.1 504388 381364 pts/84  R    14:32   0:03 python cfd.py 100 10000
user     61497  5.0  0.0 149144  1800 pts/84   R+   14:32   0:00 ps ux
user     67737  0.0  0.0 142392  2144 ?        S    12:29   0:00 sshd: user@pts/55
user     67738  0.0  0.0 114540  3096 pts/55   Ss+  12:29   0:00 -bash
```
{: .output}

The python command with PID 61091 is using a large amount of CPU (87.5%) so we probably
should kill it:

```
[remote]$ kill 61091
[remote]$ ps ux
```
{: .bash}
```
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
user     56164  0.0  0.0 142392  2136 ?        S    14:31   0:00 sshd: user@pts/84
user     56165  0.1  0.0 114640  3296 pts/84   Ss   14:31   0:00 -bash
user     62908  0.0  0.0 149144  1800 pts/84   R+   14:32   0:00 ps ux
user     67737  0.0  0.0 142392  2144 ?        S    12:29   0:00 sshd: user@pts/55
user     67738  0.0  0.0 114540  3096 pts/55   Ss+  12:29   0:00 -bash
[1]+  Terminated              python cfd.py 100 10000
```
{: .output}


> ## Login Node Etiquette
> 
> Which of these commands would probably be okay to run on the login node?
> python physics_sim.py
> make
> create_directories.sh
> molecular_dynamics_2
> tar -xzf R-3.3.0.tar.gz
> 
{: .challenge}

If you experience performance issues with a login node you should report it to the system
staff (usually via the helpdesk) for them to investigate. You can use the `top` command
to see which users are using which resources.

## Test before scaling

Remember that you are generally charged for usage on shared systems. A simple mistake in a 
job script can end up costing a large amount of resource budget. Imagine a job script with 
a mistake that makes it sit doing nothing for 24 hours on 1000 cores or one where you have
requested 2000 cores by mistake and only use 100 of them! This problem can be compounded 
when people write scripts that automate job submission (for example, when running the same
calculation or analysis over lots of different input).  When this happens it hurts both you
(as you waste lots of charged resource) and other users (who are blocked from accessing the
idle compute nodes).

On very busy resources you may wait many days in a queue for your job to fail within 10 seconds
of starting due to a trivial typo in the job script. This is extremely frustrating! Most
systems provide small, short queues for testing that have short wait times to help you 
avoid this issue.

> ## Test job submission scripts that use large amounts of resource
> Before submitting a large run of jobs, submit one as a test first to make sure everything works
> as expected.
>
> Before submitting a very large or very long job submit a short trunctated test to ensure that
> the job starts as expected
{: .callout}

## Have a backup plan

Although many HPC systems keep backups, it does not always cover all the file systems available
and may only be for disaster recovery purposes (*i.e.* for restoring the whole file system if lost
rather than an individual file or directory you have deleted by mistake). Your data on the
system is primarily your responsibility and you should ensure you have secure copies of data
that are critical to your work.

Version control systems (such as Git) often have free, cloud-based offerings (e.g. Github, Gitlab)
that are generally used for storing source code. Even if you are not writing your own 
programs, these can be very useful for storing job scripts, analysis scripts and small
input files. 

For larger amounts of data, you should make sure you have a robust system in place for taking
copies of critical data off the HPC system wherever possible to backed-up storage. Tools such
as `rsync` can be very useful for this.

Your access to the shared HPC system will generally be time-limited so you should ensure you have a
plan for transferring your data off the system before your access finishes. The time required to
transfer large amounts of data should not be underestimated and you should ensure you have planned
for this early enough (ideally, before you even start using the system for your research).

In all these cases, the helpdesk of the system you are using shoud be able to provide useful
guidance on your options for data transfer for the volumes of data you will be using.

> ## Your data is your responsibility
> Make sure you understand what the backup policy is on the file systems on the system you are
> using and what implications this has for your work if you lose your data on the system. Plan
> your backups of critical data and how you will transfer data off the system throughout the
> project.
{: .callout}

## Transferring data

As mentioned above, many users run into the challenge of transferring large amounts of data 
off HPC systems at some point (this is more often in transferring data off than onto systems
but the advice below applies in either case). Data transfer speed may be limited by many
different factors so the best data transfer mechanism to use depends on the type of data being
transferred and where the data is going. Some of the key issues to be aware of are:

- **Disk speed** - File systems on HPC systems are often highly parallel, consisting of a very
  large number of high performance disk drives. This allows them to support a very high data
  bandwidth. Unless the remote system has a similar parallel file system you may find your
  transfer speed limited by disk performance at that end.
- **Meta-data performance** - *Meta-data operations* such as opening and closing files or
  listing the owner or size of a file are much less parallel than read/write operations. If
  your data consists of a very large number of small files you may find your transfer speed is
  limited by meta-data operations. Meta-data operations performed by other users of the system
  can also interact strongly with those you perform so reducing the number of such operations
  you use (by combining multiple files into a single file) may reduce variability in your transfer
  rates and increase transfer speeds.
- **Network speed** - Data transfer performance can be limited by network speed. More importantly
  it is limited by the slowest section of the network between source and destination. If you are
  transferring to your laptop/workstation, this is likely to be its connection (either via LAN or 
  wifi).
- **Firewall speed** - Most modern networks are protected by some form of firewall that filters
  out malicious traffic. This filtering has some overhead and can result in a reduction in data
  transfer performance. The needs of a general purpose network that hosts email/web-servers and
  desktop machines are quite different from a research network that needs to support high volume
  data transfers. If you are trying to transfer data to or from a host on a general purpose
  network you may find the firewall for that network will limit the transfer rate you can achieve.

As mentioned above, if you have related data that consists of a large number of small files it
is strongly recommended to pack the files into a larger *archive* file for long term storage and
transfer. A single large file makes more efficient use of the file system and is easier to move,
copy and transfer because significantly fewer meta-data operations are required. Archive files can
be created using tools like `tar`, `cpio` and `zip`. We are going to look at `tar` as it is the 
most commonly used.

The `tar` command packs files into a "Tape ARchive" format intended for backup purposes. To create
a compressed archive file from a directory you can use:

```
[remote]$ tar -cvWlf mydata.tar mydata
[remote]$ gzip mydata.tar
```
{: .bash}
```
mydata/
mydata/output00063.out
mydata/output00066.out
mydata/output00051.out
mydata/output00002.out
mydata/output00067.out
mydata/output00046.out
mydata/output00041.out
mydata/output00054.out

[long output trimmed]

Verify mydata/
Verify mydata/output00063.out
Verify mydata/output00066.out
Verify mydata/output00051.out
Verify mydata/output00002.out
Verify mydata/output00067.out
Verify mydata/output00046.out
Verify mydata/output00041.out
Verify mydata/output00054.out

[long output trimmed]

```
{: .output}

(In bash, rather than include all options with their own `-` indicator, we can string them together so
the above tar command is equivalent to `tar -c -v -W -f mydata.tar mydata`.) The second step 
*compresses* the archive to reduce its size.

The options we used for `tar` are:

- `-c` - Create new archive
- `-v` - Verbose (print what you are doing!)
- `-W` - Verify integrity of created archive
- `-f mydata.tar` - Create the archive in file *mydata.tar*

To extract files from a tar file, the option `-x` is used. If the tar file has also been compressed using
`gzip` (as we did above) `tar` will automatically uncompress the archive. For example:

```
[local]$ tar -xvf mydata.tar.gz
```
{: .bash}
```
mydata/
mydata/output00063.out
mydata/output00066.out
mydata/output00051.out
mydata/output00002.out
mydata/output00067.out
mydata/output00046.out
mydata/output00041.out
mydata/output00054.out

[long output trimmed]

```
{: .output}

> ## Consider the best way to transfer data
> If you are transferring large amounts of data you will need to think about what may affect your transfer
> performance. It is always useful to run some tests that you can use to extrapolate how long it will
> take to transfer your data.
>
> If you have many files, it is best to combine them into an archive file before you transfer them using a
> tool such as `tar`.
{: .callout}

