# icechip
This repository contains scripts and tools for analyzing ICeChIP-seq data, as described by Grzybowski et al. (2015) and Shah et al. (2018).

## Dependencies and Required Files
These scripts assume use of a UNIX system and require the following tools to be installed and added to the PATH environment variable:

* Bowtie2 (http://bowtie-bio.sourceforge.net/bowtie2/index.shtml)
* BedTools (https://bedtools.readthedocs.io/en/latest/)
* SamTools (http://www.htslib.org/)
* UCSCTools (http://hgdownload.soe.ucsc.edu/admin/exe/).

We have tested these scripts on Ubuntu LTS 12.04, 14.04, 16.04, and 18.04. For the above listed tools, we have tested Bowtie2 v. 2.3.4.1, BedTools v. 2.26.0, and SamTools v. 1.7.

The "Scripts" folder contains the icechip and computeHMDandError scripts for analyzing the ICeChIP-seq data. These scripts require paired-end sequencing reads in FastQ format. In addition, they require a tab-delimited file listing the lengths of each chromosome as well as a calibration table listing each DNA sequence in the nucleosome standards and the nucleosome standard to which they correspond. Examples of chromosome length files and calibration tables are located in the "Example Files" folder.

Additionally, a custom genome must be built by concatenating the reference genome of interest with a fasta file listing the sequences of each nucleosome standard sequence, then concatenating the two files together. This concatenated genome is then used to build a Bowtie2 index for use in the icechip script. An example of a nucleosome standard sequence fasta file is also provided in the "Example Files" folder.

***CRITICAL: The length file, calibration table, and nucleosome standard sequences genome file provided here are purely to demonstrate the formatting of the files. They should not be used for the ICeChIP-seq data analyses. Custom chromosome length files, calibrations tables, and nucleosome standard sequence genomes must be generated for your specific cell line, genome assembly, and nucleosome standard set.***

Sample data to test these scripts can be found at the Gene Expression Omnibus (GEO) under accession number GSE103543 (https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE103543). The calibration table provided in the "Example Files" folder is designed to work with these sample data.

## Scripts
The following bash scripts are used to analyze the ICeChIP-seq datasets. They are provided in the "Scripts" folder.

### icechip
The icechip script should be run on the sequencing reads from each sample (i.e. on every input and on every IP). This script will:

1. Use Bowtie2 to align the fastq files to the concatenated genome using the Bowtie2 index
2. Filter the alignments for both alignment quality and for insert length (default: filter for mononucleosome-sized inserts), and
3. Generate genome coverage maps and calibration files counting read pairs mapping to each chromosome and histone modification (per calibration table)

Syntax:

`./icechip -p <number of cores; default 1> -x <Bowtie2 index base name> -1 <FastQ file for read 1> -2 <FastQ file for read 2> -g <chromosome length file> -c <calibration table> -q <MAPQ cutoff; default 20> -l <length cutoff; default 200> -o <output file base name>`

The expected output of the icechip script is a genome coverage bedgraph file, a genome coverage bigwig file for genome browser visualization, a calibration file (extension .cal), and a log file that gives statistics on the number of reads processed at each step (extension .log).

### computeHMDandError
The computeHMDandError script should be run on each IP and its associated input dataset. If multiple IPs share a single input, then that same input dataset is used for each of those IP datasets. This script will:

1. Compute the HMD genome-wide, 
2. Compute the magnitude of the 95% confidence interval genome-wide using Poisson statistics, and
3. Compute the upper and lower bounds of the 95% confidence interval genome-wide.

All of the outputs of the icechip script for the IP and input should be in the same folder. The name of the modification should be the same target name listed in the calibration table.

Syntax:

`./computeHMDandError -m <name of modification> -1 <IP genome coverage bedgraph> -2 <Input genome coverage bedgraph> -g <chromosome length file>`

The expected output of the computeHMDandError script is an HMD bedgraph file, a 95% confidence interval magnitude bedgraph file, upper and lower bounds of confience interval bedgraph file, and bigwig files of these bedgraph files for genome browser visualization.

## License
These resources are provided under a GNU General Public License. Scripts were written by Adrian T. Grzybowski (pantadeusz@uchicago.edu) and Rohan N. Shah (rohanshah@uchicago.edu).

## References
Grzybowski AT, Chen Z, Ruthenburg AJ. [Calibrating ChIP-Seq with Nucleosomal Internal Standards to Measure Histone Modification Density Genome Wide](https://www.cell.com/molecular-cell/fulltext/S1097-2765(15)00304-4). Mol Cell, 58:886-899. 2015.

Shah RN, Grzybowski AT, Cornett EM, Johnstone AL, Dickson BM, Boone BA, Cheek MA, Cowles MW, Maryanski D, Meiners MJ, Tiedemann RL, Vaughan RM, Arora N, Sun Z-W, Rothbart SB, Keogh M-C, Ruthenburg AJ. [Examining the Roles of H3K4 Methylation States with Systematically Characterized Antibodies](https://www.cell.com/molecular-cell/fulltext/S1097-2765(18)30675-0). Mol Cell, 72:162-177. 2018.
