# Bisulfite Sequencing Analysis using Bismark

This repository contains the workflow, scripts, and results of a DNA methylation analysis performed using **Bismark** on bisulfite-treated sequencing data.

## 🧬 Overview

The goal of this project is to align bisulfite sequencing reads to a reference genome and extract methylation information.

---

## 📁 Project Structure

```
├── data/                   # Raw input FASTQ files
├── reference/              # Reference genome and Bismark index
├── alignment/             # Bismark alignment outputs
├── methylation_calls/     # Methylation extraction results
├── scripts/               # All bash and helper scripts used
└── README.md              # This file
```

---

## 🔧 Software & Environment

- **Bismark** v0.23.1
- **Bowtie2** v2.5+
- **Samtools** v1.15
- **FastQC** v0.11.9
- OS: Ubuntu 20.04

### Create environment (if needed)
```bash
conda create -n bismark_env bismark bowtie2 samtools fastqc
conda activate bismark_env
```

---

## 🧪 Workflow

### 1. Quality Control
```bash
fastqc data/sample_R1.fastq.gz data/sample_R2.fastq.gz
```

### 2. Prepare Reference Genome
```bash
bismark_genome_preparation --bowtie2 reference/
```
### What it does:
- Indexes the reference genome using Bowtie2.
- Converts the genome into a bisulfite-converted version for alignment.

### Output:
- Bisulfite genome indices stored in the specified genome folder.

---

### 3. Alignment
```bash
bismark --genome reference/ -1 data/sample_R1.fastq.gz -2 data/sample_R2.fastq.gz -o alignment/
```
### What it does:
- Aligns bisulfite-treated reads to the reference genome.
- Supports both single-end and paired-end reads.

### Output:
- `sample_R1_bismark_bt2_pe.bam`: BAM file with aligned reads.
- `sample_R1_bismark_bt2_PE_report.txt`: Alignment report.

---

### 4. Deduplication (if paired-end)
```bash
deduplicate_bismark --bam alignment/sample_R1_bismark_bt2_pe.bam
```
### What it does:
- Removes PCR duplicates from the aligned BAM file.

### Output:
- `sample_R1_bismark_bt2_pe.deduplicated.bam`: Deduplicated BAM file.
- `deduplication_report.txt`: Summary report of duplicate removal.

---

### 5. Methylation Extraction
```bash
bismark_methylation_extractor \
  --paired-end \
  --comprehensive \
  --bedGraph \
  --counts \
  --cytosine_report \
  --CX \
  --genome_folder /path/to/bismark_genome_folder \
  alignment/sample_R1_bismark_bt2_pe.deduplicated.bam
```
### What it does:
- Extracts methylation calls from the BAM file.
- Produces methylation reports in various formats.

### Output:
- `CpG_context_*.txt.gz`, `CHG_context_*.txt.gz`, `CHH_context_*.txt.gz`: Context-specific methylation files.
- `sample.bedGraph.gz`: Coverage and methylation percentages.

---

### 6. Summary Report
```bash
bismark2report
```
### What it does:
- Generates an interactive HTML report summarizing alignment, deduplication, and methylation stats.

### Output:
- `bismark_summary_report.html`: HTML summary for all samples.

---

## 📊 Output

- `*.bam`: Aligned reads
- `*.bismark.cov.gz`: Methylation calls
- `*.bedGraph.gz`: Coverage file for visualization
- `bismark_report.html`: Summary report
- `M-bias` plots: Quality control for methylation extraction

---

## 📌 Notes

- Ensure your FASTQ reads are properly trimmed before aligning.
- Always validate alignment efficiency and duplication rates.
- M-bias plots help assess biases at 5' and 3' ends.

---
## Citation

If you use Bismark in your analysis, please cite:

Krueger F. & Andrews S.R. (2011). *Bismark: a flexible aligner and methylation caller for Bisulfite-Seq applications.* Bioinformatics.

---

## 📞 Contact

For any queries, please contact **Ismam Ahmed Protic**  
Email: [Iprotic@unr.edu]  

---
