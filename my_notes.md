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
 - iqtree2 looks really good. Takes PHYLIP or FASTA or CLUSTAL files - lots of options - conda install

### Questions

1. Data should be homologous if you have very different unrelated virus' you shouldn't analyse them! BLAST give you an idea of this before going in. Equally highly homolgous is not informative.
2. Changing the analysis methods doesn't change the outputs very much, robust data and experiments lead to robusy findings which can be reproduced using different methods.
3. Maximum likelihood is discrete - looking at specific sites in alignment. Distance method uses all data to create a numeric matrix. ML more popular.
4. PhyML - tree search is quite cheap, not indepth, this makes it quick. RAxML and iqtree are much more sophisticated, more principled. Look up Heuristics!
5. Substition model matters because you are trying to reflect the type of changes you get accurately. Different substition models model the changes differently. iqtree will assign the model and do the tree for you, you don't need to decide the model.
6. Specific to the analysis, don't worry.
7. Bootstrapping is resampling the analysis, samples the alignment, and repeats the analysis. Takes the consensus result.
8. Why should be concerned about recombination - means that you have virus with sequences from different evolutionary background - software assumes a common evolutionary background. GAURD is a really good software for getting around this.

## De novo assembly
- Check out E-utilities from GenBank - helpful tool for downloading sequences and annotation files 
- Read cleaning is important before de novo
- Can handle segmented virus, you should just expect multiple contigs
- Important to test multiple programs
- Don't need a huge number of reads, can subsample, however there is a minimum depth
- Gaps can be filled with read overhangs or clossest reference sequence
- 3 algorithms: greedy, overlap layout consensus, de bruijn graphs
  - Greedy: pick two strings with largest overlap and replace them with their merge, and then keep adding to
  - OLC: build overlap graph, then merge reads and cimplify graph, then walk the graph, nodes are reads, edges are overlaps, hamiltonian path or eulerian path, computationally expensive
  - DB Graphs: take read, chop into k-mers, create graph of all k-mers (nodes) and edges become overlaps, walk the graph, does not store k-mer position in the read, if a genome has repetetive regions the graph will have many loops, contigs can end up being shorter than reads, k-mer size is very important - depends on the data
  - Hybrid approach - combine more than one method, takes advantage of mulitple methods, but are more complex to implement and configure
 
### Assembly validation
- Is it the right orientation?
- Why do I get multiple contigs? Coverage leading to gaps, or repetetive regions
- Does sequence repeat itself? Could be due to circular assembly, or repetetion
- If you use multiple assemblers which result can you trust?
- Good result = few contigs which are long, how many Ns?
- N50/NG50:
  - Take all contigs, sort by size (longest first)
  - N50: Count total length of the assembly (sum length of all contigs), calculate number of contigs needed to cover half of the assembly
  - NG50 is the same but relative to the genome length
- If yu don't get full lnegth assembly:
  - Combine contigs from different assemblies into a scaffold
  - Check for repetetive sequence and deal with them - how many repeats to keep? Read size? Does read size cover repeat regions?
  - Can you fill gaps with read overhangs for reference genome?
 
## Novel virus detection
- single virus sequencing - droplet that only 1 virus can get into
- 
