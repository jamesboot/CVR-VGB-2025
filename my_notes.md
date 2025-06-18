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

## Molecular Phylogenetics
- Always about common ancestors, visualisation doesn't matter
- See 2007 Zvelebil and Baum Understanding Bioinformatics 
- Multiple sequence alignment has to be right to get an accurate tree
- Many ways to do phylogenetics and build trees
- Homology and similarity are not the same thing:
  - Homology is whether you are descended from the common ancestor - this is binary
  - Similarity is a scale - sequence similarity %
 - Key question is how to weight point mutations vs indels - often defaults are good enough but this is important to consider
 - CLUSTALW is outdated, MAFFT is new and popular, MUSCLE is another option
 - BEAST is a Bayesian method/software - becoming more popular but needs priors.
 - Maximum parsimony - what are possible trees, which tree is explained by the fewest mutations - less popular now - not statistical, not good for large data because its exhaustive, generates every possible tree, only using some of the data - only sites which have a certain number of mutations
 - Maximum likelihood - more regiorous and stats based, compute probability of the observed data (sites in alignment) assuming it has evolved under a particular evolutionary tree, maximum likelihood tree is the tree with the highest probability of explaining the tree
 - Distance matrix is another method - just calculate the number of differences and use these as differences - numeric matrix and then plot
   - Substituion models are a way of doing distance matrices - many types of models, simpler the better, don't overfit with a super complicated model - Jukes-Cantor, Kimura 2, General Reversible - many tools will decide this for you
 - Bootstrapping - characters and resampled with replacement to create replicate data sets, each bootstrap is analysed, agreement is searched for between trees to create a consensus tree - you can show support values on the tree - really powerful 
 - 
