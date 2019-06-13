basic star workflow consists of 2 steps
1. Generating genome indexes files
--runThreadN NUmberOfReads
--runMode genomeGenerate
--genomeDir /path/to/genomerDir (the directory to store genome indices,created before)
--genomeFastaFIles /path/to/genome/fasta1 /path/to/genomer/fasta2
--sjdbGTFfile /path/to/annotations.gtf (optional)
--sjdbOverhang ReadLength-1 (default 100,works well)


2. mapping reads to the genome
--runThreadN NUmberOfReads
--genomeDir /path/to/genomerDir
--readFileIn /path/to/read1 [/path/to/read2]


3. Output files
--outFileNamePrefile /path/to/out/dir/prefix (default ./)
Log files (Log.out Log.process Log.final.out)
SAM (Aligned.out.sam)

