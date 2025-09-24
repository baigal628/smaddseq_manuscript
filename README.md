# SMAdd-seq: Small Molecule Adduct Sequencing
## Probing Chromatin Accessibility with Small Molecule DNA Intercalation and Nanopore Sequencing

## Installation
To install dependencies, use the following command:

```{bash}
conda install -f addseq.yml
```

## Repository Contents
This repository contains scripts and data processing tools for analyzing chromatin accessibility using SMAdd-seq. Below is a breakdown of the directory 
structure:

- ```./scripts/bash/``` - Bash scripts for data preprocessing, model training, and modification prediction using NEMO.
- ```./scripts/``` - Jupyter notebooks for data processing and figure generation:
    - [0-7] - Notebooks for generating data output.
    - [Figure1-3] - Notebooks for generating figures in the manuscript.
    - [Supp_data] - Notebooks for generating supplemental data in the manuscript.
- ```./figures/``` - Figures generated from the analysis.

## Data Preprocessing Pipeline
1. Basecalling
Basecall raw sequencing data using Dorado:

```{bash}
~/dorado-0.7.3-linux-x64/bin/dorado basecaller \
    /private/groups/brookslab/gabai/tools/dna_r9.4.1_e8_sup@v3.6 chrom.pod5 \
    --emit-moves \
    --device cuda:all \
    --reference ./data/ref/sacCer3.fa > chrom.bam
```

2. Quality Control (QC)
Filter primary alignments, sort, and index BAM files using Samtools:

```{bash}
samtools view -b -F SECONDARY,SUPPLEMENTARY chrom.bam | samtools sort -o chrom_pass.sorted.bam
samtools index chrom_pass.sorted.bam
samtools stats chrom_pass.sorted.bam > chrom_pass.sorted.stats
```

3. Event Alignment
Align events to a reference genome using Uncalled4:

```{bash}
uncalled4 align \
    --ref ./data/ref/yst/sacCer3.fa \
    --reads chrom.pod5 \
    --bam-in chrom_pass.sorted.bam \
    -p 8 \
    --eventalign-out chrom_pass.sorted.tsv \
    --eventalign-flags print-read-names,signal-index,samples \
```

## Notes
- Ensure that Dorado and Samtools are properly installed before running the pipeline.
- Modify the paths to match your data storage and reference genome locations.
- The pipeline is optimized for Nanopore sequencing with R9.4.1 flow cells.

## Citation
Bai G, Dhillon N, Felton C, Meissner B, Saint-John B, Shelansky R, Meyerson E, Hrabeta-Robinson E, Hodjat B, Boeger H, Brooks AN. **SMAdd-seq: probing chromatin accessibility with small molecule DNA intercalation and nanopore sequencing.** Nucleic Acids Res. 2025 Jul 19;53(14):gkaf671. doi: 10.1093/nar/gkaf671. PMID: 40682816; PMCID: PMC12276009.


