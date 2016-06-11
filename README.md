# Installation for ACISS Users
The hzarloop and clusterfit programs can be run on the University of Oregon's computing cluster using as many nodes as you can get your hands on.  These installation instructions are for installing on ACISS specifically.  However, if you have access to another computer cluster, these instructions may have close analogues for how to install for your cluster.

### Make a directory for files
The first thing you will want to do is make a directory for all of the files in the github repo.  You will want clusterfit.py, hzarloop.py, and the .sh files.  I'll call my folder "hzarloop" and put it in my home directory.

`$ mkdir ~/hzarloop`

Place all of the aforementioned files here.

### Install hzar for R
To do this, you must first be ssh'd into ACISS.

`$ ssh username@aciss.uoregon.edu`

Then load up R.  I used 3.2.3, but later versions will probably work just as well. (maybe stick to 3.2.3 if you want to be safe)  Install hzar in an interactive R session.  I had to use the http servers because ACISS didn't let me download from the https servers.

`$ module load R/3.2.3`

We also need to make a directory for HZAR to be installed.

`$ mkdir ~/Rpackages`

Start up an R session

`$ R`

Then once you are in an interactive R session, type...

`> install.packages(hzar, lib="~/Rpackages")`

Use any of the http servers to install hzar.

Then we need to create a .Renviron file in the home directory so that R knows where to look for the hzar module.

`$ touch ~/.Renviron`

edit the file and add the line

`R_LIBS=~/Rpackages`

Now R will know to look in the ~/Rpackages folder for the hzar module.

### Sending a Job to ACISS
Almost everything is set up, we just need to modify the pbs.sh bash script so that it knows what files you want the program to analyze.  Take note of what the full path to your "hzarloop" directory is, in my case we can see it using...

```
$ pwd
/home7/perrett/hzarloop
```

The first line of pbs.sh indicates the resources that you want from ACISS.  In general you will want generic nodes, but perhaps there will be instances where you will need more computing time than 24 hours in which case you will want to use longgen nodes.  Take a look at this ACISS howto that talks shortly about what kind of nodes they have. 
http://aciss-computing.uoregon.edu/2013/09/04/how-to-submission-queues/

Indicate how many nodes and processors/node you want in the first line

`#PBS -q generic -l nodes=10:ppn=12`

Here, I have indicated that I want 10 nodes, and 12 cores per node.

Then, you will have to change the last line to include your paths instead of mine.

`mpirun -np 120 python3 /home7/perrett/bioinformatics/clusterfit.py --input /home7/perrett/bioinformatics/ref7big.csv --locinames /home7/perrett/bioinformatics/ref7big.txt --output /home7/perrett/bioinformatics/small_output/pbstest --repeat 1 --modelIII False --chainlength 1e5`

Great!  Now the program can be sent in as a job using qsub.

`$ qsub pbs.sh`

We can check if it is in queue or running using...

`$ qstat -u <your username>`

# Tag Options