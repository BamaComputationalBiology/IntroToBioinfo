**Alabama Supercomputer Orientation Part 2**

Now that you know where things live, we need to figure out how to operate on the ASC. We can do a little bit of work on the head node itself but we should submit jobs to the queue whenever possible. In order to do that we need to write a script. 
A script is simply a set of commands that we give the system to execute. We tell it which shell to execute under with the first line (called the ‘shebang’, for example #!/bin/bash for the bash shell) and any lines that we don’t want executed (comments) are ‘commented out’ with a # in the beginning. We will go through creating and submitting a script and examine some preliminary BLAST output from ASC.
Linux/Unix systems don’t support programs like Microsoft Word and in order to create a script on ASC we need to use a text editor. We will use one called vi (this is my favorite) but feel free to use another one. 
Navigate to your home directory
$ cd ~
and use vi to create a new file
$ vi my_script.sh
You should see the vi window with the ~ symbols. Type ‘i’ to get into input or insertion mode and then type the following into your file:
#!/bin/bash
source /opt/asn/etc/asn-bash-profiles/modules.sh
module load blast+/2.3.0
# place commands here
module list
echo "Hello World”
echo "Hello World" > out.txt

Hit the escape button (‘esc’) to get out of insertion mode and type ‘:wq’ (without the single quotes) to save the file and exit vi. You have just written your first script!



What’s going on here? We are telling the system
#!/bin/bash (1)
source /opt/asn/etc/asn-bash-profiles/modules.sh (2)
module load blast+/2.2.27 (3)
# place commands here (4)
module list (5)
echo "Hello World" (6)
echo "Hello World" > out.txt (7)
(1)	Which shell to use (bash)
(2)	Where the module files live on the ASC
(3)	Which module to load
(4)	Comments for ourself (not read by the machine)
(5)	To list the loaded modules for us
(6)	to print “Hello World” to stdout
(7)	to redirect “Hello World” to a file called out.txt

The last thing is that we have to make the script executable. Type
$ chmod +x my_script

Now we can submit this script to the ASC queue system and see what happens. Type
$ run_script my_script

You will start through a number of prompts. You want to use
(1)	the class queue
(2)	1 processor core (the smallest amount of resources, so you get going the fastest)
(3)	A one hour time default (01:00:00)
(4)	1gb of memory (again, a small amount of resources)
(5)	any queue (hit return)
If all goes well you should see something like this:
============================================================
=====         Summary of your script     job           =====
============================================================
  The script file is: my_script.sh
  The time limit is 1:00:00 HH:MM:SS.
  The memory limit is: 1gb
  The job will start running after: 2016-09-27T12:59:50
  Job Name: myscriptshSCRIPT
  Virtual queue: class
  QOS: -p dmc,uv,knl --qos=class
  Constraints: --constraint=uv|dmc
  Queue submit command:
sbatch -p dmc,uv,knl --qos=class -J myscriptshSCRIPT --begin=2016-09-27T12:59:50 --requeue --mail-user=jlfierst@ua.edu -o myscriptshSCRIPT.o%A -t 1:00:00 -N 1-1 -n 1 --mem-per-cpu=1000mb --constraint=uv|dmc  
Submitted batch job 52067

If you don’t, it means your job was either not submitted and is not waiting or running or it has already finished. Type
$ squeue
to check your jobs in the ‘queue.’ You should see two lines like this:
     JOBID               NAME        USER         TIME ST        QOS
     52067   myscriptshSCRIPT    ualcls20         0:05  R      class
where JOBID is your numerical job identifier, NAME is your script identifier, USER is your username, TIME is the time it has been running, ST is the state of the job (R is for running) and QOS is the ‘quality of service’ or queue it is running in (here, the class queue).
Type
$ squeue 
again to check if it is still running. 
When it is finished type
$ ls
and now you should see your script (my_script.sh) along with a job output file (myscriptshSCRIPT……) and your output file out.txt. Look inside your output file
$ more out.txt
What does it say? 
Now look in the job output file
$ more myscriptshSCRIPT……
and you should see something like this
setting dmc scratch directory
 
============================================================
=====         Summary of your script job               =====
============================================================
  The script file is: my_script.sh
  The time limit is 1:00:00 HH:MM:SS.
  The target directory is: /home/ualcls20
  The working directory is:  /scratch-local/ualcls20.myscriptshSCRIPT.52067
  The memory limit is: 1gb
  The job will start running after: 2016-09-27T13:02:34
  Job Name: myscriptshSCRIPT
  Virtual queue: class
  QOS: -p dmc,uv,knl --qos=class
  Constraints: --constraint=uv|dmc
  Using  1  cores on master node  dmc84
  Node list:  dmc84
  Nodes:  dmc84
  Queue submit command:
sbatch -p dmc,uv,knl --qos=class -J myscriptshSCRIPT --begin=2016-09-27T13:02:34 --requeue --mail-user=jlfierst@ua.edu -
o myscriptshSCRIPT.o52067 -t 1:00:00 -N 1-1 -n 1 --mem-per-cpu=1000mb --constraint=uv|dmc  

Currently Loaded Modules:
  1) blast+/2.3.0

Hello World

Below where it says “Summary of your script job” is the output you asked for in your script. The (1) and (2) lines from your script (pasted in below)
#!/bin/bash
source /opt/asn/etc/asn-bash-profiles/modules.sh
module load blast+/2.3.0
# place commands here
module list (1)
echo "Hello World" (2)
echo "Hello World" > out.txt (3)
print to stdout which goes to your job output file, while your final (3) line has printed to your out.txt file. 
This is an important difference. When you ask the ASC to execute a command in your script, it defaults to stdin/stdout/stderr but stdin is no longer your keyboard and stdout is no longer your terminal. Stdin is now embedded in your script and stdout goes to your job script. If you want the ASC to print to a file, you must put the redirect in your script and the file will be placed by default in your home directory.
I hate messy directories, please remove all your accessory job output and error files with
$ rm myscriptSCRIPT……
You can use ‘*’ to make this easier.
Now that we know how to get around in Unix and have access to adequate computational resources we are going to start moving back to the ‘bio’ part of ‘bioinformatics.’ We will be talking about blast and alignment on ASC but for now make a protein file called “protein.txt” with vi
$ vi protein.fasta
and paste in this sequence. Type ‘i’ to start the vi insertion mode and you **should** be able to copy the sequence below and paste it in. Make sure you include the >my_protein part as the .fasta header. 
>my_protein
MEEPQSDPSVEPPLSQETFSDLWKLLPENNVLSPLPSQAMDDLMLSPDDIEQWFTEDPGPDEAPRMPEAAPPVAPAPAAPTPAAPAPAPSWPLSSSVPSQKTYQGSYGFRLGFLHSGTAKSVTCTYSPALNKMFCQLAKTCPVQLWVDSTPPPGTRVRAMAIYKQSQHMTEVVRRCPHHERCSDSDGLAPPQHLIRVEGNLRVEYLDDRNTFRHSV VVPYEPPEVGSDCTTIHYNYMCNS
and edit your script to add this line at the bottom
Reminder: to use vi type
$ vi my_script.sh
and type ‘i’ for insertion mode. Insert this line
blastp -db /apps/bio/unzipped/blast/db/nr -query protein.fasta -outfmt 6 > my_results.txt
and type ‘escape’ then ‘:wq’ to save and exit vi. 
We will go into more detail in future classes, but we are using protein blast (blastp) to search against our NCBI database on ASC (/apps/bio/unzipped/blast/db/nr) to identify our protein and its nearest neighbors. Submit this job to the class queue again and run with more memory (try 64gb). It will take a little while (took me ~10 mins) to run and return a result. Once it is done inspect the file. The first column is the name of your protein, the second the NCBI/Genbank accession number it matched (or blasted) to and the third the percent match. Google the first three accession numbers. What is your protein, and what is its closest cousin?
The -outfmt 6 in the above line tells blast to give us a tabular format. Remove it so your line looks like this
blastp -db /apps/bio/unzipped/blast/db/nr -query protein.txt > my_new_results.txt
and run again. What happens?
Another important command is ‘scancel’ which will delete or kill a job. Try it with your blast job to see how it works. You need to specify the job by number/ID.
$ scancel ‘jobidhere’
The ASC manual (http://www.asc.edu/html/man.pdf) has a lot of good information. In particular, there is a Unix/Linux intro and list of commands starting on p. 58; a vi intro on p. 80 and a intro to the queue system on p. 88.
In-class points for today:
Answer the questions from above
1.	What is your protein, and what is its closest cousin?
2.	What is the difference between tabular format (-outfmt 6) and the default blast output?
