
Assignment 14


# Differential Expression Analysis Pipeline

## Overview
This pipeline performs:
1. Genome and annotation download
2. Quantification of RNA-Seq reads using Salmon
3. Generation of count matrix using featureCounts
4. Differential expression analysis using DESeq2
5. Visualization: PCA plot and heatmap

## Requirements
- Make
- wget
- Salmon
- featureCounts (part of Subread)
- R with DESeq2, ggplot2, and pheatmap

## Usage
Run the entire pipeline:
```bash
make


```bash
make genome 
```

#### Download SRA reads

```bash
make download
```

#### Index genome 

```bash
make index
```

#### Quantify reads

```bash
make quantification
```

#### generate count matrix

```bash
make call_counts
```

#### clean intermediate files

```bash
make clean
```

#### All Steps

```bash
make all
```

