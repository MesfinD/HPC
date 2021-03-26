---
layout: page
title: "Instructor Notes"
permalink: /guide/
---

## Cluster roleplay instructions (from 04-scheduler)

To do this exercise, you will need about 50-100 pieces of paper or sticky notes.  

1. Divide the room into groups, with specific roles. 
 * Pick three-four people to be the "scheduler"
 * Have the remaining one-third of the room be "users", given several slips of 
 paper (or post-it notes) and pens
 * Have the remaining two thirds of the room be "compute nodes"
Make sure everyone knows what their roles are.  Have the "users" 
	go to the front of the room (or the back, wherever there's space 
	for them to stand) and the "schedulers" stand between the users 
	and "compute nodes" (who should remain at their seats).  

2. Divide the pieces of paper / sticky notes among the "users" and have them 
fill out all the pages with simple math problems and their name.  Tell everyone that these 
are the jobs that need to be done and correspond to their computing research problems.  

3. Point out that we now have jobs and we have "compute nodes" (the people still sitting 
down) that can solve these problems.  How are the jobs going to get to the nodes?  
The answer is the scheduling program that will take the jobs from the users and deliver 
them to open compute nodes.  

4. Have all the "compute nodes" raise their hands.  Have the users "submit" their 
jobs by handing them to the schedulers.  Schedulers 
should then deliver them to "open" (hands-raised) compute nodes and collect 
finished problems and return them to the appropriate user.  

5. Wait until most of the problems are done and then re-seat everyone.  

6. Follow-up discussion: what would happen if a node couldn't solve the math problem?  It 
might be important to indicate the *resources* that your job needs to run.  Add other 
parallels that will be coming up in the next section of the lesson.  









## Job controller Status on Slurm

To check the status of slurm job control 

```
# scontrol ping
```
If the primary or the backup slurm controll is down, we need to start the slurmctld

```
# systemctl status slurmctld.service
```

## User Status on Slurm
scontrol show partition


```
$ scontrol show partition
```
{: .bash}

```
PartitionName=debug
   AllowGroups=ALL AllowAccounts=ALL AllowQos=ALL
   AllocNodes=ALL Default=YES QoS=N/A
   DefaultTime=NONE DisableRootJobs=NO ExclusiveUser=NO GraceTime=0 Hidden=NO
   MaxNodes=UNLIMITED MaxTime=UNLIMITED MinNodes=0 LLN=NO MaxCPUsPerNode=UNLIMITED
   Nodes=compute1,compute2,compute3,compute4,compute5,compute6,compute7,compute8,compute9,compute10,compute11,compute12,compute13,compute14,compute15,compute16
   PriorityJobFactor=1 PriorityTier=1 RootOnly=NO ReqResv=NO OverSubscribe=NO
   OverTimeLimit=NONE PreemptMode=OFF
   State=UP TotalCPUs=640 TotalNodes=16 SelectTypeParameters=NONE
   JobDefaults=(null)
   DefMemPerNode=UNLIMITED MaxMemPerNode=UNLIMITED
```
{: .output}

Display the accounts created:


```
$ sacctmgr show account
$ sacctmgr show account -s   # Show also associations in the accounts
```
{: .bash}

```
   abaineh              abaineh              abaineh 
    abebaw               abebaw               abebaw 
 abebayehu            abebayehu            abebayehu 
     abebe                abebe                abebe 
```
{: .output}



List users by:

```
$ sacctmgr show user
$ sacctmgr show user -s
```
{: .bash}

```
 User   Def Acct     Admin    Cluster    Account  Partition     Share MaxJobs MaxNodes  MaxCPUs MaxSubmit     MaxWall  MaxCPUMins                  QOS   Def QOS 
---------- ---------- --------- ---------- ---------- ---------- --------- ------- -------- -------- --------- ----------- ----------- -------------------- --------- 
   abaineh    abaineh      None      chess    abaineh                    1                                                                       normal   normal 
```
{: .output}

Display all Association records by:


```
$ sacctmgr show associations
```
{: .bash}

```
   Cluster    Account       User  Partition     Share GrpJobs       GrpTRES GrpSubmit     GrpWall   GrpTRESMins MaxJobs       MaxTRES MaxTRESPerNode MaxSubmit     MaxWall   MaxTRESMins                  QOS   Def QOS GrpTRESRunMin 
---------- ---------- ---------- ---------- --------- ------- ------------- --------- ----------- ------------- ------- ------------- -------------- --------- ----------- ------------- -------------------- --------- ------------- 
     chess       root                               1                                                                                                                                                  normal                         
     chess       root       root                    1                                                                                                                                                  normal                         
     chess    abaineh                               1                                                                                                                                                  normal                         
     chess    abaineh    abaineh                    1                                                                                                                                                  normal    normal               
     chess     abebaw                               1                                                                                                                                                  normal                         
     chess     abebaw     abebaw                    1                                                                                                                                                  normal                         
     chess  abebayehu                               1                                                                                                                                                  normal                         
     chess  abebayehu  abebayehu                    1                                                                                                                                                  normal    normal               
     chess      abebe                               1                                                                                                                                                  normal                         
     chess      abebe      abebe                    1                                                                                                                                                  normal    normal               
     chess     abebe2                               1                                                                                                                                                  normal                         
     chess     abebe2     abebe2                    1                                                                                                                                                  normal    normal               
     chess     abebee                               1                                                                                                                                                  normal                         
     chess    abraham                               1                                                                                                                                                  normal                         
     chess    abraham    abraham                    1                                                                                                                                                  normal    normal               
     chess      abrha                               1                                                                                                                                                  normal                         
     chess      abrha      abrha                    1                                                                                                                                                  normal                         
     chess     abrham                               1                                                                                                                                                  normal                         
     chess     abrham     abrham                    1                                                                                                                                                  normal    normal               
     chess      adane                               1                                                                                                                                                  normal            
    
```
{: .output}

Display current accounts on the slurm

` $ saccmgr show account`

{: .bash}

Create a hierarchical organization list using

` # sacctmgr add account username Descr="username" Org=username`

{: .bash}



Create user named username with a default account(required) yyy:

` # sacctmgr add user name=username Account=username`

{: .bash}

Synchronize the slurm user

`scontrol reconfig`
{: .bash}

Show the Slurm entity (e.g., accounts) problems:

` # saccmgr show problem`

{: .bash}

We can modify the database items using SQL-like where and set, for example:

` # sacctmgr modify account where  name=username set cluster=chess `

{: .bash}

We can delete user account from slurm job controller by:

`sacctmgr remove user Username where account=Username/account`
