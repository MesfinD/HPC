---
title: "Connecting to the HPC system"
teaching: 10 
exercises: 5
questions:
- "How do I open a terminal?"
- "How do I connect to a remote computer?"
objectives:
- "Succesfully connect to a remote HPC system."
keypoints:
- "To connect to a remote HPC system using SSH: `ssh yourUsername@remote.computer.address`"
---

## Opening a Terminal

Connecting to an HPC system is most often done through a tool known as "SSH"
(Secure SHell) and usually SSH is run through a terminal. So, to begin using
an HPC system we need to begin by opening a terminal. Different operating
systems have different terminals, none of which are exactly the same in terms
of their features and abilities while working on the operating system. When
connected to the remote system the experience between terminals will be
identical as each will faithfully present the same experience of using that
system.

Here is the process for opening a terminal in each operating system.

### Linux

There are many different versions (aka "flavours") of Linux and how to open a
terminal window can change between flavours. Fortunately most Linux users
already know how to open a terminal window since it is a common part of the
workflow for Linux users. If this is something that you do not know how to do
then a quick search on the Internet for "how to open a terminal window in"
with your particular Linux flavour appended to the end should quickly give
you the directions you need.

A very popular version of Linux is Ubuntu. There are many ways to open a
terminal window in Ubuntu but a very fast way is to use the terminal shortcut
key sequence: Ctrl+Alt+T.

### Mac

Macs have had a terminal built in since the first version of macOS (OSX) as it is
built on a Linux flavour known as BSD (Berkeley Systems Designs). 
The terminal can be quickly opened through the use of the Spotlight tool. 
Hold down the command key and press the spacebar. 
In the search bar that shows up type "terminal", choose the terminal app from the list of results (it will
look like a tiny, black computer screen) and you will be presented with a terminal window.
Alternatively, you can find Terminal under "Utilities" in the Applications menu.

### Windows

While Windows does have a command-line interface known as the "Command
Prompt" that has its roots in MS-DOS (Microsoft Disk Operating System) it
does not have an SSH tool built into it and so one needs to be installed.
There are a variety of programs that can be used for this, two common ones we
describe here, as follows:

#### MobaXterm

MobaXterm is a terminal window emulator for Windows and the home edition can
be downloaded for free from
[mobatek.net](https://mobaxterm.mobatek.net/download-home-edition.html). If
you follow the link you will note that there are two editions of the home
version available: Portable and Installer. The portable edition puts all
MobaXterm content in a folder on the desktop (or anywhere else you would like
it) so that it is easy add plug-ins or remove the software. The installer
edition adds MobaXterm to your Windows installation as any other program you
might install. If you are not sure that you will continue to use MobaXterm in
the future you are likely best to choose the portable edition.

Download the version that you would like to use and install it as you would
any other software on your Windows installation. Once the software is
installed you can run it by either opening the folder installed with the
portable edition and double-clicking on the file named
*MobaXterm_Personal_10.2* or, if the installer edition was used, finding the
executable through either the start menu or the Windows search option.

Once the MobaXterm window is open you should see a large button in the middle
of that window with the text "Start Local Terminal". Click this button and
you will have a terminal window at your disposal.

#### PuTTY

It is strictly speaking not necessary to have a terminal running on your local
computer in order to access and use a remote system, only a window into the remote system once connected.  PuTTy is likely the oldest, most well-known, and widely used software solution to take this approach.

PuTTY is available for free download from [www.putty.org](http://www.putty.org/).  Download the version that is correct for your operating system and install it as you would other software on you Windows system.  Once installed it will be available through the start menu or similar.

Running PuTTY will not initially produce a terminal but intsead a window full of connection options.  Putting the address of the remote system in the "Host Name (or IP Address)" box and either pressing enter or clicking the "Open" button should begin the connection process.

If this works you will see a terminal window open that prompts you for a username through the "login as:" prompt and then for a password.  If both of these are passed correctly then you will be given access to the system and will see a message saying so within the terminal.  If you need to escape the authentication process you can hold the control/ctrl key and press the c key to exit and start again.

Note that you may want to paste in your password rather than typing it.  Use control/ctrl plus a right-click of the mouse to paste content from the clipboard to the PuTTY terminal.

For those logging in with PuTTY it would likely be best to cover the terminal basics already mentioned above before moving on to navigating the remote system.

## Logging onto the system

With all of this in mind, let's connect to a cluster. 

For this lesson, we will use [Ethernet]() - an HPC system located at [Ethernet](http://www.hpc.ethernet.edu.et).
Although it's unlikely that every system will be exactly like Entoto, it's a good example of what you can expect from an HPC system.
To connect to our example computer, we will use SSH (if you are using PuTTY, see above for instructions). 

SSH allows us to connect to Linux computers remotely, and use them as if they were our own.
The general syntax of the connection command follows the format `ssh yourUsername@some.computer.address`
Let's attempt to connect to the HPC system now:

```
ssh yourUsername@hpcportal.ethernet.edu.et
```
{: .bash}

Your Instructor will give you the correct username to use in place of "yourUsername".

```{.output}
The authenticity of host '[mgmt01 ~](hpcportal.ethernet.edu.et)' can't be established.
ECDSA key fingerprint is SHA256:JRj286Pkqh6aeO5zx1QUkS8un5fpcapmezusceSGhok.
ECDSA key fingerprint is MD5:99:59:db:b1:3f:18:d0:2c:49:4e:c2:74:86:ac:f7:c6.
Are you sure you want to continue connecting (yes/no)? # type "yes"!
Warning: Permanently added the ECDSA host key for IP address '129.215.175.28' to the list of known hosts.
yourUsername@hpc.ethernet.edu.et password:  # no text appears as you enter your password
Last login: Mon Jun 18 16:21:52 2018 from cpc102380-sgyl38-2-0-cust601.18-2.cable.virginm.net
================================================================================



================================================================================
```

If you've connected successfully, you should see a prompt like the one below. 
This prompt is informative, and lets you grasp certain information at a glance:
in this case `[yourUsername@computerName workingDirectory]$`.
(If you don't understand what these things are, don't worry! 
We will cover things in depth as we explore the system further.)

```{.output}
[yourUsername@mgmt01 ~]$
```

## Telling the Difference between the Local Terminal and the Remote Terminal

You may have noticed that the prompt changed when you logged into the remote
system using the terminal (if you logged in using PuTTY this will not apply
because it does not offer a local terminal). This change is important because
it makes it clear on which system the commands you type will be run when you
pass them into the terminal. This change is also a small complication that we
will need to navigate throughout the workshop. Exactly what is reported
before the `$` in the terminal when it is connected to the local system and
the remote system will typically be different for every user. We still need
to indicate which system we are entering commands on though so we will adopt
the following convention:

`[local]$` when the command is to be entered on a terminal connected to your local computer

`[remote]$` when the command is to be entered on a terminal connected to the remote system

`$` when it really doesn't matter which system the terminal is connected to.

> ## Being Certain Which System your Terminal is connected to
> If you ever need to be certain which system a terminal you are using is connected to 
> then use the follwing command: `$ hostname`.
{: .callout}

> ## Keep Two Terminal Windows Open
> It is strongly recommended that you have two terminals open, one connected
> to the local system and one connected to the remote system, that you can
> switch back and forth between. If you only use one terminal window then you
> will need to reconnect to the remote system using one of the methods above
> when you see a change from `[local]$` to `[remote]$` and disconnect when you
> see the reverse.
{: .callout}

## Examining the nodes

Now we can log into the Entoto HPC system we will look at the nodes. You should remember that there 
are at least two  types of node on the system: *login nodes* and *compute nodes*.

We can use the `lscpu` command to print information on the processors on the login nodes to the terminal:

```
[remote]$ lscpu
```
{: .bash}
```
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                16
On-line CPU(s) list:   0-15
Thread(s) per core:    1
Core(s) per socket:    8
Socket(s):             2
NUMA node(s):          2
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 79
Model name:            Intel(R) Xeon(R) CPU E5-2620 v4 @ 2.10GHz
Stepping:              1
CPU MHz:               2100.000
CPU max MHz:           2100.0000
CPU min MHz:           1200.0000
BogoMIPS:              4190.22
Virtualization:        VT-x
L1d cache:             32K
L1i cache:             32K
L2 cache:              256K
L3 cache:              20480K
NUMA node0 CPU(s):     0-7
NUMA node1 CPU(s):     8-15
```
{: .output}

We can get some useful information from this output:

* The processor model is:   Intel(R) Xeon(R) CPU E5-2620 v4 @ 2.10GHz (you could use [Intel Ark](http://ark.intel.com) along with the model number to get more information if you wanted)
* There are 18 cores per processor: ``Core(s) per socket: 18``
* There are two processors per node: ``Sockets: 2``
* This means that there are 2 * 18 = 36 cores on the node

We can extract information about the amount of memory available from the `/proc/meminfo` file. In this case we only want the first line as it will give
us the total amount of memory available so we use the ``head -1`` command:

```
[remote]$ head -1 /proc/meminfo
```
{: .bash}
```
MemTotal:       131456088 kB
```
{: .output}

This tells us that there are approximately 125 GB of memory available (131456088/[1024*1024] = 125.37 GB) (this is out of 125 GB, ~4GB are reserved for various parts of computing hardware).

> ## Units
> 
> A computer's memory and disk are measured in units called *bytes*.  The magnitude 
> of a file or memory use is measured using the same prefixes of the metric system: 
> kilo, mega, giga, tera.  So 1000 bytes is a kilobyte (kB), 1000 kilobytes is a megabyte (MB), 
> and so on.  
>
> You may also see units that use multiples of 1024 rather than 1000. So, 1024 bytes is a 
> kibibyte (KiB), 1024 KiB is a mebibyte (MiB), 1024 MiB is a gibibyte (GiB) and so on.
>
> For small amounts of storage the differences between these two unit systems are negligible but as the
> size increases the differences can be significant.
{: .callout}

We can now repeat this for the compute nodes. All interaction with the compute nodes is handled by a specialized piece of software called a scheduler
(the scheduler used in this lesson is called PBS Pro).  We will learn more about how to use 
the scheduler to submit jobs later, but for now, we will use it to tell us more information about 
the compute nodes and what is available.  

We are going to repeat the commands above on a compute node. To do this, we will run an *interactive job* which will give
us access to a bash command line on a compute node. The ``qrun`` command is used to submit a job to the scheduler. Start a best-effort interactive job (can be interrupted by regular jobs if other users submit them):

```
[remote]$ srun -p interactive --partition=debug --nodelist=compute16 --pty bash -i
```
{: .bash}
```

[mesfind@compute16 ~]$ 
```
{: .output}

You should see your prompt change to indicate that your shell is now on a compute node rather than a login node.

> ## Differences on the compute node?
> Use the commands you saw above to determine if there are any differences between the processors and
> memory available on the compute node compared to the login node.
>
> Is the answer what you expected?
{: .challenge}

> ## Explore your local computer
>
> Try to find out the processor and memory details on your own computer. How do these differ from the 
> hardware available on the HPC system?
{: .challenge}

Now we know how to connect to the HPC system, we will next learn how to transfer data on and off the system.

