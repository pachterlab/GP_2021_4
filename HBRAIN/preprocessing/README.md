### This directory includes running scripts to process 10X human hippocampal single-cell ATAC-seq data.
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
#### 5) Run the following command line to generate cell-type specific ATAC-seq track files
```
nohup $SCATAK_HOME/scATAK -module=track -bg=bc_group.txt -bam=peak_calling/HIP1.bam -genome=reference/Homo_sapiens.GRCh38.dna_rm.primary_assembly.fa > nohup_track.txt 2>&1 &
```
#### 6) Run the following command line for cell-type specific HiChIP interactome prediction
```
wget https://ftp.ncbi.nlm.nih.gov/geo/samples/GSM4441nnn/GSM4441836/suppl/GSM4441836_RCLN-HIPP-06-1516-X006-S05-B1-T1_H3K27ac.FitHiChIP.interactions.bed.gz
gunzip GSM4441836_RCLN-HIPP-06-1516-X006-S05-B1-T1_H3K27ac.FitHiChIP.interactions.bed.gz
awk '{if($25<0.05) print}' GSM4441836_RCLN-HIPP-06-1516-X006-S05-B1-T1_H3K27ac.FitHiChIP.interactions.bed > HIP1_interactions.bedpe
nohup $SCATAK_HOME/scATAK -module=hic -bg=bc_group.txt -hic=HIP1_interactions.bedpe -bin=10000 -mtxdir=atac_regions/atac_HIP1 -t=80 > nohup_hic.txt 2>&1 &
```
