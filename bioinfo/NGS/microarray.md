http://www.bio-info-trainee.com/1024.html
http://www.breast-cancer-research.com/content/13/5/R97










代码
```R
#下载数据
suppressMessages(library(GEOquery))
setwd("D:\\test_analysis\\TNBC")
gse31519=getGEO("GSE31519",GSEMatrix = T,destdir = "./")
getGEOSuppFiles("GSE31519",baseDir = "./")
gse31519=getGEO("GSE20437",GSEMatrix = T,destdir = "./")
getGEOSuppFiles("GSE20437",baseDir = "./")
#差异分析
library(affy)
library(limma)
affy.data=ReadAffy(celfile.path="./cel_files")
eset.rma=rma(affy.data)
exprSet=exprs(eset.rma)
write.table(exprSet,"expr_rma_matrix.txt",quote=F,sep="\t")
group=factor(c(rep("control",42),rep("case",67)))
design = model.matrix(~0+group)
colnames(design)=c("case","control")
rownames(design)=sampleNames(affy.data)
fit=lmFit(exprSet,design)
cont.matrix = makeContrasts(contrasts="case-control",levels=design)
fit2=contrasts.fit(fit,cont.matrix)
fit2=eBayes(fit2)
diff_dat=topTable(fit2,coef=1,n=Inf)
write.table(diff_dat,"diff_dat.txt",quote=F)

#注释
library(biomart)
ensembl=useMart("ensembl",dataset="hsapiens_gene_ensembl")
gene_probe=getBM(attributes=c("hgnc_symbol","affy_hg_u133a"),filter="affy_hg_u133a",value=rownames(diff_dat),mart=ensembl)
diff_probe=rownames(diff_dat[abs(diff_dat[,1])>2,])
diff_gene=gene_probe[match(diff_probe,gene_probe[,2]),1]
diff_gene=na.omit(diff_gene)
diff_gene=unique(diff_gene)
length(diff_gene)

#GO和KEGG富集分析
gene_entrez=getBM(attributes=c("hgnc_symbol","entrezgene"),filter="hgnc_symbol",value=diff_gene,mart=ensembl)
require(DOSE)
require(clusterProfiler)
gene_entrez=na.omit(gene_entrez)
gene=as.character(gene_entrez[,2])
ego <- enrichGO(gene=gene,organism="human",ont="CC",pvalueCutoff=0.01,readable=TRUE)
ekk <- enrichKEGG(gene=gene,organism="human",pvalueCutoff=0.01,readable=TRUE)
write.csv(summary(ekk),"KEGG-enrich.csv",row.names =F)
write.csv(summary(ego),GO-enrich.csv,row.names =F)