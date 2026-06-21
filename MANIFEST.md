# Package manifest

This package contains reader-facing source code, lightweight demo data, expected demo outputs and documentation for the manuscript data-processing workflows.

## Main package contents

- `README.md`: package overview, system requirements, installation and demo commands.
- `LICENSE`: MIT license.
- `environment.yml`: example software environment.
- `methods_relevant_sections.md`: reader-facing summary of the manuscript Methods sections represented by the code.
- `reproduction_notes.md`: practical notes for full-scale reproduction.
- `filled_nr_software_policy_text.md`: text version of the Nature Research software checklist.
- `supporting_report_summary.md`: generic mapping between broader manuscript analyses and software components.
- `docs/`: detailed module-level logic notes (`hwm_analysis_logic.md`, `genotyping_and_recombination_logic.md`, `gonad_enriched_logic.md`) and source-code manifest.
- `source_code/`: source-code collections.
- `demo_data/`: module-level demo inputs.
- `demo_output/`: expected module-level demo outputs.

## Source-code modules

- `source_code/HWM.backScipt/`: HWM analysis scripts for mapping/QC, depth/coverage processing, SNP calling/filtering, phasing and related table-processing workflows.
- `source_code/genotyping_and_recombination/`: compact reference implementation for sperm chromosome genotyping and recombination/gene-conversion detection.
- `source_code/gonad_enriched/`: custom scripts for gonad-enriched gene-set definition and chromosome-level expression summaries.

## Demo modules

- `demo_data/HWM_analysis/` and `demo_output/HWM_analysis/`.
- `demo_data/genotyping_and_recombination/` and `demo_output/genotyping_and_recombination/`.
- `demo_data/gonad_enriched/` and `demo_output/gonad_enriched/`.
