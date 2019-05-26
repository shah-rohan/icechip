# icechip
This repository contains scripts and tools for analyzing ICeChIP-seq data, as described by Grzybowski et al. (2015) and Shah et al. (2018).

These scripts assume use of a UNIX system and require the following tools to be installed and added to the PATH environment variable: Bowtie2 (http://bowtie-bio.sourceforge.net/bowtie2/index.shtml), BedTools (https://bedtools.readthedocs.io/en/latest/), SamTools (http://www.htslib.org/), and UCSCTools (http://hgdownload.soe.ucsc.edu/admin/exe/).

We have tested these scripts on Ubuntu LTS 12.04, 14.04, 16.04, and 18.04. For the above listed tools, we have tested Bowtie2 v. 2.3.4.1, BedTools v. 2.26.0, and SamTools v. 1.7.

The "Scripts" folder contains the icechip and computeHMDandError scripts for analyzing the ICeChIP-seq data. These scripts require paired-end sequencing reads in FastQ format. In addition, they require a tab-delimited file listing the lengths of each chromosome as well as a calibration table listing each DNA sequence in the nucleosome standards and the nucleosome standard to which they correspond. Examples of chromosome length files and calibration tables are located in the "Example Files" folder.

Additionally, a custom genome must be built by concatenating the reference genome of interest with a fasta file listing the sequences of each nucleosome standard sequence, then concatenating the two files together. This concatenated genome is then used to build a Bowtie2 index for use in the icechip script. An example of a nucleosome standard sequence fasta file is also provided in the "Example Files" folder.

***CRITICAL: The length file, calibration table, and nucleosome standard sequences genome file provided here are purely to demonstrate the formatting of the files. They should not be used for the ICeChIP-seq data analyses. Custom chromosome length files, calibrations tables, and nucleosome standard sequence genomes must be generated for your specific cell line, genome assembly, and nucleosome standard set.***

Sample data to test these scripts can be found at the Gene Expression Omnibus (GEO) under accession number GSE103543 (https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE103543). The calibration table provided in the "Example Files" folder is designed to work with these sample data.

The icechip script should be run on the sequencing reads from each sample (i.e. on every input and on every IP). This script will:
	1. Use Bowtie2 to align the fastq files to the concatenated genome using the Bowtie2 index
	1. Filter the alignments for both alignment quality and for insert length (default: filter for mononucleosome-sized inserts), and
	1. Generate genome coverage maps and calibration files counting read pairs mapping to each chromosome and histone modification (per calibration table)
