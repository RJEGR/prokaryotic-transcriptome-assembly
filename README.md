This repository contains notes for the installation and execution of bioinformatics tools as well as a code-lab scripts. It is not a final version of reproducible workflow, so use with discretion.

# Author
Gómez-Reyes Ricardo

# prokaryotic-transcriptome-assembly
Transcriptomic tools exist for both, reference-based and de novo assembly, but most
of them were designed primarily for eukaryotic transcriptomes. Transcriptomic
assembly tool performance is diverse regarding different data sets and quality
metrics (Hölzer et al., 2019). Because bacterial genomes are generally denser,
neighboring transcripts frequently overlap (Ex. Polycistronic messages, operon, etc)
prokaryotic faces different challenges than eukaryotic transcriptome assemblies.
Different de transcriptome assemblies tools have been developed at which Trinity
(Hölzer et al., 2019), segemehl (Otto et al., 2014), rockhopper2 (Tjaden et al., 2015) and
bowtie2 (Marsh et al., 2018) outperforms the prokaryotic transcriptome assemblies
quality. 

The following method describes how the reference-based transcriptome may be
generated in order to perform a transcript-level analysis from bacterial RNA-seq
libraries. Cleaned reads are aligned to reference genomes using Bowtie2 (Langmead
et al., 2012) or segemehl. Then, obtain the transcript-level annotations by both:
1. Homology sequence search (BLAST+ / SwissProt), protein domain
identification (HMMER/PFAM) using the Trinotate framework (Bryant et al.,
2017). Then, functional information (eggNOG / GO / Kegg) is extracted.
2. Transcriptional start site (TSS) prediction may be performed using the
program TSSpredator (Dugar et al., 2013) and scored as Thomason et al., (2015)
and prOkaryotic OpeRons (DOOR, Mao et al., 2014).

Finally, quantification procedures are performed using either FADU (Chung et al.,
2021) or RSEM (Li et al., 2011). And downstream statistical analysis regarding
differential expression analysis and gene regulatory co-expression networks are
performed using DESEQ2 and WGCNA, respectively.

