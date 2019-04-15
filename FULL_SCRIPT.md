# **Welcome to the microbiome plot mapping script**

To start, you will need the following files from Qiime analysis workflow:

- An otu table with tqaxa [otu_table_mc2_w_tax.biom](./otujson.biom.zip) (if you are downloading it from this set of data, unzip it ;-) )
- A tree file [Rep_set.tre](./rep_set.tre)
- A mapping files [AS_map.txt](./AS_map.txt)

You may need to convert your biom table in a json biom table. For that, at the end of qiime pipeline, in terminal (this only work in Qiime - old and new version):
```
biom convert -i otu_table_mc2_w_tax.biom -o otujson.biom --table-type="OTU table" --to-json
```
Create a folder R in your Documents and copy and paste the following file: otujson.biom, AS_map.txt, Rep_set.tre

Open R studio and install the necessary packages:

To quickly install phyloseq:
```
if(!requireNamespace("BiocManager")){install.packages("BiocManager")} 
BiocManager::install("phyloseq")
install.packages("plyr")
install.packages("scales")
install.packages("ggplot2")
install.packages("Hmisc")
install.packages("corrplot")
```

Launch packages:
```
library("phyloseq")
library("ggplot2")
library("scales")
library("grid")
library("plyr")
library("Hmisc")
library(corrplot)
```

Set environment:

```
getwd()
setwd("Documents/R")
getwd()
```

Check the list of files imported in this folder
```
list.files()
```

Set up a vector called colors for graphics:
```
colors<-c("Control" = "#17202a", "ADI1x" = "#f39c12", "ADI2x" = "#e74c3c", "Taxa" = "#5499c7", "M" = "#035356", "P" = "#0fbec4")

colorstaxa<-c("Anaeroplasmataceae" = "#fffe63" , "Erysipelotrichaceae" = "#6100fd", "Lachnospiraceae" = "#2c9ceb" , "Lactobacillaceae" = "#66cafd" , "Rikenellaceae" = "#19bf63" , "Ruminococcaceae" = "#0a5bb3" , "S24-7" = "#21fe80" , "Verrucomicrobiaceae" = "#fb006d")
```
## **Import file in phyloseq**

```
otufile="otujson.biom"
biomfile=import_biom(otufile,parseFunction=parse_taxonomy_greengenes)
tree=read_tree("rep_set.tre")
mapfile="AS_map.txt" 
map=import_qiime_sample_data(mapfile)
```
Merge those file and call it "testdata":

```
testdata=merge_phyloseq(biomfile,tree,map)
print(testdata)
```
## What can you do?

Here is the worflow that you can go through to visualise your data. Each of the sections are detailed below.

- Making the [Tree](https://github.com/stephvanstich/microbiome_plotting_R_script/blob/master/FULL_SCRIPT.md#tree) (see examples for [mothers](tree_mothers.pdf) & [pups](tree_pups.pdf))
- Plotting the [alpha-diversity](https://github.com/stephvanstich/microbiome_plotting_R_script/blob/master/FULL_SCRIPT.md#alpha-diversity-observed) (see example for [mothers](alpha_diversity_mothers.pdf) & [pups](alpha_diversity_pups.pdf))
- Plotting the [beta-diversity](https://github.com/stephvanstich/microbiome_plotting_R_script/blob/master/FULL_SCRIPT.md#pcoa-plot)(PCoA plot)(see example for [mothers](PCoA_mothers.pdf) or [pups](PCoA_pups.pdf))
- Mapping the [coorelation](https://github.com/stephvanstich/microbiome_plotting_R_script/blob/master/FULL_SCRIPT.md#correlation) (see example [here](correlation_otu.pdf))
- Drawing a [heatmap](https://github.com/stephvanstich/microbiome_plotting_R_script/blob/master/FULL_SCRIPT.md#heatmap) (see examples for [mothers](heatmap_mothers.pdf) & [pups](heatmap_pups.pdf))
- Mapping the diversity network (see example for [mothers](network_mothers.pdf) & [pups](network_pups.pdf))


## **Pruning the data**
```
testdata.mothers= subset_samples(testdata, Description2!="P")
```
Prune the data to get only Pups
```
testdata.pups= subset_samples(testdata, Description2!="M")
```

Prune the data to get only the 100/50 more abundant taxa in the OTU (use for rest)
```
Filtertaxa100.mothers=prune_taxa(names(sort(taxa_sums(testdata.mothers), TRUE)[1:100]), testdata.mothers)

Filtertaxa100.pups=prune_taxa(names(sort(taxa_sums(testdata.pups), TRUE)[1:100]), testdata.pups)

Filtertaxa50.mothers=prune_taxa(names(sort(taxa_sums(testdata.mothers), TRUE)[1:50]), testdata.mothers)

Filtertaxa50.pups=prune_taxa(names(sort(taxa_sums(testdata.pups), TRUE)[1:50]), testdata.pups)
```
Compare the number of taxa of your original table(testdata) vs new pruned  table(Filtertaxa):
```
ntaxa(testdata)
ntaxa(Filtertaxa100.mothers)
```

## **Tree**
```
pdf("mothers tree top 50.pdf", width = 20)
plot_tree(Filtertaxa50.mothers, nodelabf =  nodeplotblank, color = "Description", label.tips = "Family", shape= "Phylum", plot.margin = 0.1, ladderize = TRUE) + scale_colour_manual(values = colors)
dev.off()
```
```
pdf("pups tree top 50.pdf", width = 20)
plot_tree(Filtertaxa50.pups, nodelabf =  nodeplotblank, color = "Description", label.tips = "Family", shape= "Phylum", plot.margin = 0.1, ladderize = TRUE) + scale_colour_manual(values = colors)
dev.off()
```
## **Alpha-diversity (Observed)**
```
pdf("mothers alpha diversity observed.pdf", width = 10)
plot_richness(testdata.mothers, x = "Description", color = "Description", measures= "Observed") + geom_point(size=7, alpha=0.7) + scale_colour_manual(values = colors) + geom_boxplot(aes(fill = Description), alpha = 0.5,size = 1) +scale_fill_manual(values = colors) + theme(text = element_text(size=30)) +xlab("")+      scale_x_discrete(limits=c("Control","ADI1x","ADI2x"))
dev.off()
```
```
pdf("pups alpha diversity observed.pdf", width = 10)
plot_richness(testdata.pups, x = "Description", color = "Description", measures= "Observed") + geom_point(size=7, alpha=0.7) + scale_colour_manual(values = colors) + geom_boxplot(aes(fill = Description), alpha = 0.5,size = 1) +scale_fill_manual(values = colors) + theme(text = element_text(size=30)) +xlab("") + scale_x_discrete(limits=c("Control","ADI1x","ADI2x"))
dev.off()
```

## **Beta-diverstity (PCoA plot)**

Use raw data(testdata) or filtered data (filtertaxa100)
```
ordination.mothers = ordinate(testdata.mothers, method = "PCoA")
```
```
pdf("PCoA mothers.pdf", width = 10)
plot_ordination(testdata.mothers, ordination.mothers, type = "SampleID", color = "Description") + stat_ellipse(geom = "polygon", alpha = 0.2, size = 2 ,linetype =2, aes(fill = Description, color = Description)) + geom_point(size = 10) + theme(text = element_text(size=30)) + scale_color_manual( values = colors) + scale_fill_manual ( values=colors)
 dev.off()
```
```
ordination.pups = ordinate(testdata.pups, method = "PCoA")
```
```
pdf("PCoA pups.pdf", width = 10)
plot_ordination(testdata.pups, ordination.pups, type = "SampleID", color = "Description") + stat_ellipse(geom = "polygon", alpha = 0.2, size = 2 ,linetype =2, aes(fill = Description, color = Description)) + geom_point(size = 10) + theme(text = element_text(size=30)) + scale_color_manual( values = colors) + scale_fill_manual ( values=colors)
dev.off()
```
## **Correlation**

Prepare an otu_table_L6.txt using the attached [template](otu_table_L6.txt).

Import Out table:
```
otu.table <- read.table("otu_table_L6.txt", header = TRUE, row.names = 1, check.names=F)
```
Correlation matrix:
```
corr <- cor(otu.table, method= "spearman")
corr.matrix.otu<- rcorr(as.matrix(otu.table))
corr.matrix.otu
```
Extract r value:
```
r.value<-corr.matrix.otu$r
p.value<-corr.matrix.otu$P
```

Plot the correlation:
```
pdf("Correlation btw otu mothers and pups.pdf")
corrplot(corr, sig.level = 0.01, insig = "blank", tl.col = "black")
dev.off()
```
## **Heatmap**
```
pdf("Mothers heatmap.pdf", width = 20)
plot_heatmap(Filtertaxa100.mothers, "NMDS", "bray", "Description", "Class", sample.order = "Description", taxa.order = "Phylum")
dev.off()
```
```
pdf("Pups heatmap.pdf", width = 20)
plot_heatmap(Filtertaxa100.pups, "NMDS", "bray", "Description", "Class", sample.order = "Description", taxa.order = "Phylum")
dev.off()
```

## **Distance network plot**

```
ig = make_network(testdata.mothers, max.dist = 0.85)
```
```
pdf("Mothers network.pdf", width = 20)
plot_network(ig, testdata.mothers, color = "Description", line_weight = 0.4, label = NULL) + scale_colour_manual(values = colors)
dev.off()
```
```
ig = make_network(testdata.pups, max.dist = 0.85)
```
```
pdf("Pups network.pdf", width = 20)
plot_network(ig, testdata.pups, color = "Description", line_weight = 0.4, label = NULL) + scale_colour_manual(values = colors)
dev.off()
```
