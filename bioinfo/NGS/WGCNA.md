http://www.bio-info-trainee.com/2535.html
WGCNA 加权基因共表达网络分析
Weighted correlation network analysis

需要表型和对应每个细胞的基因表达谱
> head(datTraits)  ## 56 个细胞系的分类信息，表型
                  gsm cellline       subtype
GSM1172844 GSM1172844    184A1 Non-malignant
GSM1172845 GSM1172845    184B5 Non-malignant
GSM1172846 GSM1172846    21MT1         Basal
GSM1172847 GSM1172847    21MT2         Basal
GSM1172848 GSM1172848     21NT         Basal
GSM1172849 GSM1172849     21PT         Basal
> fpkm[1:4,1:4]  ## 56个细胞系的36953个基因的表达矩阵
                GSM1172844 GSM1172845 GSM1172846  GSM1172847
ENSG00000000003   95.21255   95.69868   19.99467  65.6863763
ENSG00000000005    0.00000    0.00000    0.00000   0.1492021
ENSG00000000419  453.20831  243.64804  142.05818 200.4131493
ENSG00000000457   18.10439   26.56661   16.12776  12.0873135

```r
library(WGCNA)
​
powers = c(c(1:10), seq(from = 12, to=20, by=2))
# Call the network topology analysis function
sft = pickSoftThreshold(datExpr, powerVector = powers, verbose = 5)
#设置网络构建参数选择范围，计算无尺度分布拓扑矩阵
​
# Plot the results:
##sizeGrWindow(9, 5)
par(mfrow = c(1,2));
cex1 = 0.9;
# Scale-free topology fit index as a function of the soft-thresholding power
plot(sft$fitIndices[,1], -sign(sft$fitIndices[,3])*sft$fitIndices[,2],
       xlab="Soft Threshold (power)",ylab="Scale Free Topology Model Fit,signed R^2",type="n",
       main = paste("Scale independence"));
text(sft$fitIndices[,1], -sign(sft$fitIndices[,3])*sft$fitIndices[,2],
       labels=powers,cex=cex1,col="red");
# this line corresponds to using an R^2 cut-off of h
abline(h=0.90,col="red")
# Mean connectivity as a function of the soft-thresholding power
plot(sft$fitIndices[,1], sft$fitIndices[,5],
       xlab="Soft Threshold (power)",ylab="Mean Connectivity", type="n",
       main = paste("Mean connectivity"))
text(sft$fitIndices[,1], sft$fitIndices[,5], labels=powers, cex=cex1,col="red")

net = blockwiseModules(
                 datExpr,
                 power = sft$powerEstimate,
                 maxBlockSize = 6000,
                 TOMType = "unsigned", minModuleSize = 30,
                 reassignThreshold = 0, mergeCutHeight = 0.25,
                 numericLabels = TRUE, pamRespectsDendro = FALSE,
                 saveTOMs = TRUE,
                 saveTOMFileBase = "AS-green-FPKM-TOM",
                 verbose = 3
 )
 table(net$colors)

 # Convert labels to colors for plotting
mergedColors = labels2colors(net$colors)
table(mergedColors)
# Plot the dendrogram and the module colors underneath
plotDendroAndColors(net$dendrograms[[1]], mergedColors[net$blockGenes[[1]]],
                    "Module colors",
                    dendroLabels = FALSE, hang = 0.03,
                    addGuide = TRUE, guideHang = 0.05)
## assign all of the gene to their corresponding module 
## hclust for the genes.