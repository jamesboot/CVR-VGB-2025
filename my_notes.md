# My Notes

## BASH useful commands
`tr` - translate

`&` - put at the end of a command to send to background

`man` - view manual for a command

`sort` - use `-t` to define a delimeter, use `-k` to define segment/key (column) to sort on

## Reference alignment 
- Mismtach threshold for viral sequences? Stick with defaults unless your sample is very divergent from reference
- Coverage/depth:
  - Coverage = how many reads cover a specific position - often averaged over the genome
  - Breadth = how much of genome covered
- BAM file visualisation = Tablet - better than IGV

## Consensus
- Default coverage threshold 10 for consensus - 20 is a good threshold, 5 is weak, 2 & 1 are desperate
- Consensus requires a reference
- If unsure you may use an ambiguity code i.e. its a purine but not sure - some people hate this and prefer Ns
- mpileup is first step in consensus - converts bam data to data about each genome position (mpileup also gives coverage) - then pass to iVar
  - Use `-d 0` in samtools mpileup to stop it ignoring anything over 8000 read depth (designed for humans, don't normally get that depth)

## Variant calling
- Allele frequency depends on what your question is, are you interested in low frequency - small populations of virus' that might have mutation
- 1% is still confident that there is a variant given sequencing error rate (below 0.1% is a bit sketchy)
- Problems with low frequency variant calling:
  - Error rate - see above
  - Systematic errors: flaw in the equipment - illumina more likely to get errors at some motifs than others Meacham et al 2011 BMC bioinformatics, ONT makes errors at homo-polymers
  - Strand bias - if you have a real mutation it should be on both reads, systematic errors only occur on one read (e.g. motifs - won't be on both reads) - can use Fisher's exact test for this
  - LoFreq - getting old - but good
  - For low frequency variants you want high depth - recommend >1000 and then trust variants above 0.5% or 1%
  - LoFreq does not account for RT-PCR errors
  - VPhaser2, VarScan2, Freebayes (becoming very popular)
  - SNPeff - take VCF file and annotation file of genome - ads whether its synonymous / missense (needs gff file if not in the database) - make sure you add -ud 0 which sets upstream downstream interval length to 0 (again turning off a human setting) - this will make sure the variant is not associated with upstream or downstream genes
  - iVar can also do annotation but needs gff file 
