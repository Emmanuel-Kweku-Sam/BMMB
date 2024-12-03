SHELL := /bin/bash

# Variables
SAMPLES := $(shell cut -d, -f1 design.csv | tail -n +2)
GENOME_ACC := ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/005/845/GCF_000005845.2_ASM584v2/GCF_000005845.2_ASM584v2_genomic.fna.gz
GENOME := GCF_000005845.2_ASM584v2_genomic.fna

.PHONY: all genome download index align call_variants merge_vcf clean usage

# Main workflow
all: genome download index align call_variants merge_vcf

# Usage information
usage:
	@echo "Usage:"
	@echo "  make genome          - Download and prepare the reference genome"
	@echo "  make download        - Download SRA reads"
	@echo "  make index           - Index the genome"
	@echo "  make align           - Align reads to the genome"
	@echo "  make call_variants   - Call variants and generate VCF files"
	@echo "  make merge_vcf       - Merge all VCF files into a single file"
	@echo "  make clean           - Remove intermediate files"

# Download the genome
genome:
	wget -N $(GENOME_ACC)
	gunzip -f $(GENOME).gz

# Download FASTQ files from SRA
download:
	@echo "Downloading samples..."
	echo "$(SAMPLES)" | parallel fastq-dump --split-files --gzip {}

# Index the genome
index: genome
	samtools faidx $(GENOME)
	bwa index $(GENOME)

# Align reads for all samples
align:
	@echo "Aligning samples..."
	echo "$(SAMPLES)" | parallel 'bwa mem $(GENOME) <(gunzip -c {}_1.fastq.gz) <(gunzip -c {}_2.fastq.gz) | samtools view -bS - | samtools sort -o {}_aligned.bam; samtools index {}_aligned.bam'

# Call variants for all samples
call_variants:
	@echo "Calling variants..."
	echo "$(SAMPLES)" | parallel 'bcftools mpileup -Ou -f $(GENOME) -Q 10 {}_aligned.bam | bcftools call -c -Oz -o {}_variants.vcf.gz; bcftools index {}_variants.vcf.gz'

# Merge all VCF files into a single file
merge_vcf:
	bcftools merge -Oz -o merged_variants.vcf.gz $(shell echo $(SAMPLES) | sed 's/ /\_variants.vcf.gz /g')
	bcftools index merged_variants.vcf.gz

# Clean intermediate files
clean:
	rm -f *_1.fastq.gz *_2.fastq.gz *_aligned.bam *_aligned.bam.bai *_variants.vcf.gz *_variants.vcf.gz.csi
	rm -f $(GENOME).gz
	rm -f merged_variants.vcf.gz merged_variants.vcf.gz.csi
