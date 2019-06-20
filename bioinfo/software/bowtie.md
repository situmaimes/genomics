http://bowtie-bio.sourceforge.net/bowtie2/manual.shtml

bowtie2 build hg19.fa hg19
bowtie2 -x hg19 -U ref.fa -S temp.sam
bowtie2  -p 5  --very-sensitive -X 2000 -x $bowtie2_index -1 $fq1 -2 $fq2

bowtie2 [options]* -x <bt2-idx> {-1 <m1> -2 <m2> | -U <r> | --interleaved <i> | --sra-acc <acc> | b <bam>} -S [<sam>]


-x <bt2-idx>
The basename of the index for the reference genome. The basename is the name of any of the index files up to but not including the final .1.bt2 / .rev.1.bt2 / etc. bowtie2 looks for the specified index first in the current directory, then in the directory specified in the BOWTIE2_INDEXES environment variable.

--very-sensitive
Same as: -D 20 -R 3 -N 0 -L 20 -i S,1,0.50

-p/--threads NTHREADS

-X/--maxins <int>
The maximum fragment length for valid paired-end alignments.