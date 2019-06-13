https://software.broadinstitute.org/gatk/documentation/tooldocs/4.beta.4/index


https://mp.weixin.qq.com/s?__biz=MzAxMDkxODM1Ng==&mid=2247486167&idx=1&sn=b4a955aa3a17027f074941e8a52f5cc0&chksm=9b484a6cac3fc37a8ac449f5b3635242e361c578a9beca25f38bddc2ac6ace8ddcdaa89a53b0&scene=21#wechat_redirect

# 安装
```shell
mkdir -p ~/biosoft/GATK/ 
cd ~/biosoft/GATK/ 
wget https://github.com/broadinstitute/gatk/releases/download/4.0.2.1/gatk-4.0.2.1.zip
unzip gatk-4.0.2.1.zip 
 ~/biosoft/GATK/gatk-4.0.2.1/gatk  --help
```

# 参考基因组和构建bwa index
```shell
mkdir -p ~/biosoft/GATK/resources/bundle/hg38
cd ~/biosoft/GATK/resources/bundle/hg38
## ftp://ftp.broadinstitute.org/bundle/hg38/
nohup wget ftp://gsapubftp-anonymous@ftp.broadinstitute.org/bundle/hg38/dbsnp_146.hg38.vcf.gz & 
nohup wget ftp://gsapubftp-anonymous@ftp.broadinstitute.org/bundle/hg38/dbsnp_146.hg38.vcf.gz.tbi & 
nohup wget ftp://gsapubftp-anonymous@ftp.broadinstitute.org/bundle/hg38/Mills_and_1000G_gold_standard.indels.hg38.vcf.gz & 
nohup wget ftp://gsapubftp-anonymous@ftp.broadinstitute.org/bundle/hg38/Mills_and_1000G_gold_standard.indels.hg38.vcf.gz.tbi & 
nohup wget ftp://gsapubftp-anonymous@ftp.broadinstitute.org/bundle/hg38/Homo_sapiens_assembly38.fasta.gz & 
nohup wget ftp://gsapubftp-anonymous@ftp.broadinstitute.org/bundle/hg38/Homo_sapiens_assembly38.fasta.fai & 
nohup wget ftp://gsapubftp-anonymous@ftp.broadinstitute.org/bundle/hg38/Homo_sapiens_assembly38.dict & 
nohup wget ftp://gsapubftp-anonymous@ftp.broadinstitute.org/bundle/hg38/1000G_phase1.snps.high_confidence.hg38.vcf.gz & 
nohup wget ftp://gsapubftp-anonymous@ftp.broadinstitute.org/bundle/hg38/1000G_phase1.snps.high_confidence.hg38.vcf.gz.tbi & 
mkdir bwa_index 
cd bwa_index
~/biosoft/bwa/bwa-0.7.15/bwa index -a bwtsw -p gatk_hg38  ../Homo_sapiens_assembly38.fasta 1>hg38.bwa_index.log 2>&1   &
```
测试数据是WES
SRX1969919    Illumina HiSeq 2000 PAIRED  Hybrid Selection    GENOMIC CGU_OSCC_32_WXS_N
Homo sapiens    SRP078156 SRR3943929  female 
# 下载转换质控
```shell
mkdir -p ~/biosoft/GATK/test
cd ~/biosoft/GATK/test
prefetch SRR3943929 
ls -lh  ~/ncbi/public/sra/
mkdir raw 
dump='/home/jianmingzeng/biosoft/sratoolkit/sratoolkit.2.8.2-1-centos_linux64/bin/fastq-dump'
sample='CGU_OSCC_32_WXS_N'
$dump -A $sample -O raw --gzip --split-3 ~/ncbi/public/sra/$srr.sra
## qc
bin_trim_galore="trim_galore"
mkdir -p clean_fastq
$bin_trim_galore -q 25 --phred33 --length 50 -e 0.1 --stringency 3 --paired -o clean_fastq  $fq1 $fq2
```

# gatk 流程
```shell
INDEX=~/biosoft/GATK/resources/bundle/hg38/bwa_index/gatk_hg38
fq1=clean_fastq/CGU_OSCC_32_WXS_N_1_val_1.fq.gz
fq2=clean_fastq/CGU_OSCC_32_WXS_N_2_val_2.fq.gz
sample='oscc'
## 还需要设置好软件地址
GENOME=~/biosoft/GATK/resources/bundle/hg38/Homo_sapiens_assembly38.fasta
GATK=~/biosoft/GATK/gatk-4.0.2.1/gatk
DBSNP=~/biosoft/GATK/resources/bundle/hg38/dbsnp_146.hg38.vcf.gz

nohup ~/biosoft/bwa/bwa-0.7.15/bwa mem -t 5 -M  -R "@RG	ID:$sample	SM:$sample	LB:WES	PL:Illumina" $INDEX $fq1 $fq2 1>$sample.sam 2>/dev/null & 
nohup  $GATK  --java-options "-Xmx20G -Djava.io.tmpdir=./"  SortSam -SO coordinate  -I $sample.sam -O $sample.bam 1>log.sort 2>&1 &
samtools index $sample.bam
samtools flagstat $sample.bam > ${sample}.alignment.flagstat
samtools stats  $sample.bam > ${sample}.alignment.stat

echo plot-bamstats -p ${sample}_QC  ${sample}.alignment.stat

nohup $GATK  --java-options "-Xmx20G -Djava.io.tmpdir=./"   MarkDuplicates  -I $sample.bam -O ${sample}_marked.bam -M $sample.metrics 1>log.mark  2>&1 &


nohup $GATK  --java-options "-Xmx20G -Djava.io.tmpdir=./"   FixMateInformation -I ${sample}_marked.bam -O ${sample}_marked_fixed.bam -SO coordinate   1>log.fix 2>&1 &

samtools index ${sample}_marked_fixed.bam

GENOME=/umac/biosoft/GATK/resources/bundle/hg38/Homo_sapiens_assembly38.fasta
GATK=/umac/biosoft/GATK/gatk-4.0.2.1/gatk
DBSNP=/umac/biosoft/GATK/resources/bundle/hg38/dbsnp_146.hg38.vcf.gz
sample='oscc'
nohup $GATK  --java-options "-Xmx20G -Djava.io.tmpdir=./"   HaplotypeCaller  
-R $GENOME -I ${sample}_marked_fixed.bam  --dbsnp $DBSNP -O  ${sample}_raw.vcf 1>log.HC  2>&1 &