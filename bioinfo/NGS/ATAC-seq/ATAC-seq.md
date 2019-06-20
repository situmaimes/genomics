https://www.jianshu.com/p/5bce43a537fd

# 软件安装
```shell
conda create -n atac -y python=2 bwa
conda info --envs
source activate atac
# 可以用search先进行检索
conda search trim_galore
## 保证所有的软件都是安装在 wes 这个环境下面
conda install -y sra-tools  
conda install -y trim-galore samtools bedtools
conda install -y deeptools homer meme
conda install -y macs2 bowtie bowtie2 
conda install -y multiqc 
conda install -y sambamba
```
config.sra如下
2-cell-1 SRR2927015
2-cell-2 SRR2927016
2-cell-5 SRR3545580
2-cell-4 SRR2927018

# 下载数据
```shell

mkdir -p  ~/project/atac/
cd ~/project/atac/
mkdir {sra,raw,clean,align,peaks,motif,qc}
cd sra 
source activate atac 
cat srr.list |while read id;do ( nohup  prefetch $id & );done
## 默认下载目录：~/ncbi/public/sra/ 
ls -lh ~/ncbi/public/sra/

cd ~/project/atac/
dump=fastq-dump
analysis_dir=raw
mkdir -p $analysis_dir
cat config.sra |while read id;
do echo $id
arr=($id)
srr=${arr[1]}
sample=${arr[0]}
#  测序数据的sra转fasq
nohup $dump -A $sample -O $analysis_dir  --gzip --split-3  sra/$srr.sra & 
done 

### 如果不只是4个文件，需要使用shell脚本批处理。
cut -f 10,13 SRP055881/SraRunTable.txt|\
sed 's/Embryonic stem cell/ESC/'|sed 's/early 2-cell/e2-cell/' |\
perl -alne '{$h{$F[1]}++;print "$_-$h{$F[1]}"}' |tail -n+2|awk '{print $2"\t"$1}'> config.sra
source deactivate
```
# QC
```shell
mkdir -p clean 
source activate atac  
cat config.raw |while read id;
do echo $id
arr=($id)
fq2=${arr[2]}
fq1=${arr[1]}
sample=${arr[0]}
nohup trim_galore -q 25 --phred33 --length 35 -e 0.1 --stringency 4 --paired -o  clean  $fq1   $fq2  & 
done 
cd ~/project/atac/qc 
mkdir -p clean 
fastqc -t 5  ../clean/2-cell-*gz -o clean 
mkdir -p raw 
fastqc -t 5  ../raw/2-cell-*gz -o clean 

qualimap='/home/jianmingzeng/biosoft/Qualimap/qualimap_v2.2.1/qualimap'
$qualimap bamqc --java-mem-size=20G  -bam $id  -outdir ./
```
# align
```shell
mkdir referece && cd reference
wget -4 -q ftp://ftp.ccb.jhu.edu/pub/data/bowtie2_indexes/mm10.zip # -q, --quiet 安静模式(不输出信息)。  
unzip mm10.zip

ls /home/jmzeng/project/atac/clean/*_1.fq.gz > 1
ls /home/jmzeng/project/atac/clean/*_2.fq.gz > 2
ls /home/jmzeng/project/atac/clean/*_2.fq.gz |cut -d"/" -f 7|cut -d"_" -f 1  > 0
paste 0 1 2  > config.clean ## 供mapping使用的配置文件

cd ~/project/epi/align
bowtie2_index=/public/reference/index/bowtie/mm10
## 一定要搞清楚自己的bowtie2软件安装在哪里，以及自己的索引文件在什么地方！！！
source activate atac 
cat config.clean |while read id;
do echo $id
arr=($id)
fq2=${arr[2]}
fq1=${arr[1]}
sample=${arr[0]}
## 比对过程15分钟一个样本
bowtie2  -p 5 --very-sensitive -X 2000 -x  $bowtie2_index -1 $fq1 -2 $fq2 |samtools sort  -O bam  -@ 5 -o - > ${sample}.raw.bam 
samtools index ${sample}.raw.bam 
bedtools bamtobed -i ${sample}.raw.bam  > ${sample}.raw.bed
samtools flagstat ${sample}.raw.bam  > ${sample}.raw.stat
# https://github.com/biod/sambamba/issues/177
sambamba markdup --overflow-list-size 600000  --tmpdir='./'  -r ${sample}.raw.bam  ${sample}.rmdup.bam
samtools index  ${sample}.rmdup.bam 

## ref:https://www.biostars.org/p/170294/ 
## Calculate %mtDNA:
mtReads=$(samtools idxstats  ${sample}.rmdup.bam | grep 'chrM' | cut -f 3)
totalReads=$(samtools idxstats  ${sample}.rmdup.bam | awk '{SUM += $3} END {print SUM}')
echo '==> mtDNA Content:' $(bc <<< "scale=2;100*$mtReads/$totalReads")'%'

samtools flagstat ${sample}.rmdup.bam > ${sample}.rmdup.stat
samtools view -h -f 2 -q 30  ${sample}.rmdup.bam   |grep -v chrM |samtools sort  -O bam  -@ 5 -o - > ${sample}.last.bam
samtools index  ${sample}.last.bam 
samtools flagstat  ${sample}.last.bam > ${sample}.last.stat 
bedtools bamtobed -i ${sample}.last.bam  > ${sample}.bed
done

# 找peak
```shell
# macs2 callpeak -t 2-cell-1.bed  -g mm --nomodel --shift -100 --extsize 200  -n 2-cell-1 --outdir ../peaks/
ls *.bed | while read id ;do (macs2 callpeak -t $id  -g mm --nomodel --sHit  -100 --extsize 200  -n ${id%%.*} --outdir ../peaks/) ;done

