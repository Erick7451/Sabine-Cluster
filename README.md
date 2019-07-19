# Sabine-Cluster
What to know before working with a Cluster that operates on Slurm.

At first, working with a cluster can be a little overwhelming. However, once you break down its infrastructure, logic, and scheduling, it becomes one of the greatest tools for a Data Scientist!

Before you begin (and to get the most out of this tutorial, I would highly recommend to read the article shown below if this is your first time learning about Slurm, its purposes, and operations.

https://support.ceci-hpc.be/doc/_contents/QuickStart/SubmittingJobs/SlurmTutorial.html

Now! Lets get started.

Today, we will explore the University of Houston's Sabine cluster to get a peak of its power.

First, we will use the command **sinfo** to get more information about the partitions of the cluster.

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

WOAH! Okay! This might look daunting, but stay with me and you will see that this is very interesting knowledge once we break it down. 

The first column states all the different partitions that the Sabine cluster has. To take a better look, lets grab an example of each
```linux
[eeplater@sabine ~]$ sinfo | (read -r; echo $REPLY; sort -k1,1 -u)
PARTITION AVAIL TIMELIMIT NODES STATE NODELIST
batch*          up 14-00:00:0      1 drain* compute-1-23
gpu             up 4-00:00:00      8   idle compute-0-[36-43]
long            up 14-00:00:0     34    mix compute-0-[0,3-4,10,12,14,18-19,25-26,29-30,33-35],compute-2-[1,5,7,9,17,19,21-23,27,29,38-44,46]
long-gen10      up 14-00:00:0      5    mix compute-3-[20-22],compute-4-[4-5]
medium          up 7-00:00:00      1 drain* compute-1-23
medium-gen10    up 7-00:00:00      5    mix compute-3-[20-22],compute-4-[4-5]
short           up    4:00:00      1 drain* compute-1-23
short-gen10     up    4:00:00      7    mix compute-3-[20-22],compute-4-[4-5,10-11]
volta           up 4-00:00:00      2    mix compute-4-[0-1]
```

woihala! Much better. One thing I did not mention at the beginning is that this tutorial should teach some very useful commands on linux.

Now, lets calculate how many unique clusters we have
```linux 
[eeplater@sabine ~]$ NUM=$(sinfo | sort -k1,1 -u | wc -l); echo $((NUM -1))
9
```
Cool! We have 9 unique partitions on our cluster. 

Now, while most of the columns are very self-explanatory, lets take a look at the first row:
```linux
[eeplater@sabine ~]$ sinfo | (read -r; echo $REPLY; sort -k1,1 -u | head -1)
PARTITION AVAIL TIMELIMIT NODES STATE NODELIST
batch*          up 14-00:00:0      1 drain* compute-1-23
```
Now, this partition, **batch** is **up** and runnng, has a maximum **time limit** of 14 days: 0 hours: 0 minutes: 0 seconds, 1 node (as you can see, nodelist shows that this is node 23 from compute-1), and is in a **state** of drain*, which means that it is currently unavailable.

We might scratch our heads a little in what this partition actually entails, however, all we have to do is dig-in for more information using **scontrol show partition**. 
```linux
[eeplater@sabine ~]$ scontrol show partition | head -12
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

Of coarse, there is more information in there, but this will suffice for an introduction. Notice that without the grep, information about all partitions will show along with their defaults and restrictions.

Now that we have some fundamental knowledge into the clusters partitions and restrictions, lets head into our own department. 

Once you log in to the cluster using 
```linux
ssh your_name@sabine.cacds.uh.edu
```
and entering your password, you will end up in your home directory. 

In this directory, you can basically do whatever you want. We can look at our rights by doing 
```linux
[eeplater@sabine ~]$ ls -l
total 24
drwxr-xr-x 3 eeplater kakadiaris 8192 Jul 14 12:12 log
drwxr-xr-x 8 eeplater kakadiaris  162 Jul 16 20:51 Python_Scripts
drwxr-xr-x 2 eeplater kakadiaris    6 Jun 25 11:02 scr
-rw-r--r-- 1 eeplater kakadiaris   59 Jun 25 15:23 setup.sh
-rw-r--r-- 1 eeplater kakadiaris   48 Jul 11 20:43 startup.sh
-rw-r--r-- 1 eeplater kakadiaris   55 Jul 17 15:33 test.sh
```
We can see that we have all rights for this files. In fact, you can even see the name of your administrator. For me, its **kakadiaris**. For this reason, I have permission to access the kakadiaris server and transfer my datasets. 
```linux
[eeplater@sabine ~]$ cd /project/kakadiaris/;ls -l
total 8
drwxrwxr-x 28 xxu18    kakadiaris 4096 Jul 11 14:40 Datasets
drwxr-sr-x  8 eeplater kakadiaris  200 Jul 16 20:51 Erick
drwxrwxr-x 15 xxu18    kakadiaris  226 May  9 16:34 Experiments
-rw-rw-r--  1 xxu18    kakadiaris  217 Feb 28  2018 README.md
drwxr-sr-x  3 ywu35    kakadiaris   28 Feb 18  2018 users
```
However, when I try to access someone elses directory, you will get this followin error
```linux
cd ..; cd amritkar/
-bash: cd: amritkar/: Permission denied
```


One more thing that will save you much time is to be very wary on your memory limits. Although the cluster is ginormous, remember that its made to handle the request of hundreds of students. 

Therefore, its a good idea to check by using
```linux
[eeplater@sabine ~]$ df -h | grep eepla
/dev/mapper/sabine_storage-eeplater          10G   10G  1.5M 100% /home/eeplater
```
We see that actually, I am at full capacity with my home directory. 
It is crucial to know that most home spaces will not have enough storage to transport large datasets that are  20G >. For this reason, you will have to use the directory given to your particular administrator. 

On my administrator's directory, here are the memory limits:
```linux
[eeplater@sabine kakadiaris]$ df -h | grep kaka
nfs-4-2-ib0:/export/project/kakadiaris       10T  2.8T  7.3T  28% /project/kakadiaris
```
We can see that we have **much more data** in my administrators directory. Therefore, this directory is where all the heavy duty datasets are stored in.

Once this is done and we are ready to use the magical powers of the cluster, we first need to make a **request** for its resources (remember, hundreds of others will probably be tring to use the same resources).

Although you can find many examples of requesting resources using the bash method, I generally prefer to use the **interactive method**.

Lets seen an example:
```linux
[eeplater@sabine ~]$ srun -A kakadiaris -t 24:00:00 -n 1 -p gpu --gres=gpu:2 --pty /bin/bash -l
[eeplater@compute-0-36 ~]$
```
Now, to get a better understanding of these symbols, lets look at the symbols.
```linux
[eeplater@sabine ~]$ srun --help
Usage: srun [OPTIONS...] executable [args...]

Parallel run options:
  -A, --account=name          charge job to specified account
      --acctg-freq=<datatype>=<interval> accounting and profiling sampling
                              intervals. Supported datatypes:
                              task=<interval> energy=<interval>
                              network=<interval> filesystem=<interval>
      --bb=<spec>             burst buffer specifications
      --bbf=<file_name>       burst buffer specification file
      --bcast=<dest_path>     Copy executable file to compute nodes
      --begin=time            defer job until HH:MM MM/DD/YY
  -c, --cpus-per-task=ncpus   number of cpus required per task
      --checkpoint=time       job step checkpoint interval
      --checkpoint-dir=dir    directory to store job step checkpoint image
                              files
      --comment=name          arbitrary comment
      --compress[=library]    data compression library used with --bcast
      --cpu-freq=min[-max[:gov]] requested cpu frequency (and governor)

  -n, --ntasks=ntasks         number of tasks to run
      --nice[=value]          decrease scheduling priority by value
      --ntasks-per-node=n     number of tasks to invoke on each node
  -N, --nodes=N               number of nodes on which to run (N = min[-max])

  -p, --partition=partition   partition requested

```
In the above, I only listed some of the commands, however, the list is much much longer and you can find some really cool commands such as sending emails to yourself once permission has been granted! 


Now, you can see that the front header changed from **sabine** to **compute-0-36** which tells me that I am on the first partition batch and am using node 36. Now, just to make sure we actually have two gpus, lets check it with pytorch
```linux
[eeplater@compute-0-36 ~]$ module add Anaconda3
[eeplater@compute-0-36 ~]$ python3
Python 3.6.8 |Anaconda custom (64-bit)| (default, Dec 30 2018, 01:22:34)
[GCC 7.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import torch
>>> torch.cuda.device_count()
2
```
Perfect! We have everything set up and ready to go. However, what if you want to terminate the use of the cluster earlier?

First, lets find out where we are with ```linux; squeue```. If you read the tutorial, you know that this command finds all the requests that the cluster is currently handling. Lets see ours.

```linux
[eeplater@compute-0-36 ~]$ squeue | (read -r; echo "$REPLY"; grep eeplater)
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           1509684       gpu     bash eeplater  R       7:39      1 compute-0-36
```
Now, we see our **Job ID** along with the partition,name, user, status, time, nodes, and nodelist. 

Now, we can cancel our current resources by using **scancel JOB_ID**. Lets try it:
```linux
[eeplater@compute-0-36 ~]$ scancel 1509684
srun: Force Terminated job 1509684
[eeplater@compute-0-36 ~]$ srun: Job step aborted: Waiting up to 32 seconds for job step to finish.
srun: error: compute-0-36: task 0: Killed
[eeplater@sabine ~]$
```
Now, if we go back and check, we should not be able to see our name anymore (unless we have more than one job running)
```linux
[eeplater@sabine ~]$ squeue | grep eeplater
[eeplater@sabine ~]$
```

Sweet! Now one more thing to notice.
In the above example, we used the partition **gpu**. However, look what happens when we ask for **four gpus**.
```linux
[eeplater@sabine ~]$ srun -A kakadiaris -t 24:00:00 -n 1 -p gpu --gres=gpu:4 --pty /bin/bash -l
srun: error: Unable to allocate resources: Requested node configuration is not available
[eeplater@sabine ~]$
```
Look how we get an error now. If you see this, know that the partition does **not** have the resources you are requesting. Our partition gpu only has two gpus and therefore cannot grant our requests of 4 gpus. However, lets use a more powerful cluster
```linux
[eeplater@sabine ~]$ srun -A kakadiaris -t 24:00:00 -n 28 -p volta --gres=gpu:8 --pty /bin/bash -l
[eeplater@compute-4-2 ~]$ python3 -c "import torch; print(torch.cuda.device_count())"
8
```
Boom! Its that easy, we now have **8 GPUs**! Time to start training some very deep learning models!

Okay, this should give you a very good base start to start on your cluster adventure!
Have fun with the cluster and remember, always strive to learn more! 

