http://ncbi.github.io/sra-tools/

commands:
fastq-dump https://www.jianshu.com/p/a8d70b66794c 
fastq-dump --gzip --split-3 --defline-qual '+' --defline-seq '@$ac-$si/$ri' SRRXXXXX| SRRXXXX.sra
prefetch
sam-dump
sra-pileup
vdb-config
vdb-decrypt


fasterq-dump https://github.com/ncbi/sra-tools/wiki/HowTo:-fasterq-dump
