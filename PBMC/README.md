### This directory includes running scripts to pre-process 10X PBMC single-cell ATAC-seq data.
####
#### 1) Download the raw data from 10X website.
```
wget https://cg.10xgenomics.com/samples/cell-atac/1.1.0/atac_v1_pbmc_5k/atac_v1_pbmc_5k_fastqs.tar
tar -xvf atac_v1_pbmc_5k_fastqs.tar
cd atac_v1_pbmc_5k_fastqs
zcat atac_v1_pbmc_5k_S1_L001_R1_001.fastq.gz atac_v1_pbmc_5k_S1_L002_R1_001.fastq.gz | gzip > atac_R1.fq.gz
zcat atac_v1_pbmc_5k_S1_L001_R2_001.fastq.gz atac_v1_pbmc_5k_S1_L002_R2_001.fastq.gz | gzip > atac_R2.fq.gz
zcat atac_v1_pbmc_5k_S1_L001_R3_001.fastq.gz atac_v1_pbmc_5k_S1_L002_R3_001.fastq.gz | gzip > atac_R3.fq.gz
mkdir fastq
mv atac_v1_pbmc_5k*gz fastq/
```
#### 2) Download reference genome fasta and gene gtf files to `atac_v1_pbmc_5k_fastqs` directory for scATAK run.
```
mkdir reference
cd reference
wget http://ftp.ensembl.org/pub/release-101/gtf/homo_sapiens/Homo_sapiens.GRCh38.101.chr.gtf.gz
wget http://ftp.ensembl.org/pub/release-101/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna_rm.primary_assembly.fa.gz
gunzip *gz
cd ..
```
#### 3) Copy Snakemake files and `sample_sheet_benchmark.csv` from `preprocessing` directory to `atac_v1_pbmc_5k_fastqs` directory. 
####    Run the following command lines for single-cell quantification using scATAK (2, 4, 8 threads).
```
nohup snakemake --cores 2 -s Snakefile_2cores_scatak > nohup_scatak_2cores.txt 2>&1 &
nohup snakemake --cores 4 -s Snakefile_4cores_scatak > nohup_scatak_4cores.txt 2>&1 &
nohup snakemake --cores 8 -s Snakefile_8cores_scatak > nohup_scatak_8cores.txt 2>&1 &
```
####    Download `cellranger-atac-1.2.0.tar.gz` and `refdata-cellranger-atac-GRCh38-1.2.0.tar.gz` from https://support.10xgenomics.com/single-cell-atac/software/downloads/1.2/ to `atac_v1_pbmc_5k_fastqs` and uncompress the tar.gz files.
####    Run the following command lines for single-cell quantification using CellRanger (2, 4, 8 threads).
```
nohup snakemake --cores 2 -s Snakefile_2cores_cellranger > nohup_cellranger_2cores.txt 2>&1 &
nohup snakemake --cores 4 -s Snakefile_4cores_cellranger > nohup_cellranger_4cores.txt 2>&1 &
nohup snakemake --cores 8 -s Snakefile_8cores_cellranger > nohup_cellranger_8cores.txt 2>&1 &
```
####
#### 4) Run the R notebook `benchmark_scATAK_cellranger_PBMC_final.Rmd` for the secondary analysis and generate `bc_group.txt` file. 
####
#### 5) Run the following command line to generate cell-type specific ATAC-seq track files
```
cd scATAK_out_8cores
nohup $SCATAK_HOME/scATAK -module=track -bg=bc_group.txt -bam=peak_calling/pbmc1.bam -genome=../reference/Homo_sapiens.GRCh38.dna_rm.primary_assembly.fa > nohup_track.txt 2>&1 &
```
