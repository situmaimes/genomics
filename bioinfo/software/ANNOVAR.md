convert2annovar.pl -format vcf4 12.vcf >12.annovar

infolder=/home/jmzeng/hoston/diff
outfolder=$infolder
annovardb=/home/jmzeng/bio-soft/annovar/humandb
# start annotating
/home/jmzeng/bio-soft/annovar/annotate_variation.pl \
--buildver hg19 \
--geneanno \
--outfile ${outfolder}/12.anno \
${infolder}/12.annovar  \
${annovardb}