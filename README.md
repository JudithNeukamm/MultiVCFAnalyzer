# MultiVCFAnalyzer

  - [Description](#description)
  - [Citation](#citation)
  - [Usage](#usage)
  - [Example](#example)
  - [Output](#output)
  - [FAQs](#faqs)

## Description

[![install with bioconda](https://img.shields.io/badge/install%20with-bioconda-brightgreen.svg?style=flat)](http://bioconda.github.io/recipes/multivcfanalyzer/README.html)

**MultiVCFAnalyzer is a SNP filtering and SNP alignment generation tool, designed around (but not limited to) low coverage ancient DNA data.** MultiVCFanalyzer reads multiple VCF files as produced by GATK UnifiedGenotyper, performs filtering based on a number of criteria, and provides the combined genotype calls in a number of formats that are suitable for follow-up analyses such as phylogenetic reconstruction, SNP effect analyses, population genetic analyses etc.

Furthermore, the results are provided in the form of various tables for manual inspection and presentation/publication purposes.

## Citation

If you use MultiVCFAnalyzer please cite:

Bos, Harkins, Herbig, Coscolla _et al._ 'Pre-Columbian mycobacterial genomes reveal seals as a source of New World human tuberculosis' _Nature_ 514, 494–497 (23 October 2014) [doi:10.1038/nature13591](dx.doi.org/10.1038/nature13591)

A more detailed description of the program can be seen in the supplementary information under "SNP Calling and Phylogenetic Analysis".

In case of any questions please contact Alexander Herbig (herbig@shh.mpg.de).

## Usage

The tool is a java program and requires openJDK 8. Input VCF files must be generated from GATK UnifiedGenotyper (<= 3.5), and ploidy must be set to 2 (to give allele frequency values).

To get the help message run

```bash
java -jar MultiVCFAnalyzer_X-XX-X.jar <OPTIONS>
```

Please start the program with the following parameters in **strictly** this order:

1. SNP effect analysis result file (from [SnpEff](http://snpeff.sourceforge.net/); txt format) [OPTIONAL]
2. Reference genome fasta file - used for VCF construction
3. Reference genome gene annotation (gff) [OPTIONAL]
4. Output directory - location of where to put output files 
5. Write allele frequencies ('T' or 'F') - whether to include the percentage of reads a given allele is present in in the SNP table e.g. A (70%). In haploid microbial contexts, this can be used to assess cross-strain mapping. 
6. Minimal genotyping quality (GATK) - a threshold of which a SNP call falling under is 'discarded'
7. Minimal coverage for base call - the minimum number of a reads that a position must be covered by to be reported
8. Minimal allele frequency for homozygous call - the fraction of reads a base must have to be called 'homozygous'
9. Minimal allele frequency for heterozygous call - a fraction of which whereby if a call falls above this value, and lower than the homozygous threshold, a base will be called 'heterozygous' and reported with a [IUPAC uncertainity code](https://www.bioinformatics.org/sms/iupac.html)
10. List of positions to exclude (gff) [OPTIONAL] - a file listing positions that will be 'filtered' (i.e. ignored)
11. [vcf_files ...] input vcf files as generated by the GATK UnifiedGenotyper

> To omit an optional input file put `NA` as the file name.

## Example

The following is an example of running MultiVCFAnalyzer requiring a minimum genotyping quality of 30, a minimum fold coverage threshold of 5, a homozygous Ref/Allele being called if the base is >= 90% of 

```bash
java -jar MultiVCFanalyzer_X-XX-X.jar \
NA \
/<PATH>/<TO>/<REFERENCE>.fasta \
NA \
/<PATH>/<TO>/<OUTPUTDIRECTORY>/ \
T \
30 \
5 \
0.9 \
0.1 \
NA \
/<PATH>/<TO>/Sample_1/<VCFFILE1>.vcf.gz \
/<PATH>/<TO>/Sample_2/<VCFFILE2>.vcf.gz \
/<PATH>/<TO>/Sample_3/<VCFFILE3>.vcf.gz
```

## Output

### Output files

The following files are created in output directory:

- `fullAlignment.fasta` a fasta file of all positions contained in the VCF files i.e. including ref calls
- `info.txt` information about the run
- `snpAlignment.fasta` a fasta file of just SNP positions with samples only
- `snpAlignmentIncludingRefGenome.fasta` a fasta file of just SNP positions with reference genome
- `snpStatistics.tsv` some basic statistics about the SNP calls of each sample
- `snpTableForSnpEff.tsv` input file for [SnpEff](http://snpeff.sourceforge.net/)
- `snpTable.tsv` basic SNP table of combined positions taken from each VCF file
- `snpTableWithUncertaintyCalls.tsv` same as above, but with lower case characters indicating uncertain call
- `structureGenoypes_noMissingData-Columns.tsv` alternate input file for [STRUCTURE](https://web.stanford.edu/group/pritchardlab/structure.html)
- `structureGenotypes.tsv` input file for [STRUCTURE](https://web.stanford.edu/group/pritchardlab/structure.html)

> The `snpTable.tsv` file will not display positions that are the same call across all samples (e.g. if all samples have `N`,  or `.` [a reference call])!

### `snpStatistics.tsv`

The snpStatistics column definitions are as follows

1. **Sample**: folder name of VCF file
2. **allPos**: length of FASTA file in base pairs (bp)
3. **noCall**: Number of positions with no call made as reported by GATK
4. **discardedRefCall**: Number of positions with a discarded reference call for not passing GATK quality control
5. **discardedVarCall**: Number of positions with a discarded variant call for not passing GATK quality control
6. **filteredVarCall**: Number of positions with discarded calls based on user filter list
7. **unhandledGenotype**: Number of positions with discarded calls for having more than two possible alleles (e.g. Ref: A, ALT: G,T)
8. **refCall**: Number of reference calls made
9. **SNP Calls (all)**: Total number of non-reference calls made
10. **SNP Calls (het)**: Total number of non-reference calls not passing user-supplied heterozygosity/homozygosity thresholds
11. **coverage(fold)**: Average number of reads covering final calls
12. **coverage(percent)**: Percent coverage of all positions with final calls

> Even if 'EMIT_ALL_SITES' is turned on, GATK will ignore any non-ACGT positions in the reference entirely (e.g. Ns), and will not export that position in the VCF file. Thus, refCall + SNP Calls (all) may not match allPos - noCall - discardedRefCall - discardedVarCall - filteredVarCall - unhandledGenotype as these positions are not supplied to MultiVCFAnalyzer'. You can check for this by checking the discrepancy of the two previous calculations is equal across every sample.

## FAQs

### How do I restrict to only 'homozygous' SNPs?

Set both homozygous and heterozygous frequency to the same value. Only ACTGs passing your thresholds will then be reported.

### All my sample names are the same, or are named e.g. 'output'

MultiVCFAnalyzer takes the sample name from the files _directory_. Ensure each VCF file is in a unique directory.

### How do I increase the amount of memory?

Increase the amount of memory allocated to java with the `-Xmx` parameter (here 16 gigabytes)

```bash
java -Xmx16G -jar MultiVCFAnalyzer_X-XX-X.jar <OPTIONS>
```

### How to build the JAR file from source?

If you want to create a JAR file and use the tool, simply install [Gradle](https://gradle.org/) and follow this:

```bash
git clone https://github.com/alexherbig/MultiVCFAnalyzer
cd MultiVCFAnalyzer
gradle build
```

This will create a folder `build/libs/` that contains the compiled JAR file automatically for you. You can then use the tool as described above using:

```bash
java -jar build/libs/MultiVCFAnalyzer-VERSION.jar [...]
```

