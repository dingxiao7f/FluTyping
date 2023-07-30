# FluTyping
## Description
FluTyping is a framework to classify the genomes of influenza isolates by simultaneously considering their genetic distances and phylogenetic relationships. This approach establishes a comprehensive genotype classification for overall influenza A viruses (IAVs).   
  
The framework involves three main processes: clustering, phylogenetic calibration, and genotyping. In the clustering step, distinct phylogenetic classes of each genomic segment are automatically clustered using epidemiological information and evolutionary measures between isolates. The phylogenetic calibration step manually combines mixed clusters and outliers based on the phylogenetic topological structure. Finally, each isolate is assigned a specific genotype by combining optimized clusters of eight genes in the genotyping step.  
  
This web provides the pipeline scripts of the clustering step in FluTyping involves three procedures: the epidemiological combination, the MCU-based combination, and the distance-based clustering.  

## Requirement  
Perl 5 and the module of "Math::Complex"  
R 4.3.0 and the R packages of "ape", "ggtree", "tidytree" and "mclust"      
mafft v7.505  
FastTreeMP v2.1.10   
  
Any stable version of these softwares are also allowed.  

## Instructions
### Descriptions of scripts
**epi_cluster.pl** - combine the genomic sequences based on the epidemiological information of isolates and the sequence similarity between isolates.  
**extra_cluster.pl** - obtain the formated cluster file from the CD-HIT results.  
**update_cluster.pl** - obtain the formated cluster file of each circulation in the MCU-based combination.  
**stat_count.pl** - calculate the number of all isolates involving in the MCU-based combination, which is used to check whether the process has errors.    
**output_mcu.pl** - output all MCUs of the phylogenetic tree of each circulation.  
**check_error.pl** - check whether any circulation has redundant sequences, i.e., the circulation has error.  
**cal_parameter.pl** - calculate the measures in the MCU-based combination, which will call the sub-scripts of **cal_similarity_mp.pl**, **cal_delta_entropy.pl** and **cal_specific_site.pl**. The three sub-scripts calculate the average intra-MCU sequence similarity, the entropy change after combining MCUs and the number of same unit-specific genomic loci between MCUs.    
**cal_parameter_init.pl** - calculate the measures of the initialization in the MCU-based combination.    
**combine_seqs.pl** - combine the sequences based on the calculated measures in each circulation.  
**pipeline.pl** - the pipeline script of the MCU-based combination.  
**output_tree_info.R** - parse the phylogenetic tree and output the information formationally.  
**obtain_offspring.R** - output the offsprint of all inner nodes of the phylogenetic tree.  
**obtain_child.R** - output the child nodes of all inner nodes of the phylogenetic tree.  
**cal_inter_ss.pl** - calculate the sequence similarities between pairwise clusters from the MCU-based combination.  
**obtain_matrix.pl** - output the pairwise sequence similarities of MCUs in a matrix.  
**mclust.R** - evaluate the optimal number of clustering MCUs.  
**h_cluster.R** - hierarchical clustering of MCUs in R.  
  

### Pipeline of the epidemiological combination  
`perl epi_cluster.pl $query_fasta $meta_file $thre`  
`mafft --auto --thread -1 --quiet $nr_query_fasta  > $aligned_nr_query_fasta`  
`mafft --auto --thread -1 --quiet $query_fasta  > $aligned_query_fasta`  

$query_fasta represents all genomic sequences to be grouped, $meta_file shows the GISAID id, collection country and collection year of all isolates. $thre is the cutoff value of sequence similarity used in CD-HIT. The inputs format refer the example files.  

### Quick start of the MCU-based combination  
`perl pipeline.pl $aligned_query_fasta $aligned_nr_query_fasta $meta_file`  
  
The inputs are the results in the epidemiological combination step. The specific calculation can use corresponding script based on the description of scripts.  

### Pipeline of the distance-based combination  
#### Calculate the inter-MCUs sequence similarities  
`perl cal_inter_ss.pl $MCU_clu_fasta $cluster_info $thread > $output`  

$MCU_clu_fasta represents the respresentative genomic sequences from the MCU-based combination and $cluster_info is the isolates ID in all clusters. $thread is the number of cores of your computer expected to be used.  

#### Optimal number of clusters assessment  
The converged MCUs are then clustered based on their genetic distance, and the optimal number of clusters is estimated using the Bayesian Information Criterion (BIC) via the R package mclust.  
    
`perl obtain_matrix.pl $pair_ss > $matrix_ss`  
`Rscript mclust.R $matrix_ss $num_clu $output`  
  
$pair_ss is the pairwise sequence similarities of MCUs from the previous step. $num_clu is the number of the MCUs and the $output shows the Graphical quantification of the assessment. The optimal number of clusters will print in the terminal.  
  

#### Hierarchical clustering of the converged MCUs  
The process was performed by hierarchical clustering in R. The other cluster algorithms can also be employed.  

`Rscript h_cluster.R $matrix_ss $opt_num_clu $output`  

$matrix_ss is the matrix of pairwise sequence similarites of all MCUs. $opt_num_clu is the optimal number of clusters evaluated from mclust and the resulted clusters of all MCUs will be output in $output file.  








