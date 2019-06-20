# SAM  BAM(binary) 
1 read_name 
2 sum_of_flags 
3 reference_sequence_name 
4 position 
5 mapping_quality 
6 CIGAR 
7 mate_reference_sequence_name 
8 mate_position 
9 isize 
10 sequence 
11 quality_ascii 
12 optional_fields
# GTF
1 seq_id：序列的编号
2 source: 注释的来源，一般为数据库或者注释的机构，如果未知，则用点“.”代替；
3 type: 注释信息的类型，比如Gene、cDNA、mRNA、CDS等
4 start: 该基因或转录本在参考序列上的起始位置；
5 end: 该基因或转录本在参考序列上的终止位置；
6 score: 得分，数字，是注释信息可能性的说明，可以是序列相似性比对时的E-values值或者基因预测是的P-values值，“.”表示为空；
7 strand: 该基因或转录本位于参考序列的正链(+)或负链(-)上;
8 phase: 仅对注释类型为“CDS”有效，表示起始编码的位置，有效值为0、1、2(对于编码蛋白质的CDS来说，本列指定下一个密码子开始的位置。每3个核苷酸翻译一个氨基酸，从0开始，CDS的起始位置，除以3，余数就是这个值，，表示到达下一个密码子需要跳过的碱基个数。该编码区第一个密码子的位置，取值0,1,2。0表示该编码框的第一个密码子第一个碱基位于其5'末端；1表示该编码框的第一个密码子的第一个碱基位于该编码区外；2表示该编码框的第一个密码子的第一、二个碱基位于该编码区外；如果Feature为CDS时，必须指明具体值。)；
9 attributes:一个包含众多属性的列表，格式为“标签＝值”（tag=value），标签与值之间以空格分开，且每个特征之后都要有分号；（包括最后一个特征），其内容必须包括gene_id和transcript_id。以多个键值对组成的注释信息描述，键与值之间用“=”，不同的键值用“；
# BED
必须包含的3列：
chrom, 染色体或scafflold 的名字(eg chr3， chrY, chr2_random, scaffold0671 )
chromStart 染色体或scaffold的起始位置，染色体第一个碱基的位置是0
chromEn 染色体或scaffold的结束位置，染色体的末端位置没有包含到显示信息里面。例如，首先得100个碱基的染色体定义为chromStart =0 . chromEnd=100, 碱基的数目是0-99

# VCF VariantCallFormat