---
title: Quick Start
nav_order: 4
---

<!--
This page demonstrates a minimal end-to-end run for one microbiome feature.
Adapt file paths and argument names to your actual implementation.
-->

# Quick Start

This Quick Start example shows how to run PALM-mbQTL for a single
microbiome feature, assumed to be named `g_Blautia`.

The pipeline consists of:

1. Step 0a: merge abundance and covariates (phenotype preparation)
2. Step 0b: generate a GRM (optional but recommended)
3. Step 1: fit the null Poisson mixed model
4. Step 2: compute genome-wide score statistics

Throughout this page we assume that:

- The PALMmbQTL repository is cloned at `/path/to/PALMmbQTL`.
- Raw input files are under `/path/to/mydata`.
- Output files are written back to `/path/to/mydata/output`.

## 1. Prepare raw abundance and covariate files

Assume you have:

- `abd.tsv` : microbiome abundance matrix
- `cov.tsv` : covariate table with sample IDs

Use [Step 0 – Merge abundance and covariates](step0_merge_pheno.md)
to build a single phenotype file, e.g.:

```text
/path/to/mydata/pheno_all_taxa.txt
```

## 2. Step 0b – Generate a GRM

Using pixi:

```bash
cd /path/to/PALMmbQTL/extdata

pixi run --manifest-path=../pixi.toml   Rscript step0_generateGRM.R   --genoFile=/path/to/mydata/stool_bialleic_merged_data.gds   --grmFile=/path/to/mydata/output/stool_GRM.rds
```

Using Docker:

```bash
docker run --rm -v /path/to/mydata:/data palmmbqtl:latest   step0_generateGRM.R   --genoFile=/data/stool_bialleic_merged_data.gds   --grmFile=/data/output/stool_GRM.rds
```

## 3. Step 1 – Fit null model for `g_Blautia`

```bash
cd /path/to/PALMmbQTL/extdata

pixi run --manifest-path=../pixi.toml   Rscript step1_fitNULL.R   --phenoFile=/path/to/mydata/pheno_all_taxa.txt   --sampleCol=sample_id   --yCol=g_Blautia   --covarCols=age,sex,PC1,PC2   --grmFile=/path/to/mydata/output/stool_GRM.rds   --outFile=/path/to/mydata/output/g_Blautia_step1.rda
```

## 4. Step 2 – Genome-wide score tests

```bash
cd /path/to/PALMmbQTL/extdata

pixi run --manifest-path=../pixi.toml   Rscript step2_scoreTest.R   --inFile=/path/to/mydata/stool_bialleic_merged_data.gds   --GMMATmodelFile=/path/to/mydata/output/g_Blautia_step1.rda   --SAIGEOutputFile=/path/to/mydata/output/g_Blautia_allchr_step2.txt   --LOCO=FALSE
```

After completion, the file `g_Blautia_allchr_step2.txt` should contain
per-SNP statistics and p-values.

For more detailed explanations of each step, see:

- [Step 0 – Merge abundance and covariates](step0_merge_pheno.md)
- [Step 0 – GRM Construction](step0_grm.md)
- [Step 1 – Null Model Fitting](step1.md)
- [Step 2 – Score Tests](step2.md)
