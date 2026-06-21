# Gonad-enriched gene-set analysis: source-code logic

Source-code location in this package:

`source_code/gonad_enriched/`

The source files in that directory are included as part of the supplied source-code collection. This document explains the logic and how the scripts correspond to the manuscript Methods section **Chromosome-level summary of gonad-enriched expression and correlation analysis**.

## Methods scope covered

The Methods section describes:

1. RNA-seq data from adult tissues: testis, ovary, liver, brain, hypothalamus & pituitary, kidney, muscle, spleen and heart, with biological replicates.
2. Reads mapped to the *C. gibelio* reference genome with HISAT2 and summarized as RPKM.
3. A custom Perl workflow defining testis- and ovary-enriched genes based on elevated expression in the target gonad relative to other tissues, with fold-change and FDR filtering.
4. Chromosome-level aggregation of enriched-gene expression.
5. Calculation of chromosome-level metrics:
   - `Tenr_log2_expr_mean`: mean log2(expression + 1) for testis-enriched genes.
   - `Oenr_log2_expr_mean`: mean log2(expression + 1) for ovary-enriched genes.
   - `MI_enriched = z(Tenr_log2_expr_mean) - z(Oenr_log2_expr_mean)`.
6. Correlation and visualization against chromosome frequency in sperm.

## File-level logic

### `01.merge_tissue_expression.pl`

Purpose: merge replicate-level tissue expression into per-tissue mean expression.

Typical input:

- `Tissues_expression.txt`: gene-by-sample RPKM matrix.

Typical output:

- merged expression matrix with one expression value per tissue per gene.

Logic:

1. Parse the expression matrix header to identify tissue replicate columns.
2. For each gene, collect replicate RPKM values for each tissue.
3. Calculate mean expression per tissue.
4. Emit a compact gene-by-tissue matrix for enrichment calling.

### `02.select_specific_OR_enriched.pl`

Purpose: classify genes as tissue-specific or tissue-enriched, especially testis-enriched and ovary-enriched sets.

Key manuscript-relevant logic:

1. Select target gonad: `T` for testis or `O` for ovary.
2. Compare target-gonad expression with expression in non-target reference tissues.
3. Apply enriched-gene criteria such as log2 fold change > 1 and FDR filtering.
4. Output target-gonad enriched gene lists.

Biological meaning:

- This step identifies genes whose transcription is biased toward testis or ovary, forming the gene sets used for chromosome-level summaries.

### `03.summarize_gene_sets_by_chr.v2.pl`

Purpose: map enriched gene sets to chromosomes and calculate chromosome-level expression summaries.

Core outputs:

- chromosome-level number of enriched genes;
- testis-enriched expression metrics;
- ovary-enriched expression metrics;
- fields used by downstream R scripts, including `Tenr_log2_expr_mean` and `Oenr_log2_expr_mean`.

Logic:

1. Read gene-to-chromosome annotation and enriched-gene lists.
2. For each chromosome, collect genes belonging to each gonad-enriched category.
3. Transform expression as log2(expression + 1).
4. Calculate chromosome-level means and counts.
5. Write summary table for correlation analysis.

### `04.chr_correlation_analysis_all.R`

Purpose: broad chromosome-level correlation checks between expression-derived metrics and sperm chromosome frequency.

Logic:

1. Load chromosome summary table.
2. Load chromosome-frequency table from single-sperm analysis.
3. Merge by chromosome identifier.
4. Compute correlations and generate diagnostic plots.

### `04.chr_correlation_analysis_advanced.R`

Purpose: extended/advanced correlation analysis for gonad-enriched metrics.

Logic:

- Similar to the all-correlation script, but with additional models, labels or plotting layers for manuscript-quality interpretation.

### `04.chr_symmetric_main_analysis_Fig4c.R`

Purpose: main figure workflow for the symmetric gonad-enriched index.

Key logic:

1. Standardize chromosome-level testis-enriched expression metric to Z-score.
2. Standardize chromosome-level ovary-enriched expression metric to Z-score.
3. Calculate:

   `MI_enriched = z(Tenr_log2_expr_mean) - z(Oenr_log2_expr_mean)`

4. Correlate `MI_enriched` with sperm chromosome frequency.
5. Generate scatter plots and statistical annotations for the main figure.

Biological meaning:

- A positive `MI_enriched` means a chromosome is more biased toward testis-enriched expression relative to ovary-enriched expression.
- Correlation with sperm chromosome frequency tests whether chromosome transmission is associated with gonadal expression bias.

### `05.chr_symmetric_with_total_controls.R`

Purpose: control analysis to test whether the main correlation is driven by gonad-enriched signal rather than generic chromosome properties.

Controls may include:

- total gene number;
- global chromosome-level transcriptional activity;
- non-gonad or all-gene expression metrics.

### `05.timecourse_chr_freq_correlation_TenrM_OenrF.R`

Purpose: developmental time-course correlation analysis.

Logic:

1. Load developmental expression matrix.
2. Track male/testis-enriched and female/ovary-enriched expression over developmental stages.
3. Correlate stage-specific chromosome-level expression with sperm chromosome frequency.

## Inputs retained in the original source directory

- `Tissues_expression.txt`: multi-tissue expression matrix.
- `development.txt`: developmental expression matrix.
- `chr_freq_in_sperm.txt`: chromosome frequency in sperm.
- `read.me`: original workflow notes from the supplied code collection.

## How this code is represented in the software submission package

- Source and data files are kept under `source_code/gonad_enriched/`.
- This logic note expands the original `read.me` and maps scripts to Methods language.
- Full production reproduction requires the original reference annotation, RNA-seq mapping outputs and exact expression matrix generation steps.


## Demo inputs and outputs

A lightweight demo is included for the Perl stages of this workflow.

- Demo input directory: `demo_data/gonad_enriched/`
- Demo output directory: `demo_output/gonad_enriched/`
- Main demo output: `demo_output/gonad_enriched/chr_gonad_summary.tsv`

The demo validates replicate merging, testis/ovary enriched-gene selection and chromosome-level summarization. R plotting/correlation scripts require an R environment and are documented as optional full-workflow extensions for the demo package.
