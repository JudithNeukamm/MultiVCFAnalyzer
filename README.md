# MultiVCFAnalyzer
[![install with bioconda](https://img.shields.io/badge/install%20with-bioconda-brightgreen.svg?style=flat)](http://bioconda.github.io/recipes/multivcfanalyzer/README.html)


MultiVCFAnalyzer reads multiple VCF files as produced by the GATK UnifiedGenotyper and after filtering provides the combined genotype calls in a number of formats that are suitable for follow-up analyses such as phylogenetic reconstruction, SNP effect analyses, population genetic analyses etc.<br>
Furthermore, the results are provided in the form of various tables for manual inspection and presentation/publication purposes.

If you use MultiVCFAnalyzer please cite:

Bos, Harkins, Herbig, Coscolla et al.<br>
Pre-Columbian mycobacterial genomes reveal seals as a source of New World human tuberculosis<br>
Nature 514, 494–497 (23 October 2014) doi:10.1038/nature13591

In case of any questions please contact Alexander Herbig (herbig@shh.mpg.de).

Please start the program with the following parameters in exactly this order:

```bash
SNP effect analysis result file (from SnpEff; txt format)
Reference genome fasta file
Reference genome gene annotation (gff)
Output directory
Write allele frequencies ('T' or 'F')
Minimal genotyping quality (GATK)
Minimal coverage for base call
Minimal allele frequency for homozygous call
Minimal allele frequency for heterozygous call
List of positions to exclude (gff)
[vcf_files ...] input vcf files as generated by the GATK UnifiedGenotyper
```

> To omit an optional input file put `NA` as the file name.
> The SnpEff file, the reference gene annotation, and excluded positions are optional.

## Building JAR File

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

