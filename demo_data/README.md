# Demo data overview

The demo data are organized by source-code module.

```text
demo_data/
├── genotyping_and_recombination/
│   ├── chromosome_haplotypes.tsv
│   ├── chrom_sizes.tsv
│   └── demo_sperm_snps.tsv
├── gonad_enriched/
│   ├── tissue_expression_replicates.tsv
│   └── chr_freq_in_sperm.tsv
└── HWM_analysis/
    ├── depth_file_list.txt
    ├── depth_inputs/*/04.Stat/chr_depth.tsv
    └── haploid_genotypes.tsv
```

- `genotyping_and_recombination/` supports the compact Python demo for sperm chromosome genotyping and recombination/gene-conversion calling.
- `gonad_enriched/` supports the Perl demo for tissue-expression merging, gonad-enriched gene selection and chromosome-level summarization.
- `HWM_analysis/` supports lightweight checks of HWM analysis utilities without requiring large FASTQ/BAM/VCF files or external aligners.
