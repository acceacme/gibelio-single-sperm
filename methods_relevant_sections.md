# Manuscript methods sections relevant to the software/code submission

## HWM analysis

The HWM analysis code collection supports the upstream single-sperm analysis workflow, including SNP calling, chromosome-content inference and phasing.

### SNP calling

BWA-mem (version 0.7.17) (https://github.com/lh3/bwa) was used to map T7 reads to reference genomes created by combining D. rerio (GRCz11) with C. auratus (ASM1972071v2)/C. gibelio (ASM1984389v2) assemblies. The resulting bam files were sorted and duplicates were marked using SAMtools (Version 1.16.1, https://github.com/samtools). SNPs were called using Haplotypecaller in GATK (Version 4.1.2, https://github.com/broadinstitute/gatk) with the parameter -ploidy==1, and then filtered with VariantFiltration in GATK using quality/depth/mapping/missingness filters.

### Sperm chromosome contents

The read depths for individual chromosomes were calculated by Bamdeal (Version 0.27, https://github.com/BGI-shenzhen/BamDeal) and normalized by the average depth of the zebrafish genome in the same sample. The relative depths of all C. gibelio chromosomes showed a trimodal distribution, providing a preliminary criterion to determine chromosome ploidy. Based on this, chromosomes were classified as missing, haploid, diploid, and triploid when their relative depths were <0.6, 0.6-1.8, 1.8-3.1, and >3.1, respectively. Finally, chromosome composition was refined according to the genotype information of the sperm.

### Phasing

Using SNPs from the haploid chromosomes, we first analyzed the population structure of the 38 C. auratus and the 56 C. gibelio sperm samples for each chromosome by VCF2Dis (https://github.com/BGI-shenzhen/VCF2Dis) combined with PHYLIPNEW, and GCTA (https://yanglab.westlake.edu.cn/software/gcta) combined with R ggplot2 pipelines. Both pipelines indicated that, for each chromosome, C. auratus samples were basically divided into two groups, while C. gibelio samples were divided into three groups with no distinguishable intra-group, reflecting to their respective polyploidy. Next, linkage SNP information for each group was phased to represent a haplotype in somatic cells.

## Sperm chromosome genotyping

We genotyped a sperm sample according to the following steps. (1) We filtered out the SNP sites that show same genotype or have ‘N’ among the chromosome haplotypes; (2) We filtered out the missing SNP sites that were supported by < six reads; (3) The SNP positions with sequencing depth > 30 × were filtered out; (4) We filtered out the SNP sites that are < 5 bp from adjacent SNP site; (5) We filtered out the individual SNP sites where their genotypes belong to different haplotypes compared to consecutive adjacent SNP sites. (6) The haplotype of chromosome regions was determined by the genotype of SNP sites residing them when a given 10Kb window had at least 10 SNPs, we assigned its genotype based on the proportion of SNP sites with the genotype belong to each chromosome haplotype. (7) Chromosomes with different ploidies in each sperm sample were phased based on window genotype.

## Chromosome-level summary of gonad-enriched expression and correlation analysis

RNA-seq data of eight adult tissues were downloaded from NCBI, covering testis, ovary, liver, brain, hypothalamus & pituitary, kidney, muscle, spleen, and heart with three biological replicates per tissue. RNA-seq reads were mapped to the C. gibelio reference using HISAT2 and the expression level was calculated using reads per kilobase per million mapped reads (RPKM).

To define testis- and ovary-enriched genes, the merged multi-tissue transcriptome matrix was processed using custom Perl scripts. Genes were classified as gonad-enriched if their expression levels met filtering criteria including higher transcription in the target gonad relative to other tissues and FDR filtering.

For each chromosome, the mean log2(expression + 1) of all testis-enriched genes (`Tenr_log2_expr_mean`) and ovary-enriched genes (`Oenr_log2_expr_mean`) was calculated. An integrated symmetric metric was further defined as `MI_enriched = z(Tenr_log2_expr_mean) - z(Oenr_log2_expr_mean)`. Scatter plots were generated in R for visualization.

## Recombination

We first filtered out samples with an excessively high number of missing SNPs, and 53 samples were selected for further analysis. Recombination events were identified when the window haplotype of a chromosome changed. The haplotype at each site was inferred by jointly analyzing chromosome haplotype information, the base genotype, and the corresponding base depth. To improve haplotype accuracy, the most probable haplotype at each SNP site was corrected based on the haplotype patterns of its 20 upstream and 20 downstream SNPs. SNP sites exhibiting multiple possible haplotypes after correction were excluded from further analysis. If two or more adjacent SNPs showed haplotypes inconsistent with those of the genotyped chromosome, these SNPs were grouped into a recombinant gene conversion region.
