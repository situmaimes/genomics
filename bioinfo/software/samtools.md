https://www.htslib.org/doc/samtools.html

samtools view -bt ref_list.txt -o aln.bam aln.sam.gz
samtools sort -T /tmp/aln.sorted -o aln.sorted.bam aln.bam
samtools index aln.sorted.bam
samtools idxstats aln.sorted.bam
samtools flagstat aln.sorted.bam
samtools stats aln.sorted.bam
samtools bedcov aln.sorted.bam
samtools depth aln.sorted.bam
samtools view aln.sorted.bam chr2:20,100,000-20,200,000
samtools merge out.bam in1.bam in2.bam in3.bam
samtools faidx ref.fasta
samtools fqidx ref.fastq
samtools tview aln.sorted.bam ref.fasta
samtools split merged.bam
samtools quickcheck in1.bam in2.cram
samtools dict -a GRCh38 -s "Homo sapiens" ref.fasta
samtools fixmate in.namesorted.sam out.bam
samtools mpileup -C50 -f ref.fasta -r chr3:1,000-2,000 in1.bam in2.bam
samtools flags PAIRED,UNMAP,MUNMAP
samtools fastq input.bam > output.fastq
samtools fasta input.bam > output.fasta
samtools addreplacerg -r 'ID:fish' -r 'LB:1334' -r 'SM:alpha' -o output.bam input.bam
samtools collate -o aln.name_collated.bam aln.sorted.bam
samtools depad input.bam
samtools markdup in.algnsorted.bam out.bam



Common Use:
# view
samtools view -bS aligned/$f.Aligned.out.sam  -o aligned/$f.bam
samtools view [options] in.sam|in.bam|in.cram [region...]
With no options or regions specified, prints all alignments in the specified input alignment file (in SAM, BAM, or CRAM format) to standard output in SAM format (with no header).
-b :Output in the BAM format.
-S :Ignored for compatibility with previous samtools versions. Previously this option was required if input was in SAM format, but now the correct format is automatically detected by examining the first few characters of input.
-@ INT :Number of BAM compression threads to use in addition to main thread [0].
-h Include the header in the output.
-f Only output alignments with all bits set in INT present in the FLAG field
-q Skip alignments with MAPQ smaller than INT [0].
# flagstat 
Does a full pass through the input file to calculate and print statistics to stdout.

# index
Index a coordinate-sorted BAM or CRAM file for fast random access. 

# stats
samtools stats collects statistics from BAM files and outputs in a text format. The output can be visualized graphically using plot-bamstats.


# markdup

samtools markdup [-l length] [-r] [-s] [-T] [-S] in.algsort.bam out.bam

Mark duplicate alignments from a coordinate sorted file that has been run through fixmate with the -m option. This program relies on the MC and ms tags that fixmate provides.

-r
Remove duplicate reads.

EXAMPLE
# The first sort can be omitted if the file is already name ordered
samtools sort -n -o namesort.bam example.bam
# Add ms and MC tags for markdup to use later
samtools fixmate -m namesort.bam fixmate.bam
# Markdup needs position order
samtools sort -o positionsort.bam fixmate.bam
# Finally mark duplicates
samtools markdup positionsort.bam markdup.bam


# sort
samtools sort [-l level] [-m maxMem] [-o out.bam] [-O format] [-n] [-t tag] [-T tmpprefix] [-@ threads] [in.sam|in.bam|in.cram]

# idxstats
idxstats
samtools idxstats in.sam|in.bam|in.cram

Retrieve and print stats in the index file corresponding to the input file. Before calling idxstats, the input BAM file should be indexed by samtools index.