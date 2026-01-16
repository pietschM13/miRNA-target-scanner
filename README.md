# miRNA-target-scanner
A simple machine learning project for predicting miRNA binding sites in human 3′UTRs.

# miRNA-target-scanner

## Project Overview
This project is a computational biology / bioinformatics learning project focused on human miRNAs and their sequence-derived properties. The long-term goal is to explore how biologically meaningful features (e.g. sequence composition, seed properties) relate to known miRNA–target interactions, while building practical skills in Python, data wrangling, statistics, and machine learning.

The project is intentionally structured as a reproducible, stepwise pipeline, with clear checkpoints and documentation.

---

## Data Source
- **miRBase** (mature miRNA sequences)
- Species: *Homo sapiens*
- Input format: FASTA
- Only mature miRNA sequences are considered at this stage

---

## Current Status — Week 1
**Week 1 focus:** Data preparation, validation, and basic feature engineering.

### Completed in Week 1
- Parsed mature human miRNAs from miRBase
- Converted sequences into a structured pandas DataFrame
- Performed data validation:
  - checked for missing values
  - checked for duplicated miRNA identifiers
  - validated nucleotide alphabet (A/C/G/U)
  - normalized sequences to uppercase
- Engineered basic sequence-derived features:
  - sequence length
  - nucleotide composition (A, C, G, U counts)
  - GC content
- Performed basic exploratory analysis of feature distributions
- Saved a cleaned, feature-augmented dataset for downstream analysis

---

## Repository Structure

