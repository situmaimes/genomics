http://www.bio-info-trainee.com/2257.html
https://www.ncbi.nlm.nih.gov/pubmed/23273917
流程：
- sratoolkit fastdump 将sra转为fastq
- fastqc 质控
- bowtie2 比对到参考基因组上
- samtools 转sam为bam
- MACS2 call peaks



代码
```shell
for ((i=204;i<=209;i++)) ;
do wget ftp://ftp-trace.ncbi.nlm.nih.gov/sra/sra-instant/reads/ByStudy/sra/SRP/SRP017/SRP017311/SRR620$i/SRR620$i.sra;
done
ls *sra |while read id; do ~/biosoft/sratoolkit/sratoolkit.2.6.3-centos_linux64/bin/fastq-dump --split-3 $id;done
#质控
ls *fastq |xargs ~/biosoft/fastqc/FastQC/fastqc -t 10
#比对
~/biosoft/bowtie/bowtie2-2.2.9/bowtie2 -p 6 -3 5 --local -x ~/reference/index/bowtie/mm10 -U SRR620204.fastq | samtools sort -O bam -o ring1B.bam
~/biosoft/bowtie/bowtie2-2.2.9/bowtie2 -p 6 -3 5 --local -x ~/reference/index/bowtie/mm10 -U SRR620205.fastq | samtools sort -O bam -o cbx7.bam
~/biosoft/bowtie/bowtie2-2.2.9/bowtie2 -p 6 -3 5 --local -x ~/reference/index/bowtie/mm10 -U SRR620206.fastq | samtools sort -O bam -o suz12.bam
~/biosoft/bowtie/bowtie2-2.2.9/bowtie2 -p 6 -3 5 --local -x ~/reference/index/bowtie/mm10 -U SRR620207.fastq | samtools sort -O bam -o RYBP.bam
~/biosoft/bowtie/bowtie2-2.2.9/bowtie2 -p 6 -3 5 --local -x ~/reference/index/bowtie/mm10 -U SRR620208.fastq | samtools sort -O bam -o IgGold.bam
~/biosoft/bowtie/bowtie2-2.2.9/bowtie2 -p 6 -3 5 --local -x ~/reference/index/bowtie/mm10 -U SRR620209.fastq | samtools sort -O bam -o IgG.bam
# call peak
nohup ~/.local/bin/macs2 callpeak -c ../IgGold.bam -t ../suz12.bam -m 10 30 -p 1e-5 -f BAM -g mm -n suz12 2>suz12.masc2.log &
nohup ~/.local/bin/macs2 callpeak -c ../IgGold.bam -t ../ring1B.bam -m 10 30 -p 1e-5 -f BAM -g mm -n ring1B 2>ring1B.masc2.log &
nohup ~/.local/bin/macs2 callpeak -c ../IgG.bam -t ../cbx7.bam -m 10 30 -p 1e-5 -f BAM -g mm -n cbx7 2>cbx7.masc2.log &
nohup ~/.local/bin/macs2 callpeak -c ../IgG.bam -t ../RYBP.bam -m 10 30 -p 1e-5 -f BAM -g mm -n RYBP 2>RYBP.masc2.log &


