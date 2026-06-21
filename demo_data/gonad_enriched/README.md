# Gonad-enriched demo data

This directory contains a minimal expression matrix for the gonad-enriched gene-set demo.

- `tissue_expression_replicates.tsv`: replicate-level RPKM-like expression values. The first three columns are `GeneID`, `Location` and `Length`; subsequent columns are tissue replicate columns. The demo includes testis (`T`), ovary (`O`), liver and brain.
- `chr_freq_in_sperm.tsv`: toy chromosome-frequency table for optional downstream correlation examples.

The demo is intentionally small and is used to exercise the Perl stages of `source_code/gonad_enriched/`: replicate merging, enriched-gene selection and chromosome-level gene-set summarization.
