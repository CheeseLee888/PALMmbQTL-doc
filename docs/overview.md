---
title: Overview
nav_order: 2
---

<!--
High-level conceptual overview of the PALM-mbQTL analysis pipeline.
Adapt this page when your methodology or implementation changes.
-->

# PALM-mbQTL Analysis Overview

PALM-mbQTL provides a multi-step workflow for microbiome QTL mapping:

1. **Prepare input data**
   - Genotype (GDS or PLINK)
   - Microbiome abundance table
   - Covariates (demographics, PCs, batch)
   - Optional GRM

2. **Step 0a – Merge abundance and covariates**  
   Build a unified phenotype file with one row per sample.

3. **Step 0b – Construct a GRM (optional)**  
   Generate a genetic relationship matrix from genotype data.

4. **Step 1 – Fit the null model**  
   Fit a Poisson mixed null model for one microbiome feature.

5. **Step 2 – Score tests**  
   Use the fitted null model to compute genome-wide association statistics.

## Model components

### Genotype and GRM

- Genotype data are stored either as:
  - SeqArray/SNP GDS (recommended), or
  - PLINK bed/bim/fam, which can be converted to GDS.
- The GRM summarizes sample relatedness and is used as a random effect.

### Null Poisson mixed model (Step 1)

For a single microbiome feature (e.g. genus `g_Blautia`), PALM-mbQTL fits:

- Fixed effects: covariates such as age, sex, PCs, batch, etc.
- Random effects:
  - Feature-shared random intercept to account for compositional structure.
  - Optional GRM-based random effect to account for sample relatedness.

The fitted model is saved for reuse in Step 2.

### Score tests (Step 2)

Using the fitted null model, PALM-mbQTL computes score statistics and
p-values for each variant. This approach avoids re-fitting the full model
for every SNP and scales to large genotype matrices.

## Input files

You typically need:

- **Abundance (ABD) matrix**

  - One row per sample (or per sample/timepoint).
  - Columns for microbiome features (taxa, OTUs, genes).

- **Covariate (COV) table**

  - One row per sample.
  - Columns for demographics, PCs, batch, etc.

- **Phenotype file**

  - Result of merging ABD and COV.
  - Contains sample ID, feature columns, and covariates.

- **Genotype file**

  - GDS file, or
  - PLINK bed/bim/fam (converted internally to GDS if supported).

- **GRM file** (optional)

  - RDS file containing a genetic relationship matrix and sample IDs.
  - The set and order of sample IDs must match the phenotype file.

Next steps:

- [Installation](installation.md)
- [Quick Start guide](quickstart.md)
