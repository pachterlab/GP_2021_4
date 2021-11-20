### GP_2021_4
####
#### Benchmarking of scATAK for pre-processing tool for single-cel ATAC-seq data.
####
#### Comparison of running speed on a Supermicro server with CentOS7 System installed.
#####
##### PBMC data - total read pairs 224,636,372
#####
| CPU threads | Real time (scATAK) | Real time (Cell Ranger) |
|-------------|--------------------|-------------------------|
|      8      |      64 minutes    |       1171 minutes      |
|      4      |      81 minutes    |       1955 minutes      |
|      2      |     139 minutes    |       3456 minutes      |
#####
#####
##### Adult mouse brain data - total read pairs 244,056,346
#####
| CPU threads | Real time (scATAK) | Real time (Cell Ranger) |
|-------------|--------------------|-------------------------|
|      8      |      72 minutes    |       1045 minutes      |
|      4      |      98 minutes    |       1752 minutes      |
|      2      |     164 minutes    |       3027 minutes      |
#####
