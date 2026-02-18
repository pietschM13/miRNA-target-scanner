# miRNA-target-scanner
A simple machine learning project for predicting miRNA binding sites in human 3′UTRs.

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

## Week 2 - Seed Region Feature Engineering

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

## Week 3 — TargetScan Integration & Label Construction

Objective:
Integrate predicted miRNA–mRNA interaction data from TargetScan and construct miRNA-level regulatory labels for downstream machine learning modeling.

1. TargetScan Data Processing
  
  Input
    - TargetScan predicted targets (context++ score file)
    - Site-level predictions (one row per miRNA–mRNA interaction)

  Filtering & Cleaning
    - Restricted to human miRNAs (hsa-)
    - Removed suffixes like .1, .2 (duplicate genomic loci)
    - Identified 305 unique miRNAs after cleaning

  Precursor-Level Entries
    8 TargetScan entries were annotated only at the precursor level (e.g., hsa-miR-137) without clear arm specification (-3p/-5p).
    To avoid introducing heuristic arm assignments:
      - These 8 miRNAs were excluded from modeling.
      - One exception: hsa-miR-325-3p was retained due to strong representation in TargetScan and biological evidence in literature.

Total miRNA to left to analyze is 297

2. Label Construction (Aggregation)
  
  TargetScan data is interaction-level. To build a machine learning dataset, interaction-level rows were summarized per miRNA.

  For each miRNA, the following labels were constructed:
    - mean_context_score → Average predicted repression strength across targets.
    - min_context_score → Strongest predicted single interaction (most negative score).
    - n_targets → Number of predicted target sites.

  Result:
    - One row per miRNA
    - 297 rows total

3. Quality Control
  
  Performed checks for:
    - Data types (float for context scores)
    - Missing values (none in label columns)

  Observation:
    - Context++ scores are negative.
    - More negative values correspond to stronger predicted repression.
    - Values below −1 are possible and biologically plausible.

4. Merge with miRBase Features

  miRBase-derived features were merged onto the aggregated label table using exact mature miRNA name matching.

  Included features:
    - sequence
    - seed
    - length
    - GC_content
    - seed_GC_content
    - seed_group_size

  Merge strategy:
    - Left join (TargetScan defines prediction)
    - No 'heuristic' arm assignment
    - Missing miRBase features retained as NaN

  Final dataset:
    - One row per miRNA
    - TargetScan-derived labels
    - miRBase-derived biological features

5. Conceptual Structure

  TargetScan → labels (regulatory behavior)
  miRBase → features (intrinsic sequence properties)

  This separation ensures that the ML model attempts to predict targeting behavior from intrinsic sequence characteristics.


Status at End of Week 3
  - Integrated dataset complete
  - Clean one-row-per-miRNA structure
  - QC completed

## Week 4 — Baseline Modeling and Regularization

### Objective
Evaluate whether intrinsic miRNA sequence features explain variation in predicted repression strength (TargetScan context++ score), and assess model stability using cross-validation and regularization.

Planned tasks:

  1. Final dataset QC & finalize modeling subset
  2. Exploratory statistics (relationships between features & labels)
  3. Define regression task formally
  4. Train baseline linear regression
  5. Evaluate performance (R², MSE)
  6. Interpret coefficients

NOTE: QC showed that features for specially included hsa-miR-325-3p was missing. Features for hsa-miR-325-3p were manually reconstructed from literature-supported mature sequence.

---

### Dataset

- ~300 human miRNAs (after filtering and merging)
- Label: `mean_context_score`
- Features:
  - `length`
  - `GC_content`
  - `seed_GC_content`
  - `seed_group_size`

---

### Models Tested

#### 1. Seed-Only Linear Regression

- Predictor: `seed_GC_content`
- 5-fold cross-validation

**Results**
- CV mean R² ≈ 0.16  
- CV std ≈ 0.37  
- Test r2 = 0.32
- Highly unstable (one strongly negative fold)

**Conclusion**  
Seed GC contains signal but is insufficient alone and variance-sensitive.

---

#### 2. Full OLS (ordinary least squares) Linear Regression

- All intrinsic features included
- 5-fold CV + 80/20 train/test split

**Results**
- CV mean R² ≈ 0.24  
- CV std ≈ 0.28  
- Test R² ≈ 0.41  

**Conclusion**  
Additional features improve explanatory power, but performance remains unstable across folds.

---

#### 3. Ridge Regression (Regularized Model)

- Features standardized
- Alpha tuned via GridSearchCV
- Evaluation using Repeated 5×10 Cross-Validation

Best alpha ≈ 11

**Results**
- Repeated CV mean R² ≈ 0.38  
- CV std ≈ 0.10  
- Test R² ≈ 0.41  

**Conclusion**
Regularization significantly improves stability.  
Repeated cross-validation shows that intrinsic miRNA features explain ~35–40% of variance in predicted repression strength.


---

### Key Observations

- Intrinsic miRNA features explain ~35–40% of variance in mean repression score (based on repeated cross-validation).
- Single 5-fold CV was unstable; repeated 5×10 CV provided a more reliable performance estimate.
- Regularization (Ridge) reduced variance and improved generalization stability.
- Seed GC content contributes meaningful signal but is insufficient alone.
- Model performance is moderate, indicating real but incomplete biological signal in intrinsic features.

---

### Limitations

- Aggregation to miRNA-level reduces sample size (~300 observations).
- Interaction-level features (site type, transcript context) were not modeled.
- TargetScan context++ scores are computational predictions, not experimental measurements.
- Intrinsic sequence features alone likely capture only part of repression biology.
- Even with repeated CV, moderate variance remains due to limited dataset size.


---

### Week 4 Outcome

A stable regression baseline is established.

**Next (Week 5):**
- Alternative ML framing (classification)
- Repeated cross-validation
- Additional biologically meaningful features



