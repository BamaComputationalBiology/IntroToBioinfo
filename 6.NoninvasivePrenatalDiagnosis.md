Noninvasive Prenatal Diagnosis

For this exercise we will analyze data from the NCBI sequence read archive and perform a noninvasive fetal aneuploidy detection. BGI (formerly the Beijing Genomics Institute) submitted samples to the US NCBI sequence read archive that were used in designing their NIFTY test. The test is described on their website, www.niftytest.com. Read the website, particularly the portion for ‘healthcare providers.’

The basic workflow is outlined below, and specific instructions follow that. In this exercise I will give you some specific instructions on commands but you will also need to work with your group to figure out the commands based on the workflow and instructions and the commands we have learned in previous classes. You will also need to read the documentation for the programs that we will use and figure out the commands based on your knowledge of UNIX. 

Workflow:

1) Retrieve sample from the NCBI ftp site
2) Use the sra toolkit to convert the .sra binary file to a workable .fastq file
3) Inspect the .fastq file
4) Use gmap-gsnap to align the .fastq file to the human genome reference sequence in ascii format (.sam)
5) Use samtools to create a sorted binary alignment file (.bam)
6) Use samtools to create an index for your file (.bai)
7) Use bedtools to create a genome coverage file
8) Use awk to calculate coverage means and standard deviations for chromosomes 

The first thing we will do is download the data from the NCBI sequence read archive. The data are described here: http://sra.dnanexus.com/studies/SRP037560/experiments and the study described here: http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0092192 . I have selected samples from the archive so that each group will be analyzing a different sample. Your assigned samples are:

Group 1 - SRR1167204, SRR1167208

Group 2 - SRR1167205, SRR1167209

Group 3 - SRR1167206, SRR1167210

Group 4 - SRR1167207, SRR1167211

The easiest way to download them is to retrieve the file with *wget* directly from the NCBI ftp site. You format the address like so:

ftp://ftp.ncbi.nlm.nih.gov/sra/sra-instant/reads/ByRun/sra/SRR/SRR358/SRR358005/SRR358005.sra

But you will have to change the numbers around to fit your sample. 

Once you have the sample on your Alabama Supercomputer account you will need to transfer it from .sra (a binary NCBI-only file format) to a workable fastq file. NCBI provides their own sra toolkit, it is available on ASC as the module ‘sra.’ The documentation for converting .sra to .fastq is here http://www.ncbi.nlm.nih.gov/Traces/sra/sra.cgi?view=toolkit_doc&f=fastq-dump.

An example script:

#!/bin/bash

source /opt/asn/etc/asn-bash-profiles/modules.sh

module load sra

# place commands here

for file in *sra

	do

		fastq-dump --split-files ${file}

    done

Reminders for running scripts:

1) make it executable with $ chmod +x myscriptnamehere

2) ASC assumes that everything is located in your home directory. If you are working in another directory (if you, for example, made a “prenatal” directory and everything is in there) you must specify the path to your files in your script. For example, ~/prenatal/*.sra .

3) Submit your script with $ run_script myscriptnamehere

Once we have the data in .fastq format we can use a short read alignment program like Bowtie2 (http://bowtie-bio.sourceforge.net/bowtie2/index.shtml ) to align the reads to a reference human genome sequence. 

Reference genomes are large and commonly used reference sequences are located in /opt/asn/bio/unzipped/bowtie2/ . Alignment programs require that reference genomes are indexed with compression algorithms that facilitate rapid searching. I wrote a script below to facilitate moving forward at this step. You will need to change it for your samples.


#!/bin/bash

source /opt/asn/etc/asn-bash-profiles/modules.sh

module load bowtie2

# place commands here

bowtie2 -x /apps/bio/unzipped/bowtie2/Homo_sapiens/UCSC/hg38/Sequence/Bowtie2Index/genome -1 SRR953066_1.fastq -2 SRR953066_2.fastq -S bowtie2.sam

Make sure you run your alignment with a script!

.sam is a great format for inspecting your reads and getting to know your data. The file starts with a number of @SQ lines where every line corresponds to a section of the reference genome. The SN: field gives the chromosome and the LN: field gives the length. 

Take a look at the actual .sam alignment lines ( Hint: grep for the lines that don’t begin with @, i.e. -v, and look at the first 10 lines *head*. Also look at each column of data individually with cut -f 1 ; cut -f 2 ; etc). You can find information about the .sam format here  https://samtools.github.io/hts-specs/SAMv1.pdf and here http://genome.sph.umich.edu/wiki/SAM .

In order to format our .sam file and analyze genome coverage we need to convert it to a binary format. Our binary file must also be sorted and indexed. We use samtools for this (http://www.htslib.org/doc/samtools.html). Use the example script below but replace ‘file.sam’ and ‘file_sorted’ with the names of each of your files. 

#!/bin/bash

source /opt/asn/etc/asn-bash-profiles/modules.sh

module load samtools

# place commands here

samtools view -bS file.sam | samtools sort - file_sorted

Now, try to write the script and index command on your own.
Next time we will analyze coverage across the human genome. 
