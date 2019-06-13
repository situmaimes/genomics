https://bioinformatics-core-shared-training.github.io/cruk-summer-school-2018/RNASeq2018/html/06_Gene_set_testing.nb.html#
AIM:
It aims to understand which pathways or gene networks the differentially expressed genes are implicated in.

# GSEA analysis
1. ranking all genes in the data set
2. identifying the rank positions of all members of the gene set in the ranked data set
3. calculating an enrichment score (ES) that represents the difference between the observed rankings and that which would be expected assuming a random rank distribution.

## Create ranks
```r
library(fgsea)
load("Robjects/Annotated_Results_LvV.RData")
gseaDat <- filter(shrinkLvV, !is.na(Entrez))
ranks <- gseaDat$logFC
names(ranks) <- gseaDat$Entrez
head(ranks)
```

## Conduct analysis
```r
load("Robjects/mouse_H_v5.RData")
pathwaysH <- Mm.H
fgseaRes <- fgsea(pathwaysH, ranks, minSize=15, maxSize = 500, nperm=1000)
head(fgseaRes[order(padj, -abs(NES)), ], n=10)
plotEnrichment(pathwaysH[["HALLMARK_ESTROGEN_RESPONSE_EARLY"]], ranks)
```

# GO enrichment analysis
```r
library(goseq)
supportedOrganisms() %>% filter(str_detect(Genome, "mm"))
isSigGene <- shrinkLvV$FDR < 0.01 & !is.na(shrinkLvV$FDR)
genes <- as.integer(isSigGene)
names(genes) <- shrinkLvV$GeneID
pwf <- nullp(genes, "mm10", "ensGene", bias.data = shrinkLvV$medianTxLength)
goResults <- goseq(pwf, "mm10","ensGene", test.cats=c("GO:BP"))
goResults %>% 
    top_n(10, wt=-over_represented_pvalue) %>% 
    mutate(hitsPerc=numDEInCat*100/numInCat) %>% 
    ggplot(aes(x=hitsPerc, 
               y=term, 
               colour=over_represented_pvalue, 
               size=numDEInCat)) +
        geom_point() +
        expand_limits(x=0) +
        labs(x="Hits (%)", y="GO term", colour="p value", size="Count")
```

# KEGG pathway enrichment analysis
refer to clusterProfiler