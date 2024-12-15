SHELL := /bin/bash

# Variables
DESIGN_FILE := design.csv
COUNTS_FILE := gene_counts.txt
GENOME := GCF_000005845.2_ASM584v2_genomic.fna
GTF := Homo_sapiens.GRCh38.110.gtf
OUTPUT_DIR := results
SAMPLES := $(shell cut -d, -f1 $(DESIGN_FILE) | tail -n +2)

.PHONY: all genome index quantification counts analysis clean

# Main workflow
all: genome index quantification counts analysis

# Download genome and annotation
genome:
	@echo "Downloading genome and annotation..."
	wget -N ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/005/845/GCF_000005845.2_ASM584v2/$(GENOME).gz
	wget -N ftp://ftp.ensembl.org/pub/release-110/gtf/homo_sapiens/$(GTF).gz
	gunzip -f $(GENOME).gz $(GTF).gz

# Index genome 
index: genome
	@echo "Indexing genome with Salmon..."
	mkdir -p salmon_index
	salmon index -t $(GENOME) -i salmon_index --type quasi -k 31

# Quantify reads 
quantification: index
	@echo "Quantifying reads..."
	parallel 'salmon quant -i salmon_index -l A -1 {}_1.fastq.gz -2 {}_2.fastq.gz -p 8 -o $(OUTPUT_DIR)/{}_quant' ::: $(SAMPLES)

# Generate count matrix 
counts:
	@echo "Generating count matrix..."
	featureCounts -a $(GTF) -o $(COUNTS_FILE) -T 8 -g gene_id *.bam

# Differential expression and plots
analysis:
	@echo "Running differential expression analysis..."
	Rscript differential_expression.R

# Clean intermediate files
clean:
	rm -rf salmon_index *.bam *.bai $(GENOME) $(GTF) $(OUTPUT_DIR) $(COUNTS_FILE)
