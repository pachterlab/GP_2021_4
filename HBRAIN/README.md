### This directory includes running scripts and a notebook to process 10X human hippocampal single-cell ATAC-seq data.
####
#### 1) Download the raw data from GEO database.
```
mkdir GSM4441823_HIP
cd GSM4441823_HIP
fastq-dump --split-files SRR11442501
gzip *fastq
```
#### 2) Download reference genome fasta and gene gtf files to `GSM4441823_HIP` directory for scATAK run.
```
mkdir reference
cd reference
wget http://ftp.ensembl.org/pub/release-101/gtf/homo_sapiens/Homo_sapiens.GRCh38.101.chr.gtf.gz
wget http://ftp.ensembl.org/pub/release-101/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna_rm.primary_assembly.fa.gz
gunzip *gz
cd ..
```
#### 3) Copy Snakemake files and `sample_sheet.csv` from this directory to `GSM4441823_HIP` directory. 
####    Run the following command line for single-cell quantification using scATAK.
```
nohup $SCATAK_HOME/scATAK -module=quant -id=sample_sheet.csv -wlist=737K-cratac-v1.txt \
       -genome=reference/Homo_sapiens.GRCh38.dna_rm.primary_assembly.fa \
       -gene=reference/Homo_sapiens.GRCh38.101.chr.gtf \
       -bc=16 -bf=40 -t=16 > nohup_quant.txt 2>&1 &
```
#### 4) Run the R notebook `benchmark_scATAK__hbrain.Rmd` for the secondary analysis and generate `bc_group.txt` file. 
####
#### 5) Run the following command to generate cell-type specific ATAC-seq track files
```
cd scATAK_out_8cores
nohup $SCATAK_HOME/scATAK -module=track -bg=bc_group.txt -bam=peak_calling/pbmc1.bam -genome=../reference/Homo_sapiens.GRCh38.dna_rm.primary_assembly.fa > nohup_track.txt 2>&1 &
```
