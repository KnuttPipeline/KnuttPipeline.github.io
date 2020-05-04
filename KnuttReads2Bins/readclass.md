---
layout: default
title: Read classification
nav_order: 2
permalink: /KnuttReads2Bins/ReadClassification
parent: KnuttReads2Bins
has_toc: false
---
## Read Classification
{: .no_toc}

KnuttReads2Bins currently supports **3** toolchains to classify the prepared reads. The first is a small chain that filters reads of the **SSU rRNA gene** and classifies them using [SINA](https://readthedocs.org/projects/sina/). The second one uses [Kaiju](https://github.com/bioinformatics-centre/kaiju) to search protein databases like NCBIs non-redundant **protein set for k-mers** found in the reads. The third method uses [Sourmash](https://sourmash.readthedocs.io/en/latest/) which relies on **hashes of nucleotide k-mers**.

If looking at Krona reports, make sure to check the **fraction of the sample, that actually was classified**.

The steps for this stage are defined in the [`Snakefile_2ClassifyReads`](https://github.com/KnuttPipeline/KnuttReads2Bins/blob/master/Snakefile_2ClassifyReads) file. While the disk space needed for the reference database depends on the **selected Kaiju database**, with a current `nr_euk` dataset, around Tbd. GB are required.

<dl>
  <dt><em>Small metagenome</em></dt>
  <dd>Tbd. GB</dd>
  <dt><em>Medium metagenome</em></dt>
  <dd>Tbd. GB</dd>
  <dt><em>Large metagenome</em></dt>
  <dd>Tbd. GB</dd>
</dl>

Reference data can be generated with the rule `classifyReadsRefData`. All classification steps can be run with the `classifyReads`, `classifyReadsKrona` and `classifyReadsReport` rules.

Outputs are stored in `<outputdir>/ReadClassification/`, `<outputdir>/Data/ReadClassification/` and `<outputdir>/Reports/`.

---

#### Table of contents
{: .no_toc }

1. TOC
{:toc}

---

### SSU reads (SINA)

The SSU workflow uses the configured version of the *SILVA SSSU NR 99* database to build a **BBMap and SINA** index for analysis. This can be executed with the `SSURefData` rule. The files use around 5.5GB disk space.

The filtering and classification of the reads can be run with the `SSU` rule. A report can be generated with the `SSUReport`rule.

#### Output files
{: .no_toc }
``` tree
output
├── output/Benchmarking
│   └── output/Benchmarking/ReadClassification
│       ├── output/Benchmarking/ReadClassification/sina_SAMPLE.tsv
│       └── output/Benchmarking/ReadClassification/ssu_bbmap_map_SAMPLE.tsv
├── output/Data
│   └── output/Data/ReadClassification
│       └── output/Data/ReadClassification/SAMPLE_readclassification_SSU.tsv
├── output/ReadClassification
│   └── output/ReadClassification/SSU
│       └── output/ReadClassification/SSU/SAMPLE
│           ├── output/ReadClassification/SSU/SAMPLE/SAMPLE_bbmap_SSU.bam
│           ├── output/ReadClassification/SSU/SAMPLE/SAMPLE_bbmap_SSU.fasta
│           ├── output/ReadClassification/SSU/SAMPLE/SAMPLE_bbmap_SSU.out
│           ├── output/ReadClassification/SSU/SAMPLE/SAMPLE_sina_hits.csv
│           ├── output/ReadClassification/SSU/SAMPLE/SAMPLE_sina_hits.fasta
│           └── output/ReadClassification/SSU/SAMPLE/SAMPLE_sina_hits.log
└── output/Reports
    ├── output/Reports/5.1SSUreport.html
    └── output/Reports/SSU_krona.html
```

See the general file information for the [`Benchmarking`](/shared_files#benchmark-files) files. The `SAMPLE_readclassification_SSU.tsv` file combines the results from SINA (`SAMPLE_sina_hits.csv`) and BBmap (`SAMPLE_bbmap_SSU.bam`).

It contains the following columns. Every row is a read filtered by BBmap.

<dl>
  <dt><em>qname</em></dt>
  <dd>Full id of the read</dd>
  <dt><em>sname</em></dt>
  <dd>Full id of the BBMap hit</dd>
  <dt><em>strand</em></dt>
  <dd>Strand of the BBmap hit (-/+)</dd>
  <dt><em>pos</em></dt>
  <dd>Start (left-most) part of the alignment</dd>
  <dt><em>qwidth</em></dt>
  <dd>Read length</dd>
  <dt><em>mapq</em></dt>
  <dd>Mapping quality, error rate as a PHRED score</dd>
  <dt><em>cigar</em></dt>
  <dd><a href="https://drive5.com/usearch/manual/cigar.html">CIGAR</a> alignment string</dd>
  <dt><em>tag.MD</em></dt>
  <dd><a href="https://github.com/vsbuffalo/devnotes/wiki/The-MD-Tag-in-BAM-Files">BBmap MD tag</a></dd>
  <dt><em>tag.XM</em></dt>
  <dd>Number of mismatches (BBMap)</dd>
  <dt><em>tag.NM</em></dt>
  <dd>Edit distance (BBMap)</dd>
  <dt><em>align_cutoff_head_slv</em></dt>
  <dd>Unaligned bases at the beginning of the read (SINA)</dd>
  <dt><em>align_cutoff_tail_slv</em></dt>
  <dd>Unaligned bases at the end of the read (SINA)</dd>
  <dt><em>align_filter_slv</em></dt>
  <dd>Positional variability by parsimony filter applied (Default: NA)</dd>
  <dt><em>align_quality_slv</em></dt>
  <dd>Highest similarity to a reference</dd>
  <dt><em>aligned_slv</em></dt>
  <dd>Date and time of the alignment</dd>
  <dt><em>lca_tax_embl</em></dt>
  <dd>LCA result of the EMBL-EBI/ENA taxa</dd>
  <dt><em>lca_tax_gg</em></dt>
  <dd>LCA result of the Greengenes taxa</dd>
  <dt><em>lca_tax_gtdb</em></dt>
  <dd>LCA result of the GTDB taxa (recommended)</dd>
  <dt><em>lca_tax_rdp</em></dt>
  <dd>LCA result of the RDP taxa</dd>
  <dt><em>lca_tax_slv</em></dt>
  <dd>LCA result of the SILVA taxa</dd>
  <dt><em>nearest_slv</em></dt>
  <dd>References found in the homology search. Space seperated:<br/> <em>ID.VERSION.START.END~IDENTITY</em></dd>
  <dt><em>turn</em></dt>
  <dd>Did SINA reverse and/or complement the read?</dd>
</dl>

---

### Protein reads (Kaiju)

Kaiju can use [different databases](https://github.com/bioinformatics-centre/kaiju#creating-the-reference-database-and-index). Their usage and construction can be quite **memory hungry and time intensive**. You may want to change the **thread count** in the Snakefile. It is also possible to download the `.fmi` file from the Kaiju website and copy it to the appropiate location (You can see it when running the workflow with the `-rn` flags). Reference data generation is executed with the `kaijuRefData` rule.

The classification for all samples is run with the `kaiju` rule. A report can be generated with the `kaijuReport`rule. The Krona report (`kaijuKrona`) doesn't contains the database.

#### Output files
{: .no_toc }
``` tree
output
├── output/Benchmarking
│   └── output/Benchmarking/ReadClassification
│       └── output/Benchmarking/ReadClassification/kaiju_SAMPLE.tsv
├── output/Data
│   └── output/Data/ReadClassification
│       └── output/Data/ReadClassification/SAMPLE_readclassification_kaiju.tsv
├── output/ReadClassification
│   └── output/ReadClassification/kaiju
│       └── output/ReadClassification/kaiju/SAMPLE
│           └── output/ReadClassification/kaiju/SAMPLE/SAMPLE_kaiju.log
└── output/Reports
    │── output/Reports/5.2kaijureport.html
    └── output/Reports/kaiju_krona.html
```

See the general file information for the [`Benchmarking`](/shared_files#benchmark-files). The `SAMPLE_readclassification_kaiju.tsv`contains the results. It contains rows for all reads.

<dl>
  <dt><em>qname</em></dt>
  <dd>Full id of the read</dd>
  <dt><em>classified</em></dt>
  <dd>Was this read classified (U or C)</dd>
  <dt><em>taxid</em></dt>
  <dd>Assigned NCBI tax id</dd>
  <dt><em>lengthorscore</em></dt>
  <dd>Length (MEM) or score (Greedy) of this match</dd>
  <dt><em>match_taxids</em></dt>
  <dd>Tax ids of sequences (max 20) with the best match</dd>
  <dt><em>match_ascs</em></dt>
  <dd>Accessions of those sequences</dd>
  <dt><em>match_fragments</em></dt>
  <dd>Matching fragments</dd>
  <dt><em>tax</em></dt>
  <dd>Classification tax string, seperated by ;</dd>
</dl>

---

### Polynucleotide hash sketches (Sourmash)

Sourmash is really fast and memory efficient. The default settings use an high k-mer length to provide more specific results compared to the Kaiju analysis. The taxonomy and the available genoems are based on the genome taxanomy database (GTDB) project. The hash count is proportionally to the read count. While this preserves the diversity of the reads, it makes comparing samples of uneven depths even harder to compare.

Download the reference database with `sourmashRefData`, run all analysis steps with `sourmash` and produce the report with `sourmashReport`. The Sourmash Krona report also doesn't contain the database (`sourmashKrona`).

#### Output files
{: .no_toc }
``` tree
output
├── output/Benchmarking
│   └── output/Benchmarking/ReadClassification
│       ├── output/Benchmarking/ReadClassification/sourmash_comparison.tsv
│       ├── output/Benchmarking/ReadClassification/sourmash_description_SAMPLE.tsv
│       ├── output/Benchmarking/ReadClassification/sourmash_gather_SAMPLE.tsv
│       ├── output/Benchmarking/ReadClassification/sourmash_k51_SAMPLE.tsv
│       └── output/Benchmarking/ReadClassification/sourmash_lca_SAMPLE.tsv
├── output/Data
│   └── output/Data/ReadClassification
│       ├── output/Data/ReadClassification/SAMPLE_readclassification_sourmash_gather.tsv
│       ├── output/Data/ReadClassification/SAMPLE_readclassification_sourmash_lca.tsv
│       ├── output/Data/ReadClassification/SAMPLE_sourmash_signature.tsv
│       ├── output/Data/ReadClassification/PFL9_meta_sourmash_signature.tsv
│       ├── output/Data/ReadClassification/sourmash_sample_comparison
│       ├── output/Data/ReadClassification/sourmash_sample_comparison.bin
│       ├── output/Data/ReadClassification/sourmash_sample_comparison.bin.labels.txt
│       ├── output/Data/ReadClassification/sourmash_sample_comparison.dendro.png
│       ├── output/Data/ReadClassification/sourmash_sample_comparison.hist.png
│       ├── output/Data/ReadClassification/sourmash_sample_comparison.labels.txt
│       ├── output/Data/ReadClassification/sourmash_sample_comparison.matrix.png
│       └── output/Data/ReadClassification/sourmash_sample_comparison.tsv
├── output/ReadClassification
│   └── output/ReadClassification/sourmash
│       ├── output/ReadClassification/sourmash/SAMPLE
│       │   ├── output/ReadClassification/sourmash/SAMPLE/SAMPLE_sourmash_descr.log
│       │   ├── output/ReadClassification/sourmash/SAMPLE/SAMPLE_sourmash_gather.log
│       │   ├── output/ReadClassification/sourmash/SAMPLE/SAMPLE_sourmash_k51.log
│       │   ├── output/ReadClassification/sourmash/SAMPLE/SAMPLE_sourmash_k51.sig
│       │   └── output/ReadClassification/sourmash/SAMPLE/SAMPLE_sourmash_lca.log
│       └── output/ReadClassification/sourmash/sourmash_sample_comparison.log
└── output/Reports
    ├── output/Reports/5.3sourmashReport.html
    └── output/Reports/sourmash_krona.html
```

See the general file information for the [`Benchmarking`](/shared_files#benchmark-files). `SAMPLE_readclassification_sourmash_gather.tsv` gives a list of genomes in the database found in the sample.

<dl>
  <dt><em>intersect_bp</em></dt>
  <dd>Base pairs (approximate) shared between sample and genome</dd>
  <dt><em>f_orig_query</em></dt>
  <dd>Fraction of the sample in the genome</dd>
  <dt><em>f_match</em></dt>
  <dd>Fraction of the genome in the sample</dd>
  <dt><em>f_unique_to_query</em></dt>
  <dd>Fraction of the sample that is only represented in this genome</dd>
  <dt><em>f_unique_weighted</em></dt>
  <dd><em>f_unique_to_query</em> weighted by hash abundance</dd>
  <dt><em>average_abund</em></dt>
  <dd>Average abundace of the hashes found in the sample</dd>
  <dt><em>median_abund</em></dt>
  <dd>Median abundace of the hashes found in the sample</dd>
  <dt><em>std_abund</em></dt>
  <dd>Standard deviation abundace of the hashes found in the sample</dd>
  <dt><em>name</em></dt>
  <dd>Genome name</dd>
  <dt><em>filename</em></dt>
  <dd>Reference data file</dd>
  <dt><em>md5</em></dt>
  <dd>MD5 checksum of the genome</dd>
</dl>

The file `SAMPLE_readclassification_sourmash_lca.tsv` uses the hashes to provide a taxonomic composition of the sample. While it looks like a Krona data file, the `count` field gives the number of assigned hashes and **summed child counts**.

`SAMPLE_sourmash_signature.tsv` provides information on the generated sketch/signature files.

<dl>
  <dt><em>intersect_bp</em></dt>
  <dd>Base pairs (approximate) shared between sample and genome</dd>
  <dt><em>f_orig_query</em></dt>
  <dd>Fraction of the sample in the genome</dd>
  <dt><em>f_match</em></dt>
  <dd>Fraction of the genome in the sample</dd>
  <dt><em>f_unique_to_query</em></dt>
  <dd>Fraction of the sample that is only represented in this genome</dd>
  <dt><em>f_unique_weighted</em></dt>
  <dd><em>f_unique_to_query</em> weighted by hash abundance</dd>
  <dt><em>average_abund</em></dt>
  <dd>Average abundace of the hashes found in the sample</dd>
  <dt><em>median_abund</em></dt>
  <dd>Median abundace of the hashes found in the sample</dd>
  <dt><em>std_abund</em></dt>
  <dd>Standard deviation abundace of the hashes found in the sample</dd>
  <dt><em>name</em></dt>
  <dd>Genome name</dd>
  <dt><em>filename</em></dt>
  <dd>Reference data file</dd>
  <dt><em>md5</em></dt>
  <dd>MD5 checksum of the genome</dd>
</dl>

`sourmash_comparison.tsv` contains the comparison of the samples based on the sourmash hashes using the Jaccard similarity.

---
