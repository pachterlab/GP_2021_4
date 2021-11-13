### This directory includes running scripts and a notebook to process 10X PBMC single-cell ATAC-seq data.
####
#### 1) Download the raw data from 10X website.
````
wget https://cg.10xgenomics.com/samples/cell-atac/1.1.0/atac_v1_pbmc_5k/atac_v1_pbmc_5k_fastqs.tar
tar -xvf atac_v1_pbmc_5k_fastqs.tar
cd atac_v1_pbmc_5k_fastqs
zcat atac_v1_pbmc_5k_S1_L001_R1_001.fastq.gz atac_v1_pbmc_5k_S1_L002_R1_001.fastq.gz | gzip > atac_R1.fq.gz
zcat atac_v1_pbmc_5k_S1_L001_R2_001.fastq.gz atac_v1_pbmc_5k_S1_L002_R2_001.fastq.gz | gzip > atac_R2.fq.gz
zcat atac_v1_pbmc_5k_S1_L001_R3_001.fastq.gz atac_v1_pbmc_5k_S1_L002_R3_001.fastq.gz | gzip > atac_R3.fq.gz
mkdir fastq
mv atac_v1_pbmc_5k*gz fastq/
```

#### 2) Copy Snakemake files and other input files from this directory to atac_v1_pbmc_5k_fastqs directory. 
####    Run the following commands for single-cell quantification using scATAK (2, 4, 8 threads).
```
snakemake --cores 2 -s Snakefile_2cores
snakemake --cores 4 -s Snakefile_4cores
snakemake --cores 8 -s Snakefile_8cores
```
####    Run the following commands for single-cell quantification using CellRanger (2, 4, 8 threads).
```
nohup snakemake --cores 2 -s Snakefile_2cores_cellranger > nohup_cellranger_2cores.txt 2>&1 &
nohup snakemake --cores 4 -s Snakefile_4cores_cellranger > nohup_cellranger_4cores.txt 2>&1 &
nohup snakemake --cores 8 -s Snakefile_8cores_cellranger > nohup_cellranger_8cores.txt 2>&1 &
```
