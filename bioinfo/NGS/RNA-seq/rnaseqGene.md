URL:http://www.bioconductor.org/packages/release/workflows/vignettes/rnaseqGene/inst/doc/rnaseqGene.html

experiment:In the experiment, four primary human airway smooth muscle cell lines were treated with 1 micromolar dexamethasone for 18 hours. For each of the four cell lines, we have a treated and an untreated sample. For more description of the experiment see the PubMed entry 24926665 and for raw data see the GEO entry GSE52778.

normlize:It is important to never provide counts that were pre-normalized for sequencing depth/library size, as the statistical model is most powerful when applied to un-normalized counts, and is designed to account for library size differences internally.

transcript abundance quantification methods:
Salmon (Patro et al. 2017), 
Sailfish (Patro, Mount, and Kingsford 2014), 
kallisto (Bray et al. 2016), 
RSEM (Li and Dewey 2011), 
tximport package for assembling estimated count and offset matrices for use with Bioconductor differential gene expression packages.
# Align
Here, we used the STAR read aligner (Dobin et al. 2013) to align the reads for our current experiment to the Ensembl release 75 (Flicek et al. 2014) human reference genome. 

```shell
for f in `cat files`; do STAR --genomeDir ../STAR/ENSEMBL.homo_sapiens.release-75 \
--readFilesIn fastq/$f\_1.fastq fastq/$f\_2.fastq \
--runThreadN 12 --outFileNamePrefix aligned/$f.; done
```
generate sam

```shell
for f in `cat files`; do samtools view -bS aligned/$f.Aligned.out.sam \
-o aligned/$f.bam; done
```
generate bam
# Count matrix
function	        package	             framework	    output	                DESeq2 input function
summarizeOverlaps	GenomicAlignments	 R/Bioconductor	SummarizedExperiment	DESeqDataSet
featureCounts	    Rsubread	         R/Bioconductor	matrix	                DESeqDataSetFromMatrix
tximport	        tximport	         R/Bioconductor	list of matrices	    DESeqDataSetFromTximport
htseq-count	        HTSeq	             Python	        files	                DESeqDataSetFromHTSeq

We now proceed using summarizeOverlaps. 
Note: make sure that the chromosome names of the genomic features in the annotation you use are consistent with the chromosome names of the reference used for read alignment.
See the seqlevelsStyle function in the GenomeInfoDb package for solutions.
We can check the chromosome names (here called “seqnames”) 
```r
library("airway")
indir <- system.file("extdata", package="airway", mustWork=TRUE)
csvfile <- file.path(indir, "sample_table.csv")
sampleTable <- read.csv(csvfile, row.names = 1)
filenames <- file.path(indir, paste0(sampleTable$Run, "_subset.bam"))
library("Rsamtools")
bamfiles <- BamFileList(filenames, yieldSize=2000000)
```
using makeTxDbFromGFF from the GenomicFeatures package to transform gtf/gff to TxDb
For the known genes track from the UCSC Genome Browser (Kent et al. 2002), one can use the pre-built Transcript DataBase: TxDb.Hsapiens.UCSC.hg19.knownGene. If the annotation file is accessible from AnnotationHub (as is the case for the Ensembl genes), a pre-scanned GTF file can be imported using makeTxDbFromGRanges.
```r
library("GenomicFeatures")
gtffile <- file.path(indir,"Homo_sapiens.GRCh37.75_subset.gtf")
txdb <- makeTxDbFromGFF(gtffile, format = "gtf", circ_seqs = character()) #indicating that none of our sequences (chromosomes) are circular using a 0-length character vector.
ebg <- exonsBy(txdb, by="gene")
```
We will use summarizeOverlaps from the GenomicAlignments package.

```r
library("GenomicAlignments")
library("BiocParallel")
register(SerialParam()) # can specify multiple cores
se <- summarizeOverlaps(features=ebg, reads=bamfiles,
                        mode="Union",
                        singleEnd=FALSE, # paired
                        ignore.strand=TRUE, # not strand-specfic
                        fragments=TRUE )   # create SummarizedExperiment object
colData(se) <- DataFrame(sampleTable) #add colData
```
# Use DESeq2 to Create DESeqDataSet
 to next analysis
DESeqDataSet is built on top of the SummarizedExperiment class, and it is easy to convert SummarizedExperiment objects into DESeqDataSet objects.

One of the two main differences is that the  assay slot is instead accessed using the counts accessor function, and the DESeqDataSet class enforces that the values in this matrix are non-negative integers. 
A second difference is that the DESeqDataSet has an associated design formula. The experimental design is specified at the beginning of the analysis
The design formula tells which columns in the sample information table (colData) specify the experimental design and how these factors should be used in the analysis.

The simplest design formula for differential expression would be ~ condition, where  condition is a column in colData(dds) that specifies which of two (or more groups) the samples belong to. 
## Starting from SummarizedExperiment
```r
data("airway")
se <- airway
library("magrittr")
#it is prefered in R that the first level of a factor be the reference level (e.g. control, or untreated samples)
se$dex %<>% relevel("untrt") # se$dex <- relevel(se$dex, "untrt")
se$dex
library("DESeq2")
dds <- DESeqDataSet(se, design = ~ cell + dex)
```
## Starting from count matrices
build an DESeqDataSet supposing we only have a count matrix and a table of sample information.
```r
countdata <- assay(se)
coldata <- colData(se)
```
all we need: 
countdata: a table with the fragment counts
coldata: a table with information about the samples
```r
ddsMat <- DESeqDataSetFromMatrix(countData = countdata,
                                  colData = coldata,
                                  design = ~ cell + dex)
```
# Exploratory analysis and visualization
```r
# Pre-filtering the dataset
nrow(dds)
dds <- dds[ rowSums(counts(dds)) > 1, ]
nrow(dds)
```
DESeq2 offers two transformations for count data that stabilize the variance across the mean: the variance stabilizing transformation (VST) for negative binomial data with a dispersion-mean trend (Anders and Huber 2010), implemented in the vst function, and the regularized-logarithm transformation or rlog (Love, Huber, and Anders 2014).
Which transformation to choose? The VST is much faster to compute and is less sensitive to high count outliers than the rlog. The rlog tends to work well on small datasets (n < 30), potentially outperforming the VST when there is a wide range of sequencing depth across samples (an order of magnitude difference). We therefore recommend the VST for medium-to-large datasets (n > 30). You can perform both transformations and compare the meanSdPlot or PCA plots generated, as described below.
```r
vsd <- vst(dds, blind = FALSE)
rld <- rlog(dds, blind = FALSE)
```
blind = FALSE means that differences between cell lines and treatment (the variables in the design) will not contribute to the expected variance-mean trend of the experiment.
Sample distances
```r
library("dplyr")
library("ggplot2")
dds <- estimateSizeFactors(dds)
df <- bind_rows(
  as_data_frame(log2(counts(dds, normalized=TRUE)[, 1:2]+1)) %>%
         mutate(transformation = "log2(x + 1)"),
  as_data_frame(assay(vsd)[, 1:2]) %>% mutate(transformation = "vst"),
  as_data_frame(assay(rld)[, 1:2]) %>% mutate(transformation = "rlog"))
colnames(df)[1:2] <- c("x", "y")  
ggplot(df, aes(x = x, y = y)) + geom_hex(bins = 80) +
  coord_fixed() + facet_grid( . ~ transformation)  
```
## Sample distances
```r
sampleDists <- dist(t(assay(vsd)))
library("pheatmap")
library("RColorBrewer")
sampleDistMatrix <- as.matrix( sampleDists )
rownames(sampleDistMatrix) <- paste( vsd$dex, vsd$cell, sep = " - " )
colnames(sampleDistMatrix) <- NULL
colors <- colorRampPalette( rev(brewer.pal(9, "Blues")) )(255)
pheatmap(sampleDistMatrix,
         clustering_distance_rows = sampleDists,
         clustering_distance_cols = sampleDists,
         col = colors)

library("PoiClaClu")
poisd <- PoissonDistance(t(counts(dds)))
samplePoisDistMatrix <- as.matrix( poisd$dd )
rownames(samplePoisDistMatrix) <- paste( dds$dex, dds$cell, sep=" - " )
colnames(samplePoisDistMatrix) <- NULL
pheatmap(samplePoisDistMatrix,
         clustering_distance_rows = poisd$dd,
         clustering_distance_cols = poisd$dd,
         col = colors)

plotPCA(vsd, intgroup = c("dex", "cell"))
pcaData <- plotPCA(vsd, intgroup = c( "dex", "cell"), returnData = TRUE)
percentVar <- round(100 * attr(pcaData, "percentVar"))
library("ggplot2")
ggplot(pcaData, aes(x = PC1, y = PC2, color = dex, shape = cell)) +
  geom_point(size =3) +
  xlab(paste0("PC1: ", percentVar[1], "% variance")) +
  ylab(paste0("PC2: ", percentVar[2], "% variance")) +
  coord_fixed()
mds <- as.data.frame(colData(vsd))  %>%
         cbind(cmdscale(sampleDistMatrix))
ggplot(mds, aes(x = `1`, y = `2`, color = dex, shape = cell)) +
  geom_point(size = 3) + coord_fixed()
```
# Differential expression analysis
Calling results without any arguments will extract the estimated log2 fold changes and p values for the last variable in the design formula
```r
dds <- DESeq(dds)
res <- results(dds)
## DataFrame with 6 rows and 2 columns
##                        type                               description
##                 <character>                               <character>
## baseMean       intermediate mean of normalized counts for all samples
## log2FoldChange      results  log2 fold change (MLE): dex trt vs untrt
## lfcSE               results          standard error: dex trt vs untrt
## stat                results          Wald statistic: dex trt vs untrt
## pvalue              results       Wald test p-value: dex trt vs untrt
## padj                results                      BH adjusted p-values
summary(res) # p-value < 0.1
res.05 <- results(dds, alpha = 0.05) # lower the false discovery rate threshold p-value < 0.05
resLFC1 <- results(dds, lfcThreshold=1) # raise the log2 fold change thresholdlog fold change threshold = 1

# other comparison
results(dds, contrast = c("cell", "N061011", "N61311"))
sum(res$padj < 0.1, na.rm=TRUE)
resSig <- subset(res, padj < 0.1)
head(resSig[ order(resSig$log2FoldChange), ])
head(resSig[ order(resSig$log2FoldChange, decreasing = TRUE), ])
topGene <- rownames(res)[which.min(res$padj)]
plotCounts(dds, gene = topGene, intgroup=c("dex"))

# MA-plot
library("apeglm")
resultsNames(dds)
res <- lfcShrink(dds, coef="dex_trt_vs_untrt", type="apeglm")
plotMA(res, ylim = c(-5, 5))
hist(res$pvalue[res$baseMean > 1], breaks = 0:20/20,
     col = "grey50", border = "white")

# Gene clustering
library("genefilter")
topVarGenes <- head(order(rowVars(assay(vsd)), decreasing = TRUE), 20)
mat  <- assay(vsd)[ topVarGenes, ]
mat  <- mat - rowMeans(mat)
anno <- as.data.frame(colData(vsd)[, c("cell","dex")])
pheatmap(mat, annotation_col = anno)
```
Annotation
```r
library("AnnotationDbi")
library("org.Hs.eg.db")
res$symbol <- mapIds(org.Hs.eg.db,
                     keys=row.names(res),
                     column="SYMBOL",
                     keytype="ENSEMBL",
                     multiVals="first")
res$entrez <- mapIds(org.Hs.eg.db,
                     keys=row.names(res),
                     column="ENTREZID",
                     keytype="ENSEMBL",
                     multiVals="first")
```
Plotting fold changes in genomic space
```r
resGR <- results(dds, name="dex_trt_vs_untrt", format="GRanges")
resGR$log2FoldChange <- res$log2FoldChange
resGR$symbol <- mapIds(org.Hs.eg.db, names(resGR), "SYMBOL", "ENSEMBL")
```
Removing hidden batch effects
The SVA package uses the term surrogate variables for the estimated variables that we want to account for in our analysis, while the RUV package uses the terms factors of unwanted variation with the acronym “Remove Unwanted Variation” explaining the package title.
```r
library("sva")
dat  <- counts(dds, normalized = TRUE)
idx  <- rowMeans(dat) > 1
dat  <- dat[idx, ]
mod  <- model.matrix(~ dex, colData(dds))
mod0 <- model.matrix(~   1, colData(dds))
svseq <- svaseq(dat, mod, mod0, n.sv = 2)
mod  <- model.matrix(~ dex, colData(dds))
mod0 <- model.matrix(~   1, colData(dds))
svseq <- svaseq(dat, mod, mod0, n.sv = 2)
svseq$sv
par(mfrow = c(2, 1), mar = c(3,5,3,1))
for (i in 1:2) {
  stripchart(svseq$sv[, i] ~ dds$cell, vertical = TRUE, main = paste0("SV", i))
  abline(h = 0)
 }
 # add these two surrogate variables as columns to the DESeqDataSet and then add them to the design:
 ddssva <- dds
ddssva$SV1 <- svseq$sv[,1]
ddssva$SV2 <- svseq$sv[,2]
design(ddssva) <- ~ SV1 + SV2 + dex
```
Use RUVg frp, RUVSeq
We first would run DESeq and results to obtain the p-values for the analysis without knowing about the batches, e.g. just ~ dex. 
```r
library("RUVSeq")
set <- newSeqExpressionSet(counts(dds))
idx  <- rowSums(counts(set) > 5) >= 2
set  <- set[idx, ]
set <- betweenLaneNormalization(set, which="upper")
not.sig <- rownames(res)[which(res$pvalue > .1)]
empirical <- rownames(set)[ rownames(set) %in% not.sig ]
set <- RUVg(set, empirical, k=2)
pData(set)
par(mfrow = c(2, 1), mar = c(3,5,3,1))
for (i in 1:2) {
  stripchart(pData(set)[, i] ~ dds$cell, vertical = TRUE, main = paste0("W", i))
  abline(h = 0)
 }
 ddsruv <- dds
ddsruv$W1 <- set$W_1
ddsruv$W2 <- set$W_2
design(ddsruv) <- ~ W1 + W2 + dex
```
Time course experiments
```r
library("fission")
data("fission")
ddsTC <- DESeqDataSet(fission, ~ strain + minute + strain:minute)
ddsTC <- DESeq(ddsTC, test="LRT", reduced = ~ strain + minute)
resTC <- results(ddsTC)
resTC$symbol <- mcols(ddsTC)$symbol
head(resTC[order(resTC$padj),], 4)
fiss <- plotCounts(ddsTC, which.min(resTC$padj), 
                   intgroup = c("minute","strain"), returnData = TRUE)
ggplot(fiss,
  aes(x = as.numeric(minute), y = count, color = strain, group = strain)) + 
  geom_point() + geom_smooth(se = FALSE, method = "loess") + scale_y_log10()


resultsNames(ddsTC)
res30 <- results(ddsTC, name="strainmut.minute30", test="Wald")
res30[which.min(resTC$padj),]
betas <- coef(ddsTC)
colnames(betas)
topGenes <- head(order(resTC$padj),20)
mat <- betas[topGenes, -c(1,2)]
thr <- 3 
mat[mat < -thr] <- -thr
mat[mat > thr] <- thr
pheatmap(mat, breaks=seq(from=-thr, to=thr, length=101),
         cluster_col=FALSE)