https://www.ncbi.nlm.nih.gov/pubmed/24074865 
http://www.bio-info-trainee.com/2352.html


```shell
#下载
wget ftp://ftp-trace.ncbi.nlm.nih.gov/sra/sra-instant/reads/ByStudy/sra/SRP/SRP018/SRP018845/SRR764931/SRR764931.sra
wget ftp://ftp-trace.ncbi.nlm.nih.gov/sra/sra-instant/reads/ByStudy/sra/SRP/SRP018/SRP018845/SRR764932/SRR764932.sra
#sra转fastq
ls *sra | while read id;
do ~/biosoft/sratoolkit/sratoolkit.2.6.3-centos_linux64/bin/fastq-dump --split-3 $id;
done
#质控
ls *fastq |xargs ~/biosoft/fastqc/FastQC/fastqc -t 10
ls *.fastq | while read id
do
~/biosoft/sickle/sickle-master/sickle se -t sanger -g -f $id -o ${id%%.*}.trimmed.fq.gz
done
#比对 sam转bam
~/biosoft/bowtie/bowtie2-2.2.9/bowtie2 -p 6 -x ~/reference/index/bowtie/mm10 -U SRR764931.fastq | samtools sort -O bam -o shDnmt3L.bam
samtools index shDnmt3L.bam
#call peak
~/.local/bin/macs2 callpeak -t shDnmt3L.bam -m 10 30 -p 1e-5 -f BAM -g mm -n shDnmt3L 2>shDnmt3L.masc2.log
bamCoverage -b shDnmt3L.bam -o shDnmt3L.bw ## 这里有个参数，-p 10 --normalizeUsingRPKM
computeMatrix reference-point --referencePoint TSS -b 10000 -a 10000 -R ~/annotation/CHIPseq/mm10/ucsc.refseq.bed -S shDnmt3L.bw --skipZeros -o matrix1_shDnmt3L_TSS.gz
plotHeatmap -m matrix1_shDnmt3L_TSS.gz -out shDnmt3L.png