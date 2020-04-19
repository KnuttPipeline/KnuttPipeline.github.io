---
layout: default
title: KnuttBinPhylo
nav_order: 4
permalink: /KnuttBinPhylo
has_children: true
has_toc: true
---

# KnuttBinPhylo
{: .no_toc}

[**KnuttBinPhylo**](https://github.com/KnuttPipeline/KnuttBinPhylo) is a pipeline to automatically construct a set of universal markers and use those to construct a phylogenetic tree.

## Marker selection

Marker candidates are based on **PFAM** families and configured in the config file. The default candidates are based on the [PhyloM sets A,B](http://giphy.pasteur.fr/PhyloM/bacteria/). Proteins from **UniProt** proteomes (Archaea and Bacteria) with this marker are downloaded and aligned using MAFFTs automatic run mode. Markers need to be present in 75% of proteomes across each domain (Bacteria, Archaea AND candidates). Proteomes then need to have 50% of the selected markers.

If a proteome contains has multiple proteins with the marker annotation, the JC distances from the candidates to a subsample(n=1000) of the other proteins with the marker will be calculated. If the identity is to low for the JC, an infinite distance is assumed. The candidate with the lowest median distance is selected.

After running the rule `refData` the following files will be available:

#### Output files
{: .no_toc }
``` tree
Tbd.
```

## Tree

The tree can be produced with the `tree` rule. RAxML HPC is used, not the newer RAxML-NG as the tree will be calculated with rapid boostraps, this feature isn't available in the newer implementation, but reduces the bootstraping time significantly.

## Tree Metadata

For the 0.1 release the pipeline will provide files to annotate the tree in the iTOL tree viewer.