# minimap2 has replaced BWA-MEM for PacBio and Nanopore read alignment
manual page: http://bio-bwa.sourceforge.net/bwa.shtml
git clone https://github.com/lh3/bwa.git
cd bwa; make
./bwa index ref.fa
./bwa mem ref.fa read-se.fq.gz | gzip -3 > aln-se.sam.gz
./bwa mem ref.fa read1.fq read2.fq | gzip -3 > aln-pe.sam.gz

BWA-backtrack, BWA-SW and BWA-MEM

For all the algorithms, BWA first needs to construct the FM-index for the reference genome (the index command). Alignment algorithms are invoked with different sub-commands: aln/samse/sampe for BWA-backtrack, bwasw for BWA-SW and mem for the BWA-MEM algorithm.





bwa index ref.fa
bwa mem ref.fa reads.fq > aln-se.sam
bwa mem ref.fa read1.fq read2.fq > aln-pe.sam
bwa aln ref.fa short_read.fq > aln_sa.sai
bwa samse ref.fa aln_sa.sai short_read.fq > aln-se.sam
bwa sampe ref.fa aln_sa1.sai aln_sa2.sai read1.fq read2.fq > aln-pe.sam
bwa bwasw ref.fa long_read.fq > aln.sam


# index
index	bwa index [-p prefix] [-a algoType] <in.db.fasta>
Index database sequences in the FASTA format.

OPTIONS:
-p STR	Prefix of the output database [same as db filename]
-a STR	Algorithm for constructing BWT index. Available options are:
is	IS linear-time algorithm for constructing suffix array. It requires 5.37N memory where N is the size of the database. IS is moderately fast, but does not work with database larger than 2GB. IS is the default algorithm due to its simplicity. The current codes for IS algorithm are reimplemented by Yuta Mori.
bwtsw	Algorithm implemented in BWT-SW. This method works with the whole human genome.

# mem
-M	Mark shorter split hits as secondary (for Picard compatibility).
-t INT	Number of threads [1]
-R STR	Complete read group header line. ’\t’ can be used in STR and will be converted to a TAB in the output SAM. The read group ID will be attached to every read in the output. An example is ’@RG\tID:foo\tSM:bar’. [null]