# HWM analysis: source-code logic

Source-code location in this package:

`source_code/HWM.backScipt/`

The source files in that directory are included as part of the supplied source-code collection. This document explains the logic and maps the code collection to the HWM analysis workflow components:

- **SNP calling**
- **Sperm chromosome contents**
- **Phasing**

The same original code collection also supports single-sperm genotyping and recombination analyses described elsewhere in the Methods.

## HWM analysis workflow components

### SNP calling

The Methods section states that:

1. BWA-MEM v0.7.17 maps T7 reads to combined zebrafish + *Carassius* references.
2. SAMtools v1.16.1 sorts BAM files and marks duplicates.
3. GATK v4.1.2 HaplotypeCaller calls SNPs with ploidy setting appropriate to the analysis.
4. GATK VariantFiltration and downstream custom filters remove low-quality, low-depth, high-depth and high-missingness sites.

### Sperm chromosome contents

The Methods section states that:

1. Read depths are calculated per chromosome with BamDeal v0.27.
2. Depth is normalized by the average zebrafish-genome depth in the same sample.
3. Relative chromosome depth is used to classify chromosome state as missing, haploid, diploid or triploid.
4. Depth-based chromosome composition is refined using genotype/haplotype information.

### Phasing

The Methods section states that:

1. Haploid-chromosome SNPs are used for population-structure analysis.
2. VCF2Dis + PHYLIPNEW and GCTA + R/ggplot2 pipelines cluster sperm samples per chromosome.
3. Clustering resolves two haplotype groups in *C. auratus* and three groups in *C. gibelio*.
4. Linkage SNP information is phased into chromosome haplotypes in somatic cells.

## Directory-level logic in `HWM.backScipt/`

### `01.Reference/`

Reference-level preprocessing.

- `ref_gc_content.pl`: calculates GC content of reference sequences/scaffolds.

Meaning:

- Provides reference covariates for QC or depth-related interpretation.

### `02.FastqQC/`

FASTQ-level quality-control scripts for sperm, egg and MDA/sample batches.

Logic:

1. Run sample-level read-quality statistics.
2. Produce per-sample QC summaries before mapping.
3. Keep sample-specific shell scripts generated for the original dataset.

### `03.Mapping/`

BWA-MEM mapping and BAM-processing workflow.

Relevant scripts include:

- `01.Generators/generate_sperm_mapping_jobs.pl`: generates mapping jobs for sperm samples.
- `01.Generators/generate_mda_mapping_jobs.pl`: generates mapping jobs for MDA samples.
- sample-level `*.sh` scripts under `02.Sperm/`, `03.Egg/` and `04.MDA/`.

Logic:

1. Read sample-to-FASTQ lists.
2. Generate BWA-MEM commands.
3. Sort/fix/mark duplicates and index BAM files.
4. Generate BAM QC and depth/statistics outputs.

Reader-facing mapping:

- Supports the BWA-MEM and SAMtools processing steps used before SNP calling.

### `04.Coverage/`

Depth, coverage and repeat-region coverage analysis.

Relevant script categories:

- per-sample depth counting;
- chromosome-level depth aggregation;
- sliding-window depth scans;
- repeat-region coverage summaries.

Logic:

1. Extract per-base or per-window read depth from BAM files.
2. Summarize depth by chromosome or target interval.
3. Normalize and format outputs for ploidy inference and plotting.

Reader-facing mapping:

- Supports depth-derived chromosome composition and sperm chromosome-content inference.

### `05.Ploidy/`

Ploidy and chromosome-content inference.

Relevant scripts:

- `depth_ratio_ploidy.pl`
- `chr_ploidy_ratio.pl`
- `heterozygosity_window.pl`
- `ref_gc_window.pl`

Logic:

1. Calculate chromosome-level depth ratio relative to the sample baseline.
2. Convert normalized depth into chromosome-copy-state categories.
3. Summarize heterozygosity and GC windows for QC/context.

Reader-facing mapping:

- Implements the depth-ratio logic used to classify missing, haploid, diploid and triploid chromosome states.

### `06.GATK/`

GATK variant-calling scripts.

Relevant scripts:

- `01.HaplotypeCaller/gatk_hc_sperm_S56.sh`
- `01.HaplotypeCaller/gatk_hc_egg_S16.sh`
- `02.Filter/filter_vcf_by_positions.pl`
- `02.Filter/poslist_to_bed.pl`

Logic:

1. Run GATK HaplotypeCaller on selected sample groups.
2. Convert positions to BED/interval files.
3. Filter VCF sites for downstream haplotype analysis.

Reader-facing mapping:

- Supports SNP calling and site filtering.

### `07.HapGATK/`

First-generation haplotype-analysis workflow.

Substeps:

1. Prepare sample lists and chromosome BED intervals.
2. Run per-chromosome GATK HaplotypeCaller.
3. Apply VCF filtering, depth filtering, missingness filtering and SNP/InDel separation.
4. Convert or merge genotype formats.
5. Assign haplotypes and calculate binned haplotype frequencies.
6. Generate PCA plots and RectChr visualizations.

Reader-facing mapping:

- Supports phasing because haplotype groups are inferred from SNP patterns.
- Also supports single-sperm chromosome genotyping.

### `08.H2GATK/`

Refined haplotype-analysis workflow.

Relevant scripts:

- `02.Distance/run_vcf2dis.sh`
- `02.Distance/build_hap_presence_matrix.pl`
- `03.Assign/dist_matrix_to_hap.pl`
- `03.Assign/annotate_vcf_with_hap.pl`
- `03.Assign/sample_hap_distribution.pl`

Logic:

1. Convert VCF sites into interval/marker data.
2. Build haplotype presence/absence matrices.
3. Run VCF2Dis to calculate genetic distances.
4. Convert distance matrices into haplotype assignments.
5. Annotate VCF records with haplotype information.
6. Summarize sample-level haplotype distribution.

Reader-facing mapping:

- Directly supports phasing, especially VCF2Dis-based clustering and haplotype assignment.

### `09.LOH/`

Loss-of-heterozygosity and recombinant-block detection helpers.

Logic:

1. Extract site-level depth.
2. Detect LOH-like or haplotype-inconsistent regions.
3. Finalize per-sample LOH/recombination-like summaries.

Reader-facing mapping:

- Supports downstream interpretation of haplotype discontinuities and recombination/gene-conversion analysis.

### `10.Visualization/`

Visualization scripts for PCA and BAM/depth QC.

Logic:

- Generate chromosome-level PCA or QC figures from processed matrices.

Reader-facing mapping:

- Supports figures associated with **Phasing** and QC validation.

## End-to-end conceptual flow

```text
FASTQ QC
  -> BWA-MEM mapping
  -> SAMtools BAM processing / duplicate marking / indexing
  -> BamDeal and window-depth summaries
  -> chromosome depth normalization
  -> depth-based chromosome-content/ploidy calls
  -> GATK SNP calling and filtering
  -> per-chromosome haplotype clustering with VCF2Dis/GCTA/PCA
  -> haplotype assignment and VCF annotation
  -> sperm chromosome genotyping and recombination/gene-conversion analyses
```

## How this code is represented in the software submission package

- Source files are kept under `source_code/HWM.backScipt/`.
- This logic note maps scripts to Methods language and explains the role of each directory.
- The package-level demo scripts remain small reference implementations for peer-review demonstration; the original source directory contains the fuller production-oriented script collection.


## Demo inputs and outputs

A lightweight demo is included for selected HWM analysis utilities that can run without external aligners or large sequencing files.

- Demo input directory: `demo_data/HWM_analysis/`
- Demo output directory: `demo_output/HWM_analysis/`
- Demonstrated utilities:
  - `04.Coverage/02.Summary/aggregate_coverage.pl`
  - `05.Ploidy/chr_ploidy_ratio.pl`
  - `07.HapGATK/03.Process/vcf_hap_to_diploid.pl`

The HWM demo validates table parsing, chromosome-depth aggregation, depth-ratio style summaries and genotype-format conversion. Toy values are not biological conclusions.
