# gibelio-single-sperm
Custom scripts for single-sperm genome analysis in Carassius gibelio: haplotype phasing, chromosome genotyping, recombination/gene-conversion detection, and chromosome-level gonad-enriched expression correlation. 
# Code and Software Submission Package: manuscript data-processing workflows

This package provides the source code, lightweight demo data, expected demo outputs, and reproduction notes for the main data-processing workflows described in the manuscript Methods. The code now covers nearly all computational data-processing steps represented in the manuscript Methods, including read mapping/QC, SNP calling and filtering, chromosome-depth and chromosome-content inference, chromosome phasing, single-sperm chromosome genotyping, recombination/gene-conversion detection, and chromosome-level gonad-enriched expression analysis.

The package is organized for readers and reviewers rather than as a raw project directory. Source code is grouped by analysis module, demo inputs and outputs mirror those modules, and original production-oriented scripts are accompanied by compact runnable examples where full-scale sequencing data are not required.

## 1. Package scope and source-code organization

```text
source_code/
├── HWM.backScipt/
├── genotyping_and_recombination/
└── gonad_enriched/

demo_data/
├── HWM_analysis/
├── genotyping_and_recombination/
└── gonad_enriched/

demo_output/
├── HWM_analysis/
├── genotyping_and_recombination/
└── gonad_enriched/
```

### 1.1 `source_code/HWM.backScipt/`

Production-oriented HWM analysis scripts supporting upstream and intermediate single-sperm analyses:

- FASTQ quality-control job generation;
- BWA-MEM read mapping and BAM processing;
- chromosome-level depth and coverage summaries;
- chromosome-content and ploidy-style inference from normalized depth;
- GATK SNP calling and filtering;
- per-chromosome phasing and haplotype clustering using distance/PCA-style workflows;
- VCF and genotype table transformation utilities;
- LOH/recombination-related downstream summaries and visualization helpers.

Reader-facing logic note: `docs/hwm_analysis_logic.md`.

### 1.2 `source_code/genotyping_and_recombination/`

Compact reference implementation for the Methods sections **Sperm chromosome genotyping** and **Recombination**:

- filtering informative SNPs;
- assigning 10-kb window haplotypes;
- summarizing chromosome-level haplotype composition;
- detecting haplotype-switch recombination events;
- grouping adjacent inconsistent SNPs as candidate gene-conversion regions.

This module is intentionally small and transparent so reviewers can run it directly on the supplied demo data.

Reader-facing logic note: `docs/genotyping_and_recombination_logic.md`.

### 1.3 `source_code/gonad_enriched/`

Custom scripts for chromosome-level gonad-enriched expression analysis:

- merging multi-tissue RNA-seq expression replicates;
- defining testis- and ovary-enriched gene sets;
- summarizing enriched-gene expression at chromosome level;
- deriving chromosome-level metrics such as `Tenr_log2_expr_mean`, `Oenr_log2_expr_mean`, and the symmetric testis-vs-ovary index;
- correlating expression-derived chromosome metrics with sperm chromosome frequency in the full workflow.

Reader-facing logic note: `docs/gonad_enriched_logic.md`.

## 2. Required content checklist

| Checklist item | Status | Location / note |
|---|---|---|
| Compiled standalone software and/or source code | Included | Source code is organized under `source_code/`: `HWM.backScipt/`, `genotyping_and_recombination/`, and `gonad_enriched/`. |
| Small demo dataset | Included | Module-level demo inputs are provided under `demo_data/`; expected outputs are provided under `demo_output/`. |
| README with system requirements, installation, demo and usage instructions | Included | This `README.md`. |
| Reproduction instructions | Included where feasible | See `reproduction_notes.md`. Full reproduction requires the original sequencing data, reference genomes and large intermediate alignment/variant/depth tables. |
| Software license | Included | MIT license in `LICENSE`. |

## 3. System requirements

### Operating system

The analyses are intended for a Linux server/HPC environment. The lightweight demos run on a standard x86_64 Linux workstation.

### Core software dependencies

The full manuscript workflow uses a mixture of external bioinformatics tools and custom scripts. The demo package includes lightweight examples that can be run with Python 3 and Perl; optional R-based plotting/correlation scripts require R and related packages.

Key tools represented in the Methods/source code include:

- BWA-MEM v0.7.17: short-read alignment.
- SAMtools v1.16.1 or compatible: BAM sorting, indexing and duplicate/statistical processing.
- GATK v4.1.2: SNP calling and filtering.
- HISAT2 v2.2.1: RNA-seq read mapping for expression analysis.
- BamDeal v0.27: chromosome-level depth/coverage statistics.
- VCF2Dis, PHYLIPNEW and GCTA: chromosome-level phasing/population-structure workflows.
- Beagle and VCF utilities: genotype and marker processing.
- R with packages such as `ggplot2`, `dplyr` and `clusterProfiler`: plotting, correlation and enrichment analyses.
- Perl/Python/shell utilities: custom filtering, table processing and workflow execution.
- Additional supporting-analysis tools represented in the broader Methods include minimap2, hifiasm, fastp, BLAST, mosdepth and SyRI.

An example `environment.yml` is included. Some specialized tools may need to be installed separately depending on the exact full-scale analysis stage being reproduced.

### Hardware requirements

For the included demo datasets: 2-4 CPU threads and <8 GB RAM should be sufficient.

For full-scale reproduction: resource requirements depend on sample number and genome size. Read alignment, genome-wide variant calling, long-read alignment/assembly and genome-scale plotting typically require a high-memory Linux server or HPC cluster.

### Versions tested

The included demos were tested in the current Linux environment with Python 3 and Perl. R was not available in this package-building environment, so R plotting/correlation steps are documented but not pre-run here. Full manuscript reproduction should record the exact tool versions used for each production run.

Recommended reporting format:

```text
Operating system: Linux x86_64, [distribution/version]
Python: [version]
Perl: [version]
R: [version, if running R steps]
BWA-MEM: 0.7.17
SAMtools: 1.16.1
GATK: 4.1.2
HISAT2: 2.2.1
BamDeal: 0.27
VCF2Dis / PHYLIPNEW / GCTA: [versions]
Additional supporting tools: [versions]
```

## 4. Installation guide

If conda is used:

```bash
conda env create -f environment.yml
conda activate single_sperm_analysis
```

If containerized, provide the Docker/Singularity image and build command.

Typical installation time on a normal Linux workstation is approximately 10-30 minutes for conda environments, excluding large reference/data downloads.

## 5. Demo

The package provides module-level demo data and expected outputs for all three source-code collections.

```text
demo_data/
├── HWM_analysis/
├── genotyping_and_recombination/
└── gonad_enriched/

demo_output/
├── HWM_analysis/
├── genotyping_and_recombination/
└── gonad_enriched/
```

The demo is intentionally small. It verifies software logic and file formats, not full-scale biological conclusions.

### 5.1 Sperm chromosome genotyping and recombination demo

Source code:

`source_code/genotyping_and_recombination/`

Input data:

`demo_data/genotyping_and_recombination/`

Commands:

```bash
python3 source_code/genotyping_and_recombination/genotype_sperm_chromosomes.py \
  --haplotypes demo_data/genotyping_and_recombination/chromosome_haplotypes.tsv \
  --snp-genotypes demo_data/genotyping_and_recombination/demo_sperm_snps.tsv \
  --chrom-sizes demo_data/genotyping_and_recombination/chrom_sizes.tsv \
  --window-size 10000 \
  --min-window-snps 10 \
  --min-depth 6 \
  --max-depth 30 \
  --min-snp-distance 5 \
  --outdir demo_output/genotyping_and_recombination/genotyping

python3 source_code/genotyping_and_recombination/call_recombination.py \
  --window-genotypes demo_output/genotyping_and_recombination/genotyping/window_haplotypes.tsv \
  --snp-haplotypes demo_output/genotyping_and_recombination/genotyping/filtered_snp_haplotypes.tsv \
  --flank-snps 20 \
  --outdir demo_output/genotyping_and_recombination/recombination
```

Expected key outputs:

- `demo_output/genotyping_and_recombination/genotyping/window_haplotypes.tsv`
- `demo_output/genotyping_and_recombination/genotyping/chromosome_calls.tsv`
- `demo_output/genotyping_and_recombination/recombination/recombination_events.tsv`
- `demo_output/genotyping_and_recombination/recombination/gene_conversion_regions.tsv`

### 5.2 Gonad-enriched gene-set analysis demo

Source code:

`source_code/gonad_enriched/`

Input data:

`demo_data/gonad_enriched/`

Commands:

```bash
perl source_code/gonad_enriched/01.merge_tissue_expression.pl \
  demo_data/gonad_enriched/tissue_expression_replicates.tsv \
  demo_output/gonad_enriched/tissue_expression_merged.tsv

perl source_code/gonad_enriched/02.select_specific_OR_enriched.pl \
  --input demo_output/gonad_enriched/tissue_expression_merged.tsv \
  --prefix demo_output/gonad_enriched/gonad_demo \
  --targets T,O \
  --se_min_expr 1 --se_fc_othermean 2 --se_fc_othermax 1.5 --se_tau 0.40 \
  --re_min_expr 1 --re_fc_othermean 1.5 --re_fc_othermax 1.2 --re_tau 0.30

perl source_code/gonad_enriched/03.summarize_gene_sets_by_chr.v2.pl \
  --expr demo_output/gonad_enriched/tissue_expression_merged.tsv \
  --out demo_output/gonad_enriched/chr_gonad_summary.tsv \
  --gene_sets Tenr:demo_output/gonad_enriched/gonad_demo.T.strict_enriched.id.txt,Oenr:demo_output/gonad_enriched/gonad_demo.O.strict_enriched.id.txt \
  --t_col T --o_col O --chr_prefix chr --only_chr 1
```

Expected key outputs:

- `demo_output/gonad_enriched/tissue_expression_merged.tsv`
- `demo_output/gonad_enriched/gonad_demo.T.summary.txt`
- `demo_output/gonad_enriched/gonad_demo.O.summary.txt`
- `demo_output/gonad_enriched/chr_gonad_summary.tsv`

The R plotting/correlation scripts in `source_code/gonad_enriched/` require R and plotting/statistics packages. They are part of the full workflow but are not required for the lightweight Perl demo.

### 5.3 HWM analysis demo

Source code:

`source_code/HWM.backScipt/`

Input data:

`demo_data/HWM_analysis/`

Commands:

```bash
perl source_code/HWM.backScipt/04.Coverage/02.Summary/aggregate_coverage.pl \
  demo_data/HWM_analysis/depth_file_list.txt \
  > demo_output/HWM_analysis/aggregate_coverage_stdout.tsv

perl source_code/HWM.backScipt/05.Ploidy/chr_ploidy_ratio.pl \
  demo_data/HWM_analysis/depth_file_list.txt \
  > demo_output/HWM_analysis/chr_ploidy_ratio_stdout.tsv

perl source_code/HWM.backScipt/07.HapGATK/03.Process/vcf_hap_to_diploid.pl \
  demo_data/HWM_analysis/haploid_genotypes.tsv \
  > demo_output/HWM_analysis/haploid_to_diploid.tsv
```

Expected key outputs:

- `demo_output/HWM_analysis/aggregate_coverage_stdout.tsv`
- `demo_output/HWM_analysis/chr_ploidy_ratio_stdout.tsv`
- `demo_output/HWM_analysis/haploid_to_diploid.tsv`

This demo exercises table-processing and depth-summary utilities from the HWM analysis collection without requiring FASTQ/BAM files or external aligners. The toy values are for software validation only.

See also:

- `demo_data/README.md`
- `demo_output/README.md`

## 6. Instructions for use on full data

Full-scale use follows the same module structure as the demos, replacing toy inputs with production data and intermediate files.

### 6.1 HWM analysis workflow

Typical inputs:

- raw short-read FASTQ files;
- combined or species-specific reference genomes;
- chromosome-size tables;
- BAM/coverage/VCF intermediates generated by upstream steps;
- sample lists and per-chromosome interval files.

Typical outputs:

- QC summaries;
- sorted/marked/indexed BAM files;
- chromosome-depth and depth-ratio matrices;
- filtered SNP/VCF files;
- chromosome-level distance/PCA/phasing outputs;
- haplotype assignment and genotype-format tables.

### 6.2 Sperm chromosome genotyping and recombination workflow

Required inputs:

- chromosome haplotype definitions/phased haplotype table;
- SNP-level genotype and depth table for each sperm sample;
- chromosome-size table;
- optional gene/repeat annotation for downstream breakpoint interpretation.

Key algorithmic steps:

1. Remove SNP sites with identical genotype among chromosome haplotypes or with `N` in a chromosome haplotype.
2. Remove missing SNP sites supported by fewer than six reads.
3. Remove SNP sites with sequencing depth greater than 30x.
4. Remove SNP sites closer than 5 bp to adjacent SNPs.
5. Remove isolated SNPs whose genotype/haplotype assignment conflicts with adjacent SNPs.
6. Assign haplotypes in 10-kb windows when a window contains at least 10 informative SNPs.
7. Identify recombination events from chromosome window haplotype changes.
8. Correct SNP-level haplotypes using 20 upstream and 20 downstream SNPs.
9. Group two or more adjacent inconsistent SNPs into candidate gene-conversion regions.

### 6.3 Gonad-enriched expression workflow

Typical inputs:

- multi-tissue RNA-seq expression matrix, usually RPKM or equivalent;
- gene locations or chromosome assignments;
- chromosome-frequency table from single-sperm analysis.

Typical outputs:

- tissue-level merged expression matrix;
- testis- and ovary-enriched gene sets;
- chromosome-level enriched-gene expression metrics;
- optional correlation and visualization outputs linking expression bias to chromosome frequency.

## 7. Manuscript Methods coverage

The code package covers the main data-processing sections represented in the manuscript Methods, including:

- SNP calling;
- sperm chromosome-content inference;
- phasing;
- sperm chromosome genotyping;
- recombination and candidate gene-conversion detection;
- chromosome-level gonad-enriched expression analysis;
- supporting table-processing, depth-summary, genotype-conversion and plotting-preparation utilities used by the full workflow.

Relevant Methods text is summarized in `methods_relevant_sections.md`.

## 8. Supporting analyses represented by manuscript-associated results

The broader manuscript analysis framework includes:

- single-sperm chromosome distribution and haplotype/genotype summary analyses;
- gene-conversion and recombination-region summaries;
- breakpoint-proximal gene extraction and functional enrichment;
- observed-vs-random breakpoint/gene-distance comparisons;
- regional heterozygosity and repeat-density summaries;
- chromosome presence/absence gene-enrichment summaries;
- paternal/sex-specific sequence alignment, assembly and normalized-depth analyses;
- whole-genome collinearity and putative centromere-position inference.

These components require the original large sequencing datasets, reference genomes and production intermediate files. The included demos validate representative software logic and file formats.

## 9. Code availability and manuscript location

- Local source code: `source_code/HWM.backScipt/`, `source_code/genotyping_and_recombination/`, and `source_code/gonad_enriched/`.
- External code repository/archived DOI: to be provided if available.
- Detailed algorithmic descriptions are in the manuscript Methods; a reader-facing summary is provided in `methods_relevant_sections.md`.

## 10. License

The analysis code is released under the MIT License; see `LICENSE`. Large raw sequencing data and reference genomes should be obtained from the repositories/accessions described in the manuscript Data Availability statement.
