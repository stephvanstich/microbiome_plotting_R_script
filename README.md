**Welcome to the microbiome plot mapping script**

To start, you will need the following files from Qiime analysis workflow:

- An otu table with tqaxa [otu_table_mc2_w_tax.biom](./otujson.biom.zip) (if you are downloading it from this set of data, unzip it ;-) )
- A tree file [Rep_set.tre](./rep_set.tre)
- A mapping files [AS_map.txt](./AS_map.txt)

You may need to convert your biom table in a json biom table. For that, at the end of qiime pipeline, in terminal(this only work in Qiime- old and new version):
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
Import file in phyloseq:

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

Here is the worflow that you can go through to visualise your data. Each of the sections are detailed below.

- Make the Tree [mothers'tree](mothers_tree_top_50.pdf) [pups'tree](pups tree top 50.pdf)
- Plotting the alpha-diversity (see example for [mothers'](../mothers alpha diversity observed.pdf) or [pups'](../pups alpha diversity observed.pdf))
- Plotting the beta-diversity (PCoA plot)(see example for [mothers'](../PCoA mothers.pdf) or [pups'](../PCoA pups.pdf))
- Mapping the Network maps (see example for [mothers'](../Mothers networks.pdf) or [pups'](../Pups network.pdf))
- Mapping the coorelation (see example [here](Correlation btw otu mothers and pups.pdf))

