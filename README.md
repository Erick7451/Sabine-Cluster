# Sabine-Cluster
Today we are going to take a tour around the operations, functions, and logic of the University of Houston's Sabine Cluster.

At first, working with a cluster can be a little overwhelming. However, once you break down its infrastructure, logic, and scheduling, it becomes one of the greatest tools for a Data Scientist!

Before you begin (and to get the most out of this tutorial, I would highly recommend to read the article shown below if this is your first time learning about Slurm, its purposes, and operations.

https://support.ceci-hpc.be/doc/_contents/QuickStart/SubmittingJobs/SlurmTutorial.html

Now! Lets get started.



First, let's us take a look at the **sinfo** command to attain more information of the cluster's partition.

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

WOAH! Okay! This might look like a lot, however, stay with me and you will see that this is very simple information.

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

Voila! Much better. From this, we can see that our Sabine Cluster has nine different partitions, each with their own unique purpose. To find out more about this, lets analyze the first partition: **batch**

```linux
[eeplater@sabine ~]$ sinfo | (read -r; echo $REPLY; sort -k1,1 -u | head -1)
PARTITION AVAIL TIMELIMIT NODES STATE NODELIST
batch*          up 14-00:00:0      1 drain* compute-1-23
```
Now, this partition, **batch** is **up** and runnng, has a maximum **time limit** of 14 days: 0 hours: 0 minutes: 0 seconds, 1 node (as you can see, **nodelist** shows that this is node 23 from compute-1), and is in a **state** of drain*, which means that it is currently unavailable.

Now that we know some properties, what makes this partition unique from the others? Say volta?

Let's compare these two with **scontrol show partition** command. 
```linux
[eeplater@sabine ~]$ scontrol show partition | grep -E 'batch|volta' -A 11
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
--
PartitionName=volta
   AllowGroups=ALL AllowAccounts=ALL AllowQos=ALL
   AllocNodes=ALL Default=NO QoS=N/A
   DefaultTime=04:00:00 DisableRootJobs=NO ExclusiveUser=NO GraceTime=0 Hidden=NO
   MaxNodes=UNLIMITED MaxTime=4-00:00:00 MinNodes=0 LLN=NO MaxCPUsPerNode=UNLIMITED
   Nodes=compute-4-[0-3]
   PriorityJobFactor=1 PriorityTier=1 RootOnly=NO ReqResv=NO OverSubscribe=NO
   OverTimeLimit=NONE PreemptMode=OFF
   State=UP TotalCPUs=112 TotalNodes=4 SelectTypeParameters=NONE
   JobDefaults=(null)
   DefMemPerCPU=4096 MaxMemPerNode=UNLIMITED
   TRESBillingWeights=CPU=0.85,Mem=0.0G,GRES/gpu=31.0

```

if we look hard enough, we can see some differences such as the **TotalCPUs**, however, let's make the differences more clear by using process substitution
```linux
[eeplater@sabine ~]$ diff <(scontrol show partition | grep 'PartitionName=batch' -A 11) <(scontrol show partition | grep 'PartitionName=volta' -A 11)
1c1
< PartitionName=batch
---
> PartitionName=volta
3c3
<    AllocNodes=ALL Default=YES QoS=N/A
---
>    AllocNodes=ALL Default=NO QoS=N/A
5,6c5,6
<    MaxNodes=UNLIMITED MaxTime=14-00:00:00 MinNodes=0 LLN=NO MaxCPUsPerNode=UNLIMITED
<    Nodes=compute-0-[0-35],compute-1-[0-31],compute-2-[0-47]
---
>    MaxNodes=UNLIMITED MaxTime=4-00:00:00 MinNodes=0 LLN=NO MaxCPUsPerNode=UNLIMITED
>    Nodes=compute-4-[0-3]
9c9
<    State=UP TotalCPUs=3248 TotalNodes=116 SelectTypeParameters=NONE
---
>    State=UP TotalCPUs=112 TotalNodes=4 SelectTypeParameters=NONE
12c12
<    TRESBillingWeights=CPU=0.85,Mem=0.0G
---
>    TRESBillingWeights=CPU=0.85,Mem=0.0G,GRES/gpu=31.0
```

Much better.

From this, we can immediately see that:
1. Default 
2. Maxtime
3. TotalCPUs
4. GRES/gpu

Are all different.

Now that we have some fundamental knowledge into the clusters partitions, let's quickly take a look at our permissions and memory limits.

In your home directory, you have all rights. 
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
In fact, you can even see the name of your administrator. For me, its **kakadiaris**, and for this reason, I have permission to access the server and transfer my datasets (keep in mind that restrictions become very strict once you venture of your home or administrative directory). 
```linux
[eeplater@sabine ~]$ cd /project/kakadiaris/;ls -l
total 8
drwxrwxr-x 28 xxu18    kakadiaris 4096 Jul 11 14:40 Datasets
drwxr-sr-x  8 eeplater kakadiaris  200 Jul 16 20:51 Erick
drwxrwxr-x 15 xxu18    kakadiaris  226 May  9 16:34 Experiments
-rw-rw-r--  1 xxu18    kakadiaris  217 Feb 28  2018 README.md
drwxr-sr-x  3 ywu35    kakadiaris   28 Feb 18  2018 users
```
This is crucial as your home directory will have very limited amounts of data. For me, I began with 10G and therefore I quickly had to store my datasets in the administrative directory as it will have much more data
```linux
[eeplater@sabine ~]$ df -h | grep eepla
/dev/mapper/sabine_storage-eeplater          10G   10G  1.5M 100% /home/eeplater
```

Therefore, now that we know of our home and administrative directory, we can begin using the magical powers of our cluster. First, we need to make a **request** for its resources (remember, hundreds of others will probably be tring to use the same resources).

Although you can find many examples of requesting resources using the bash method, I generally prefer to use the **interactive method**.

Lets seen an example:
```linux
[eeplater@sabine ~]$ srun -A kakadiaris -t 24:00:00 -n 1 -p gpu --gres=gpu:2 --pty /bin/bash -l
```
This will:
1. Charge request to your **a**dmnistrators account
2. Grant resources for **t**ime-limit of 24 hours
3. Use 1 **n**ode
4. Request gpu **p**artition
5. Number of **gpu**s (2)
6. Run task on pseudo terminal (**--pty**)
7. Prepend task number to **l**ines of stdout

I only listed some of the commands, however, the list is much much longer and you can find some really cool commands such as sending emails to yourself once permission has been granted! 

Once our request has been granted, our front header will change from **sabine** to **compute-0-36** which tells me that I am using node 36. Now, just to make sure we actually have two gpus, lets check it with pytorch
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
Perfect! We have everything set up and ready to go. However, what if you want to terminate the use of the cluster earlier? To do this, we must first learn our specific **JOBID** that is given to each request.
```linux
[eeplater@compute-0-36 ~]$ squeue | (read -r; echo "$REPLY"; grep eeplater)
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           1509684       gpu     bash eeplater  R       7:39      1 compute-0-36
```
Now that we have our **JOBID**, we can cancel our current resources by using **scancel JOB_ID**
```linux
[eeplater@compute-0-36 ~]$ scancel 1509684
srun: Force Terminated job 1509684
[eeplater@compute-0-36 ~]$ srun: Job step aborted: Waiting up to 32 seconds for job step to finish.
srun: error: compute-0-36: task 0: Killed
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
Look how we get an error now. If you encounter such error, know that the partition does **not** have the resources you are requesting. Our partition (**gpu**) only has two gpus and therefore cannot grant our requests of 4 gpus. However, lets use a more powerful parition
```linux
[eeplater@sabine ~]$ srun -A kakadiaris -t 24:00:00 -n 28 -p volta --gres=gpu:8 --pty /bin/bash -l
[eeplater@compute-4-2 ~]$ python3 -c "import torch; print(torch.cuda.device_count())"
8
```
Boom! It's that easy. We now have **8 GPUs**! Time to start training some very deep learning models!

Okay, this should give you a very good base start to start on your cluster adventure!
Have fun with the cluster and remember, always strive to learn more! 

