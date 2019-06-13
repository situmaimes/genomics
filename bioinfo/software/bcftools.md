https://samtools.github.io/bcftools/bcftools.html
bcftools — utilities for variant calling and manipulating VCFs and BCFs.

-o, --output FILE
When output consists of a single stream, write it to FILE rather than to standard output, where it is written by default.
-O, --output-type b|u|z|v
Output compressed BCF (b), uncompressed BCF (u), compressed VCF (z), uncompressed VCF (v). Use the -Ou option when piping between bcftools subcommands to speed up performance by removing unnecessary compression/decompression and VCF←→BCF conversion.

# call
See bcftools call for variant calling from the output of the samtools mpileup command.
-m, --multiallelic-caller
alternative model for multiallelic and rare-variant calling designed to overcome known limitations in -c calling model (conflicts with -c)
-v, --variants-only
output variant sites only