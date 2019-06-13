https://www.jianshu.com/p/a84cd44bac67
```shell
conda create -n rna python=2 
conda info --envs 
source activate rna 
conda install -y sra-tools
conda install -y trimmomatic
conda install -y cutadapt multiqc 
conda install -y trim-galore
conda install -y star hisat2 bowtie2
conda install -y subread tophat htseq bedtools deeptools
conda install -y salmon
source deactivate #注销当前的rna环境
```
软件列表
质控
fastqc , multiqc, trimmomatic, cutadapt ,trim-galore
比对
star, hisat2, bowtie2, tophat, bwa, subread
计数
htseq, bedtools, deeptools, salmon
# 下载数据
SRR_Acc_List.txt：将SRR号码保存在文档内，一个号码占据一行
```shell
wkd=/home/jmzeng/project/airway/ #设置工作目录
source activate rna
cat SRR_Acc_List.txt | while read id; do (prefetch  ${id} );done
mkdir $wkd/raw 
cd $wkd/raw 
ls /public/project/RNA/airway/sra/*  | while read id; do ( fastq-dump --gzip --split-3 -O ./ ${id}  ); done ## 批量转换sra到fq格式。
source deactivate
```
# QC
```shell
ls *gz | xargs fastqc -t 2
multiqc ./ #qc
mkdir $wkd/clean 
cd $wkd/clean 
ls /home/jmzeng/project/airway/raw/*_1.fastq.gz >1
ls /home/jmzeng/project/airway/raw/*_2.fastq.gz >2
paste 1 2  > config
source activate rna
bin_trim_galore=trim_galore
dir='/home/jmzeng/project/airway/clean'
cat config |while read id
do
        arr=(${id})
        fq1=${arr[0]}
        fq2=${arr[1]} 
 $bin_trim_galore -q 25 --phred33 --length 36 --stringency 3 --paired -o $dir $fq1 $fq2
done
source deactivate
```
# 比对
```shell
source activate rna
cd $wkd/clean 
ls *gz|cut -d"_" -f 1 |sort -u |while read id;do
ls -lh ${id}_1_val_1.fq.gz   ${id}_2_val_2.fq.gz 
# 多个比对示范
# bwa bowtie不能跨越内含子比对不建议使用
hisat2 -p 10 -x /public/reference/index/hisat/hg38/genome -1 ${id}_1_val_1.fq.gz   -2 ${id}_2_val_2.fq.gz  -S ${id}.hisat.sam
subjunc -T 5  -i /public/reference/index/subread/hg38 -r ${id}_1_val_1.fq.gz -R ${id}_2_val_2.fq.gz -o ${id}.subjunc.sam
bowtie2 -p 10 -x /public/reference/index/bowtie/hg38  -1 ${id}_1_val_1.fq.gz   -2 ${id}_2_val_2.fq.gz  -S ${id}.bowtie.sam
bwa mem -t 5 -M  /public/reference/index/bwa/hg38   ${id}_1_val_1.fq.gz   ${id}_2_val_2.fq.gz > ${id}.bwa.sam
done
ls *.sam|while read id ;do (samtools sort -O bam -@ 5  -o $(basename ${id} ".sam").bam   ${id});done
rm *.sam 
ls *.bam |xargs -i samtools index {}
ls *.bam |xargs -i samtools flagstat -@ 2  {}  >
ls *.bam |while read id ;do ( samtools flagstat -@ 1 $id >  $(basename ${id} ".bam").flagstat  );done
source deactivate
```
# 计数
```shell
mkdir $wkd/align 
cd $wkd/align 
source activate rna
# 如果一个个样本单独计数，输出多个文件使用代码是：
for fn in {508..523}
do
featureCounts -T 5 -p -t exon -g gene_id  -a /public/reference/gtf/gencode/gencode.v25.annotation.gtf.gz -o $fn.counts.txt SRR1039$fn.bam
done
# 如果是批量样本的bam进行计数，使用代码是：
mkdir $wkd/align 
cd $wkd/align 
source activate rna
gtf="/public/reference/gtf/gencode/gencode.v25.annotation.gtf.gz"   
featureCounts -T 5 -p -t exon -g gene_id  -a $gtf -o  all.id.txt  *.bam  1>counts.id.log 2>&1 &
source deactivate
```
# 接下来做DEG
做一些检查
```R
rm(list = ls())
options(stringsAsFactors = F)
a=read.table('all.id.txt',header = T)
tmp=a[1:14,1:7]
meta=a[,1:6]
exprSet=a[,7:ncol(a)]
colnames(exprSet)
a2=exprSet[,'SRR1039516.hisat.bam']

library(airway)
data(airway)
exprSet=assay(airway)
colnames(exprSet)
a1=exprSet[,'SRR1039516']
group_list=colData(airway)[,3]

a2=data.frame(id=meta[,1],a2=a2)
a1=data.frame(id=names(a1),a1=as.numeric(a1))
library(stringr)
a2$id <- str_split(a2$id,'\\.',simplify = T)[,1]
tmp=merge(a1,a2,by='id')
png('tmp.png')
plot(tmp[,2:3])
dev.off()

library(corrplot)
png('cor.png')
corrplot(cor(log2(exprSet+1)))
dev.off()

library(pheatmap)
png('heatmap.png')
m=cor(log2(exprSet+1))
pheatmap(scale(cor(log2(exprSet+1))))
dev.off()