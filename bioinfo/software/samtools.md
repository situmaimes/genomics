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

# flagstat 
Does a full pass through the input file to calculate and print statistics to stdout.

# index
Index a coordinate-sorted BAM or CRAM file for fast random access. 

# stats
samtools stats collects statistics from BAM files and outputs in a text format. The output can be visualized graphically using plot-bamstats.