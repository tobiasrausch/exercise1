# exercise1

## checkout of the code

You can clone the repository using

`git clone https://github.com/tobiasrausch/exercise1.git`

## Download SARS-CoV-2 reference

The FASTA file can be downloaded [here](https://www.ncbi.nlm.nih.gov/nuccore/NC_045512.2)

## Index the reference

We use [bwa](https://github.com/lh3/bwa) to align to the SARS-CoV-2 reference. We first need to index the reference

`bwa index sequence.fasta`

## Align to the reference

Alignment:

`bwa mem sequence.fasta Plate42B2.R1.fastq.gz Plate42B2.R2.fastq.gz | samtools sort -o align.bam -`

`samtools index align.bam`

To view the alignments on the screen

`samtools view align.bam | head | cut -f 1-7`

The alignment flags can be decoded using this [tool](https://broadinstitute.github.io/picard/explain-flags.html).

## Visualizing alignments

[IGV](https://software.broadinstitute.org/software/igv/) is one popular alignment viewer.

Another option on the command line is [samtools](https://github.com/samtools/samtools) tview:

`samtools tview --reference sequence.fasta -p NC_045512.2:23000 align.bam`

## Variant calling

[BCFtools](https://github.com/samtools/bcftools) can be used for variant calling

`bcftools mpileup -f sequence.fasta align.bam | bcftools call --ploidy 1 -mv -Ob -o calls.bcf`

`bcftools view calls.bcf | less`

## Variant annotation

Variant annotation online using [VEP](https://covid-19.ensembl.org/info/docs/tools/vep/index.html)

`bcftools view calls.bcf  | cut -f 1-5 | grep -v "^#"`

## Lineage assignment

Common S gene mutations for each lineage are available [here](https://outbreak.info/compare-lineages)

