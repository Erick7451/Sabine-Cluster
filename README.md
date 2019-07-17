# Sabine-Cluster
What to know before working with UH Cluster

At first, working with a cluster can be a little bit overwhelming if it is your first time. However, once you break down its infrastructure, logic, and scheduling, it becomes one of the greatest tools for a Data Scientist

Today, we will explore the Sabine cluster to get a better understanding of what a Cluster is about.

First, we will use the command **sinfo** to get more information about the partitions of the cluster

```linux
[eeplater@sabine ~]$ sinfo
PARTITION    AVAIL  TIMELIMIT  NODES  STATE NODELIST
batch*          up 14-00:00:0      1 drain* compute-1-23
batch*          up 14-00:00:0     19    mix compute-0-[1,5,12-13,19,29-30,33],compute-1-[2-5,12],compute-2-[1,17,29,38-39,47]
batch*          up 14-00:00:0     86  alloc compute-0-[0,2-4,6-11,14-18,20-28,31-32,34-35],compute-1-[0-1,6-11,13-22,24-31],compute-2-[0,2-5,10-16,20-23,28,30-37,40-46]
batch*          up 14-00:00:0     10   idle compute-2-[6-9,18-19,24-27]
short           up    4:00:00      1 drain* compute-1-23
short           up    4:00:00     19    mix compute-0-[1,5,12-13,19,29-30,33],compute-1-[2-5,12],compute-2-[1,17,29,38-39,47]
short           up    4:00:00     86  alloc compute-0-[0,2-4,6-11,14-18,20-28,31-32,34-35],compute-1-[0-1,6-11,13-22,24-31],compute-2-[0,2-5,10-16,20-23,28,30-37,40-46]
short           up    4:00:00     10   idle compute-2-[6-9,18-19,24-27]
medium          up 7-00:00:00      1 drain* compute-1-23
medium          up 7-00:00:00     19    mix compute-0-[1,5,12-13,19,29-30,33],compute-1-[2-5,12],compute-2-[1,17,29,38-39,47]
medium          up 7-00:00:00     86  alloc compute-0-[0,2-4,6-11,14-18,20-28,31-32,34-35],compute-1-[0-1,6-11,13-22,24-31],compute-2-[0,2-5,10-16,20-23,28,30-37,40-46]
medium          up 7-00:00:00     10   idle compute-2-[6-9,18-19,24-27]
long            up 14-00:00:0     14    mix compute-0-[1,5,12-13,19,29-30,33],compute-2-[1,17,29,38-39,47]
long            up 14-00:00:0     60  alloc compute-0-[0,2-4,6-11,14-18,20-28,31-32,34-35],compute-2-[0,2-5,10-16,20-23,28,30-37,40-46]
long            up 14-00:00:0     10   idle compute-2-[6-9,18-19,24-27]
gpu             up 4-00:00:00      3    mix compute-0-[36,42-43]
gpu             up 4-00:00:00      5   idle compute-0-[37-41]
volta           up 4-00:00:00      2    mix compute-4-[0-1]
volta           up 4-00:00:00      1  alloc compute-4-2
volta           up 4-00:00:00      1   idle compute-4-3
short-gen10     up    4:00:00     17    mix compute-3-[4,11,13,15-17,22,25,27,29-30,40-43],compute-4-[7-8]
short-gen10     up    4:00:00     32  alloc compute-3-[0-3,5-10,12,14,18-21,23-24,26,31-39],compute-4-[4-6,9]
short-gen10     up    4:00:00      4   idle compute-3-28,compute-4-[10-12]
medium-gen10    up 7-00:00:00     17    mix compute-3-[4,11,13,15-17,22,25,27,29-30,40-43],compute-4-[7-8]
medium-gen10    up 7-00:00:00     32  alloc compute-3-[0-3,5-10,12,14,18-21,23-24,26,31-39],compute-4-[4-6,9]
medium-gen10    up 7-00:00:00      1   idle compute-3-28
long-gen10      up 14-00:00:0     17    mix compute-3-[4,11,13,15-17,22,25,27,29-30,40-43],compute-4-[7-8]
long-gen10      up 14-00:00:0     32  alloc compute-3-[0-3,5-10,12,14,18-21,23-24,26,31-39],compute-4-[4-6,9]
long-gen10      up 14-00:00:0      1   idle compute-3-28
```

WOAH! Okay! This might want to make you completely ignore this tutorial, but stay with me and you will see that this is very interesting knowledge once we break it down. 

The first column states all the different partitions that the Sabine cluster has. To take a better look, lets grab an example of each
```linux
sinfo | (read -r; printf "%s\n" "$REPLY"; sort)
output
```

woihala! Much better. One thing I did not mention at the beginning is that this tutorial should teach some very useful commands on linux.

Now, we can see that we have ```linux sinfo | sort -k1,1 -u | wc -l n``` n partitions on the cluster. Now, if you read the tutorial above, you know that a partition is a set of nodes for a particular job. Now, while most of the columns are very self-explanatory, lets take a look at the first row:
```linux
PARTITION    AVAIL  TIMELIMIT  NODES  STATE NODELIST
batch*          up 14-00:00:0      1 drain* compute-1-23
```
Now, this partition, **batch** is **up** and runnng, has a time limit of 14 hours, 1 node (as you can see, nodelist shows that this is node 23 from compute-1), and is in a state of drain*.

We might scrach our heads a little in what this partition actually means, but all we have to do is search for information 
```linux
scontrol show partition | grep batch

PartitionName=batch
   AllowGroups=ALL AllowAccounts=ALL AllowQos=ALL
   AllocNodes=ALL Default=YES QoS=N/A
   DefaultTime=04:00:00 DisableRootJobs=NO ExclusiveUser=NO GraceTime=0 Hidden=NO
   MaxNodes=UNLIMITED MaxTime=14-00:00:00 MinNodes=0 LLN=NO MaxCPUsPerNode=UNLIMITED
   Nodes=compute-0-[0-35],compute-1-[0-31],compute-2-[0-47]
   PriorityJobFactor=1 PriorityTier=1 RootOnly=NO ReqResv=NO OverSubscribe=NO
   OverTimeLimit=NONE PreemptMode=OFF
   State=UP TotalCPUs=3248 TotalNodes=116 SelectTypeParameters=NONE
   JobDefaults=(null)
   DefMemPerCPU=4096 MaxMemPerNode=UNLIMITED
   TRESBillingWeights=CPU=0.85,Mem=0.0G
```

From reading this, we can immediately gain some insights about this partition:
1. All accounts are allowed to use this partition
2. There are no exlusive users that can use this partition
3. A user can request all nodes and cpus of partition

Of coarse, there is more information in there, but this will suffice for an introduction. Notice that without the grep, information about all partitions is show and their defaults and restrictions.

Now that we have some rudimentary knowledge into the clusters partitions and restrictions, lets head into our own department. 

Once you log in to the cluster using 
```linux
ssh eeplater@sabine.cacds.uh.edu
```
and entering your password, you will end up in your home directory. 

In this directory, you can basically do whatever you want. However, be very wary on your memory limits. Although the cluster is ginormous, remember that its made to handle the request of hundreds of students. 

Therefore, its a good idea to check by using
'''linux
df -h
```
or
```linux
free -mh
```
It is crucial to know that most home spaces will not have enough storage to transport large datasets that are  20G >. For this reason, you will have to use the directory given to your particular administrator. 

Once this is done and we are ready to use the magical powers of the cluster, we first need to make a **request** for its resources (remember, hundreds of others will probably be tring to use the same resources).






We will take a look at UH's Sabine cluster to make an example 

First, most clusters today operate on Slurm, which is a task scheduler used to manage the cluster. It is helpful to think of a cluster as an interconnected network of many computers functioning as one. 

![](https://ucdavis-bioinformatics-training.github.io/2017-June-RNA-Seq-Workshop/monday/cluster_diagram.png)

The above image represents 
