https://www.jianshu.com/p/49d035b121b8
WES主要是为了找exon的变异
# 安装软件
```shell
conda create -n wes python=2 bwa
conda info --envs
conda activate wes
# 可以用search先进行检索
conda search sratools
## 保证所有的软件都是安装在 wes 这个环境下面
conda install sra-tools
conda install samtools
conda install -y bcftools vcftools snpeff
conda install -y multiqc qualimap
 
# https://software.broadinstitute.org/gatk/download/
cd ~/biosoft
mkdir -p gatk4 && cd gatk4
wget  https://github.com/broadinstitute/gatk/releases/download/4.0.6.0/gatk-4.0.6.0.zip
unzip gatk-4.0.6.0.zip
```
需要的数据，建立好的bwa索引
/public/biosoft/GATK/resources/bundle/hg38/bwa_index/
|-- [ 20K]  gatk_hg38.amb
|-- [445K]  gatk_hg38.ann
|-- [3.0G]  gatk_hg38.bwt
|-- [767M]  gatk_hg38.pac
|-- [1.5G]  gatk_hg38.sa
|-- [6.2K]  hg38.bwa_index.log 
`-- [ 566]  run.sh
gatk需要的参考基因组、变异
/public/biosoft/GATK/resources/bundle/hg38/
|-- [1.8G]  1000G_phase1.snps.high_confidence.hg38.vcf.gz
|-- [2.0M]  1000G_phase1.snps.high_confidence.hg38.vcf.gz.tbi
|-- [3.2G]  dbsnp_146.hg38.vcf.gz
|-- [3.0M]  dbsnp_146.hg38.vcf.gz.tbi
|-- [ 59M]  hapmap_3.3.hg38.vcf.gz
|-- [1.5M]  hapmap_3.3.hg38.vcf.gz.tbi
|-- [568K]  Homo_sapiens_assembly38.dict
|-- [3.0G]  Homo_sapiens_assembly38.fasta
|-- [157K]  Homo_sapiens_assembly38.fasta.fai
|-- [ 20M]  Mills_and_1000G_gold_standard.indels.hg38.vcf.gz
|-- [1.4M]  Mills_and_1000G_gold_standard.indels.hg38.vcf.gz.tbi


# QC
fastqc看一下质量
```shell
mkdir ~/project/boy
wkd=/home/jmzeng/project/boy
mkdir {raw,clean,qc,align,mutation}
cd qc 
find /public/project/clinical/beijing_boy -name *gz |grep -v '\._'|xargs fastqc -t 10 -o ./ 
#grep -v   --revert-match   #显示不包含匹配文本的所有行。
#fastqc -t 线程数 -o 输出目录
```

# filter
trim_galore去除低质量位点
```shell
mkdir $wkd/clean 
cd $wkd/clean

find /public/project/clinical/beijing_boy -name *gz |grep -v '\._'|grep 1.fastq.gz > 1
find /public/project/clinical/beijing_boy -name *gz |grep -v '\._'|grep 2.fastq.gz > 2
paste 1 2  > config  # 把每个文件的列加以合并
### 打开文件 qc.sh ，并且写入内容如下： 
conda activate wes

bin_trim_galore=trim_galore
dir=$wkd/clean
cat config | while read id
do
        arr=(${id})
        fq1=${arr[0]}
        fq2=${arr[1]} 
        echo  $dir  $fq1 $fq2 
nohup $bin_trim_galore -q 25 --phred33 --length 36 -e 0.1 --stringency 3 --paired -o $dir  $fq1 $fq2 & 
done 
# -q/–quality ：控制的质量分数阈值 –length ：丢弃小于此长度的读段
# -e：允许的错误率 –stringency：限定最少与adaptor序列重叠的碱基数（用来trim的标准）
conda deactivate
```
# align
文件名如下
7E5239    7E5239.L1_1.fastq   7E5239.L1_2.fastq
7E5240    7E5240_L1_A001.L1_1.fastq   7E5240_L1_A001.L1_2.fastq
7E5241    7E5241.L1_1.fastq   7E5241.L1_2.fastq
```shell
ls /home/jmzeng/project/boy/clean/*1.fq.gz >1
ls /home/jmzeng/project/boy/clean/*2.fq.gz >2
cut -d"/" -f 7 1 |cut -d"_" -f 1  > 0 #获取7E5239.L1 7E5240 7E5241.L1作为sample name
paste 0 1 2 > config 
conda activate wes
INDEX=/public/biosoft/GATK/resources/bundle/hg38/bwa_index/gatk_hg38
cat config |while read id
do
arr=($id)
fq1=${arr[1]}
fq2=${arr[2]}
sample=${arr[0]}
echo $sample $fq1 $fq2 
bwa mem -t 5 -R "@RG\tID:$sample\tSM:$sample\tLB:WGS\tPL:Illumina" $INDEX $fq1 $fq2  | samtools sort -@ 5 -o $sample.bam -   
#bwa mem模式比对然后sort
done 
```
# call variation
## 简单的bcftools
```shell
ref=/public/biosoft/GATK/resources/bundle/hg38/Homo_sapiens_assembly38.fasta
conda activate wes
time samtools mpileup -ugf $ref *.bam | bcftools call -vmO z -o out.vcf.gz
ls *.bam |xargs -i samtools index {} #对每个bam建立index
#-f 来输入有索引文件的fasta参考序列  -g 输出到bcf格式，-u 不压缩
#-O选择的是z格式输出 -v 输出有变异的位点 -m 一个位点，可以允许多种突变情况吧。
```
## GATK流程
```shell
conda activate wes
GATK=/home/jmzeng/biosoft/gatk4/gatk-4.0.6.0/gatk
ref=/public/biosoft/GATK/resources/bundle/hg38/Homo_sapiens_assembly38.fasta
snp=/public/biosoft/GATK/resources/bundle/hg38/dbsnp_146.hg38.vcf.gz
indel=/public/biosoft/GATK/resources/bundle/hg38/Mills_and_1000G_gold_standard.indels.hg38.vcf.gz  

for sample in {7E5239.L1,7E5240,7E5241.L1}
do 
echo $sample  
# Elapsed time: 7.91 minutes
$GATK --java-options "-Xmx20G -Djava.io.tmpdir=./" MarkDuplicates \
    -I $sample.bam \
    -O ${sample}_marked.bam \
    -M $sample.metrics \
    1>${sample}_log.mark 2>&1 

## Elapsed time: 13.61 minutes
$GATK --java-options "-Xmx20G -Djava.io.tmpdir=./" FixMateInformation \
    -I ${sample}_marked.bam \
    -O ${sample}_marked_fixed.bam \
    -SO coordinate \
    1>${sample}_log.fix 2>&1 

samtools index ${sample}_marked_fixed.bam

##  17.2 minutes
$GATK --java-options "-Xmx20G -Djava.io.tmpdir=./"  BaseRecalibrator \
    -R $ref  \
    -I ${sample}_marked_fixed.bam  \
    --known-sites $snp \
    --known-sites $indel \
    -O ${sample}_recal.table \
    1>${sample}_log.recal 2>&1 
    
$GATK --java-options "-Xmx20G -Djava.io.tmpdir=./"   ApplyBQSR \
    -R $ref  \
    -I ${sample}_marked_fixed.bam  \
    -bqsr ${sample}_recal.table \
    -O ${sample}_bqsr.bam \
    1>${sample}_log.ApplyBQSR  2>&1 
    
## 使用GATK的HaplotypeCaller命令
$GATK --java-options "-Xmx20G -Djava.io.tmpdir=./" HaplotypeCaller \
     -R $ref  \
     -I ${sample}_bqsr.bam \
      --dbsnp $snp \
      -O ${sample}_raw.vcf \
      1>${sample}_log.HC 2>&1  
done 
```
## 特点区域 call variation
chr17   HAVANA  gene    43044295        43170245
```shell
samtools  view -h 7E5240.bam chr17:43044295-43170245 |samtools sort -o  7E5240.brca1.bam -
samtools  view -h 7E5240_bqsr.bam chr17:43044295-43170245 |samtools sort -o  7E5240_bqsr.brca1.bam -
samtools  view -h 7E5240_marked.bam chr17:43044295-43170245 |samtools sort -o  7E5240_marked.brca1.bam -
samtools  view -h 7E5240_marked_fixed.bam chr17:43044295-43170245 |samtools sort -o  7E5240_marked_fixed.brca1.bam -
ls  *brca1.bam|xargs -i samtools index {}
conda activate wes
ref=/public/biosoft/GATK/resources/bundle/hg38/Homo_sapiens_assembly38.fasta
samtools mpileup -ugf $ref 7E5240_bqsr.brca1.bam  | bcftools call -vmO z -o 7E5240_bqsr.vcf.gz
```

## bqsr走bcftools
```shell
conda activate wes
wkd=/home/jmzeng/project/boy
cd $wkd/align 
ls  *_bqsr.bam |xargs -i samtools index {}
ref=/public/biosoft/GATK/resources/bundle/hg38/Homo_sapiens_assembly38.fasta
nohup samtools mpileup -ugf $ref  *_bqsr.bam | bcftools call -vmO z -o all_bqsr.vcf.gz & 
```

# 注释
```shell
~/biosoft/ANNOVAR/annovar/convert2annovar.pl -format vcf4old 7E5240_raw.vcf  >7E5240.annovar
~/biosoft/ANNOVAR/annovar/annotate_variation.pl -buildver hg38 --outfile 7E5240.anno 7E5240.annovar ~/biosoft/ANNOVAR/annovar/humandb/
```
一次性注释多个数据库
```shell
dir=/home/jianmingzeng/biosoft/ANNOVAR/annovar
db=$dir/humandb/ 
ls $db
wget https://ftp.ncbi.nlm.nih.gov/pub/clinvar/vcf_GRCh38/archive_2.0/2018/clinvar_20180603.vcf.gz 
perl $dir/annotate_variation.pl --downdb --webfrom annovar --buildver hg38 clinvar_20180603 $db
perl $dir/annotate_variation.pl  --downdb --webfrom annovar --buildver hg38 gnomad_genome $db
mkdir annovar_results 
perl $dir/convert2annovar.pl -format vcf4old highQ.vcf  1> highQ.avinput  2>/dev/null 
perl $dir/annotate_variation.pl  -buildver hg38 -filter -dbtype clinvar_20180603  --outfile annovar_results/highQ_clinvar  highQ.avinput  $db
perl $dir/table_annovar.pl -buildver hg38 highQ.avinput  $db -out test -remove -protocol refGene,clinvar_20170905 -operation g,r -nastring NA 
```