# Supporting analysis summary for software submission

This document summarizes how broader manuscript-associated computational analyses relate to the source-code modules in this package. It intentionally uses generic analysis categories rather than local project filenames.

## 1. Single-sperm genotyping and chromosome maps

Relevant code modules:

- `source_code/HWM.backScipt/`
- `source_code/genotyping_and_recombination/`

Main data types:

- chromosome haplotype/genotype tables;
- single-sperm SNP genotype/depth tables;
- per-window haplotype assignments;
- chromosome-level genotype/phasing summaries.

Main output types:

- chromosome distribution summaries;
- per-sample haplotype/genotype maps;
- recombination and candidate gene-conversion tables.

## 2. Recombination-region gene analysis and enrichment

Relevant code context:

- downstream tables generated from `source_code/genotyping_and_recombination/` outputs;
- R/Perl/table-processing utilities represented in the broader workflow.

Main data-processing logic:

- extend recombination or candidate gene-conversion regions by a defined flanking distance;
- intersect extended regions with gene annotations;
- perform GO/KEGG or similar functional enrichment;
- compare observed breakpoint/gene distances with randomly sampled genomic positions.

## 3. Repeat density, heterozygosity and recombination context

Relevant code context:

- HWM analysis depth/variant processing utilities;
- downstream table-processing and plotting scripts.

Main data-processing logic:

- orient chromosome coordinates according to centromere positions;
- bin chromosomes into comparable intervals;
- calculate regional heterozygosity from SNP density;
- calculate repeat density from merged repeat annotations;
- compare these genomic features with recombination positions.

## 4. Chromosome presence/absence gene enrichment

Relevant code context:

- chromosome-content summaries from HWM analysis;
- chromosome genotype/haplotype calls from the genotyping module.

Main data-processing logic:

- classify chromosomes by presence frequency across sperm samples;
- extract genes from high-frequency or low-frequency chromosomes;
- compare functional enrichment between chromosome groups.

## 5. Paternal/sex-specific sequence analyses

Relevant code context:

- supporting Methods workflows using long-read/short-read alignment, assembly and normalized-depth analysis.

Main data-processing logic:

- align paternal long reads to a maternal reference;
- extract unmapped or enriched reads;
- assemble non-reference contigs;
- build combined reference sequences;
- align progeny short reads and summarize normalized depth across reference components.

## 6. Collinearity and putative centromere inference

Relevant code context:

- supporting Methods workflows using genome alignment and synteny analysis.

Main data-processing logic:

- align genome assemblies;
- infer syntenic/collinear blocks;
- transfer or compare centromere-position information between assemblies;
- generate chromosome-level coordinate summaries.
