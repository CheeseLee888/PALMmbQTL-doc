---
title: Step 0 – Merge abundance and covariates
nav_order: 5
---

<!--
This page documents phenotype preparation: merging microbiome abundance
(ABD) and covariate (COV) tables into a single phenotype file that will
be used by later steps.
-->

# Step 0: Merge abundance and covariates

Before fitting models, PALM-mbQTL expects a **single phenotype file**
containing:

- one row per sample,
- a sample ID column,
- one or more microbiome feature columns (e.g. taxa),
- covariates such as age, sex, PCs, batch, etc.

This file is typically created by merging:

- an abundance (ABD) matrix, and
- a covariate (COV) table,

using the sample ID as the join key.

In the PALMmbQTL repository, this operation is handled by a helper script,
often called:

```text
extdata/step0_mergePheno.R
```

## Input files

A common setup uses:

- **Abundance file** (ABD)

  Example path:

  ```text
  /path/to/mydata/abd.tsv
  ```

  Example structure:

  ```text
  sample_id   g_Blautia  g_Bacteroides  g_Roseburia
  S1          12         30             7
  S2          20         10             3
  S3          18         25             5
  ```

- **Covariate file** (COV)

  Example path:

  ```text
  /path/to/mydata/cov.tsv
  ```

  Example structure:

  ```text
  sample_id   age  sex  PC1   PC2  batch
  S1          35   M    0.12 -0.03 A
  S2          42   F   -0.02  0.11 A
  S3          29   F    0.05  0.00 B
  ```

The `sample_id` column must be present in both tables and uniquely
identify rows.

## Script location

Assuming the script is `extdata/step0_mergePheno.R`, you can view its
usage via:

```bash
cd PALMmbQTL/extdata
pixi run --manifest-path=../pixi.toml Rscript step0_mergePheno.R --help
```

Typical arguments (adapt to your implementation):

- `--abdFile`    : path to the abundance matrix file
- `--covFile`    : path to the covariate table file
- `--sampleCol`  : name of the sample ID column (e.g. `sample_id`)
- `--outFile`    : output phenotype file (tab-delimited)
- `--minCount`   : optional filter on total counts per feature/sample
- `--minSamples` : optional filter on the number of non-zero samples

## Example (pixi)

```bash
cd /path/to/PALMmbQTL/extdata

pixi run --manifest-path=../pixi.toml   Rscript step0_mergePheno.R   --abdFile=/path/to/mydata/abd.tsv   --covFile=/path/to/mydata/cov.tsv   --sampleCol=sample_id   --outFile=/path/to/mydata/pheno_all_taxa.txt
```

## Example (Docker)

```bash
docker run --rm -v /path/to/mydata:/data palmmbqtl:latest   step0_mergePheno.R   --abdFile=/data/abd.tsv   --covFile=/data/cov.tsv   --sampleCol=sample_id   --outFile=/data/pheno_all_taxa.txt
```

## Output

The merged phenotype file `pheno_all_taxa.txt` will have the form:

```text
sample_id   g_Blautia  g_Bacteroides  g_Roseburia  age  sex  PC1   PC2  batch
S1          12         30             7            35   M    0.12 -0.03 A
S2          20         10             3            42   F   -0.02  0.11 A
S3          18         25             5            29   F    0.05  0.00 B
```

This file is then used as `--phenoFile` in
[Step 1 – Null Model Fitting](step1.md).

Make sure that:

- All samples present in the phenotype file also appear in the GRM (if used).
- There are no duplicated sample IDs.
- Covariate columns are correctly typed (numeric vs categorical) in R.
