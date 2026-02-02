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

## Week 1
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

## Week 2: Seed Region Feature Engineering

Objective: Engineer biologically motivated seed-region features from mature human miRNA sequences to capture core sequence determinants of miRNA–mRNA targeting.

Planned Tasks

- Define the miRNA seed region using canonical conventions
- Extract seed sequences from mature miRNAs
- Engineer seed-derived features:
      -seed sequence (string)
      -seed length (QC)
      -seed GC content
      -seed nucleotide composition
- Group miRNAs by identical seed sequence as a proxy for miRNA families
- Perform basic QC and exploratory summaries
- Save an updated, feature-augmented dataset

Assumptions:

- The miRNA seed region is approximated as nucleotides 2–8 of the mature miRNA sequence. 
- This definition captures the dominant sequence specificity underlying most canonical miRNA–mRNA interactions. 
- Seed-derived features, such as GC content and nucleotide composition, are treated as intrinsic properties of miRNAs, independent of specific mRNA targets.
- I.e. we ask for a given miRNA, what sort of targeting behaviour does it project? 

Limitations

- miRNA binding sites are defined on the mRNA side, and a single miRNA can interact with different targets via different
canonical site types (e.g. 6mer, 7mer-A1, 8mer). 
- This analysis does not explicitly model mRNA sequence context, target-site access (secondary structures), site class, etc.
- Non-canonical and context-dependent interactions are therefore not captured at this stage.

These initial simplifications allow us to do some managable data testing and modelling without having to immediately resort to complex features of miRNA-mRNA interactions. Later, we can add mRNA-dependent features, complicating the model, but bringing it closer to the biological nature

!Important!
Although many miRNAs are evolutionarily conserved, seed-sharing miRNAs in this analysis are defined purely by sequence identity in the seed region and are intended to capture functional similarity in target recognition rather than evolutionary relatedness.

