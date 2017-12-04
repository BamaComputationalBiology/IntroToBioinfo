In order to associate RNA-Seq counts with genes we will use HTSeq-Count. More information here:

https://htseq.readthedocs.io/en/release_0.9.1/count.html

You will need to obtain the gene file (.gtf or .gff) for your organism. I can help with locating this so contact me if you are having difficulty. 

On ASC we can access htseq-count through python and anacondas. Load both

    > module load python
    > module load anaconda

You should now be able to access htseq-count. Try it without any arguments to make sure you have it loaded

    > htseq-count

You should get an error message that says

    > usage: htseq-count [options] alignment_file gff_file
    > htseq-count: error: too few arguments
    
The syntax for htseq-count is 

    > htseq-count <alignment file> <gff file>
