# HWM analysis demo data

This directory contains small inputs for lightweight HWM analysis code checks.

- `depth_file_list.txt`: list of per-sample chromosome-depth files.
- `depth_inputs/*/04.Stat/chr_depth.tsv`: toy chromosome-depth summaries. The path structure intentionally mimics the original workflow because some HWM scripts infer sample IDs from parent directories.
- `haploid_genotypes.tsv`: toy haploid genotype table used to demonstrate genotype-format conversion.

The demo avoids external aligners and large genome files. It exercises small Perl utilities from the HWM analysis collection, especially chromosome-depth aggregation/ploidy-style summaries and genotype table conversion.
