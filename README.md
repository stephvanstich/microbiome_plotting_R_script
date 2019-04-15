**Welcome to the microbiome plot mapping script**

To start, you will need the following files from Qiime analysis workflow:

- An otu table with tqaxa [link to otu_table_mc2_w_tax.biom](otu_table_mc2_w_tax.biom)
- A tree file [link to Rep_set.tre](Rep_set.tre)
- A mapping files[link to AS_map.txt](AS_map.txt)

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
