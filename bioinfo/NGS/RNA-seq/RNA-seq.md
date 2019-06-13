http://www.bio-info-trainee.com/2218.html
https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE50177
流程
- sratoolkit fastdump 将sra转为fastq
- fastqc 质控
- hisat2 比对到参考基因组上
- samtools  将比对结果sam转为bam
- htseq-counts 基因表达量定量
- 差异分析 limma，DEseq2，edgeR

代码
```shell
#下载
for ((i=677;i<=680;i++));
do wget ftp://ftp-trace.ncbi.nlm.nih.gov/sra/sra-instant/reads/ByStudy/sra/SRP/SRP029/SRP029245/SRR957$i/SRR957$i.sra;
done
#sra转为fastq
ls *sra | while read id; do ~/biosoft/sratoolkit/sratoolkit.2.6.3-centos_linux64/bin/fastq-dump --split-3 $id;done
#质控
ls *fastq |xargs ~/biosoft/fastqc/FastQC/fastqc -t 10
#比对
reference=/home/jianmingzeng/reference/index/hisat/hg19/genome
~/biosoft/HISAT/current/hisat2 -p 5 -x $reference -U SRR957677.fastq -S control_1.sam 2>control_1.log
~/biosoft/HISAT/current/hisat2 -p 5 -x $reference -U SRR957678.fastq -S control_2.sam 2>control_2.log
~/biosoft/HISAT/current/hisat2 -p 5 -x $reference -U SRR957679.fastq -S siSUZ12_1.sam 2>siSUZ12_1.log
~/biosoft/HISAT/current/hisat2 -p 5 -x $reference -U SRR957680.fastq -S siSUZ12_2.sam 2>siSUZ12_2.log
#sam转bam
ls *sam |while read id;do (nohup samtools sort -n -@ 5 -o ${id%%.*}.Nsort.bam $id &);done
#表达量定量
ls *.Nsort.bam |while read id;do (nohup samtools view $id | ~/.local/bin/htseq-count -f sam -s no -i gene_name - ~/reference/gtf/gencode/gencode.v25lift37.annotation.gtf 1>${id%%.*}.geneCounts 2>${id%%.*}.HTseq.log&);done



https://www.plob.org/article/11454.html 