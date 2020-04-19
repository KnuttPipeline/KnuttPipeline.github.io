---
layout: default
title: Assembly
nav_order: 4
permalink: /KnuttReads2Bins/Assembly
parent: KnuttReads2Bins
has_toc: false
---
## Assembly
{: .no_toc}

Assembly of the reads is performed with **MEGAHIT**. Only adapter trimming (*if enabled*) is run on the reads for assembly, **no quality trimming**. This is recommended by the authors of MEGAHIT. MEGAHIT is run with the ouput of **BBmerge**, including the **unmerged reads**. For read mapping the BBMerge input reads are used. The whole assembly can be classified with **Sourmash**, each contig can be classified with **CAT**. **MetaQUAST** is run on each assembly.

MetaQUAST reference download **is turned off** by default, as parallel downloads of the same reference for two different samples results in invalid files and crashes.

The steps for this stage are defined in the [`Snakefile_4Assembly`](https://github.com/KnuttPipeline/KnuttReads2Bins/blob/paper/Snakefile_4Assembly) file. Sourmash uses the **same reference data** (GenomeDB based) for contig classification and read classification. CAT classification is based on **NCBIs nr** database.

<dl>
  <dt><em>Small metagenome</em></dt>
  <dd>Tbd. GB</dd>
  <dt><em>Medium metagenome</em></dt>
  <dd>Tbd. GB</dd>
  <dt><em>Large metagenome</em></dt>
  <dd>Tbd. GB</dd>
</dl>

Assembly and read mapping data can be generated with `assembly` and the report is produced by `assemblyReport`. A Sourmash description of the **whole assembly** is available with `assemblySourmash` and **single contig classification** with CAT can be executed with `cat`. A Krona report for the the contigs is available with `catKrona` and `assemblySourmashKrona`. Reference data for CAT (and BAT) can be generated with the `catRefData` rule. The CAT contig data is **reused for running BAT** on the bins later. Reference data (excluding CAT/BAT) is the input for `assemblyRefData`.

Outputs are stored in `<outputdir>/Assembly/`, `<outputdir>/Data/Assembly/` and `<outputdir>/Reports/`.

---

#### Output files
{: .no_toc }

``` tree
output
├── output/Assembly
│   ├── output/Assembly/CAT
│   │   └── output/Assembly/CAT/SAMPLE
│   │       ├── output/Assembly/CAT/SAMPLE/SAMPLE.alignment.diamond
│   │       ├── output/Assembly/CAT/SAMPLE/SAMPLE_CAT.log
│   │       ├── output/Assembly/CAT/SAMPLE/SAMPLE.contig2classification.named.txt
│   │       ├── output/Assembly/CAT/SAMPLE/SAMPLE.contig2classification.txt
│   │       ├── output/Assembly/CAT/SAMPLE/SAMPLE.ORF2LCA.txt
│   │       ├── output/Assembly/CAT/SAMPLE/SAMPLE.predicted_proteins.faa
│   │       └── output/Assembly/CAT/SAMPLE/SAMPLE.predicted_proteins.gff
│   ├── output/Assembly/MEGAHIT
│   │   ├── output/Assembly/MEGAHIT/SAMPLE
│   │   │   ├── output/Assembly/MEGAHIT/SAMPLE/checkpoints.txt
│   │   │   ├── output/Assembly/MEGAHIT/SAMPLE/done
│   │   │   ├── output/Assembly/MEGAHIT/SAMPLE/final.contigs.fa
│   │   │   ├── output/Assembly/MEGAHIT/SAMPLE/intermediate_contigs
│   │   │   │   ├── ...
│   │   │   ├── output/Assembly/MEGAHIT/SAMPLE/log
│   │   │   └── output/Assembly/MEGAHIT/SAMPLE/options.json
│   │   └── output/Assembly/MEGAHIT/SAMPLE_megahit.log
│   ├── output/Assembly/MetaQUAST
│   │   ├── output/Assembly/MetaQUAST/SAMPLE
│   │   │   └── output/Assembly/MetaQUAST/SAMPLE/SAMPLE_metaquast
│   │   │       ├── ...
│   │   │       ├── output/Assembly/MetaQUAST/SAMPLE/SAMPLE_metaquast/report.pdf
│   │   │       └── output/Assembly/MetaQUAST/SAMPLE/SAMPLE_metaquast/transposed_report.txt
│   │   └── output/Assembly/MetaQUAST/SAMPLE_metaquast.log
│   ├── output/Assembly/Readmapping
│   │   └── output/Assembly/Readmapping/SAMPLE
│   │       ├── output/Assembly/Readmapping/SAMPLE/SAMPLE_jgi_summarize_depth.log
│   │       ├── output/Assembly/Readmapping/SAMPLE/SAMPLE_jgi_summarize_depth.tsv
│   │       ├── output/Assembly/Readmapping/SAMPLE/SAMPLE_pileup_cov.log
│   │       ├── output/Assembly/Readmapping/SAMPLE/SAMPLE_pileup_unmapped_merged.fastq.gz
│   │       ├── output/Assembly/Readmapping/SAMPLE/SAMPLE_pileup_unmapped_R1.fastq.gz
│   │       ├── output/Assembly/Readmapping/SAMPLE/SAMPLE_pileup_unmapped_R2.fastq.gz
│   │       ├── output/Assembly/Readmapping/SAMPLE/SAMPLE_readmapping.bam
│   │       ├── output/Assembly/Readmapping/SAMPLE/SAMPLE_readmapping.bam.bai
│   │       ├── output/Assembly/Readmapping/SAMPLE/SAMPLE_readmapping.log
│   │       └── output/Assembly/Readmapping/SAMPLE/SAMPLE_readmapping.sam
│   └── output/Assembly/sourmash
│       └── output/Assembly/sourmash/SAMPLE
│           ├── output/Assembly/sourmash/SAMPLE/SAMPLE_contigs_sourmash_k51.log
│           ├── output/Assembly/sourmash/SAMPLE/SAMPLE_contigs_sourmash_k51.sig
│           └── output/Assembly/sourmash/SAMPLE/SAMPLE_contigs_sourmash_lca.log
├── output/Benchmarking
│   ├── output/Benchmarking/Assembly
│   │   ├── output/Benchmarking/Assembly/assembly_report.tsv
│   │   ├── output/Benchmarking/Assembly/cat_SAMPLE.tsv
│   │   ├── output/Benchmarking/Assembly/MEGAHIT_SAMPLE.tsv
│   │   ├── output/Benchmarking/Assembly/metaquast_SAMPLE.tsv
│   │   ├── output/Benchmarking/Assembly/pileup_SAMPLE.tsv
│   │   ├── output/Benchmarking/Assembly/read_mapping_SAMPLE.tsv
│   │   ├── output/Benchmarking/Assembly/sourmash_contigs_k51_SAMPLE.tsv
│   │   └── output/Benchmarking/Assembly/sourmash_contigs_lca_SAMPLE.tsv
├── output/Data
│   └── output/Data/Assembly
│       ├── output/Data/Assembly/SAMPLE_contigs_cat.tsv
│       ├── output/Data/Assembly/SAMPLE_contigs_sourmash_lca.tsv
│       ├── output/Data/Assembly/SAMPLE_cov_jgi_details.tsv
│       ├── output/Data/Assembly/SAMPLE_cov_pileup_details.tsv
│       ├── output/Data/Assembly/SAMPLE_cov_pileup_summary.tsv
│       └── output/Data/Assembly/SAMPLE_metaquast.tsv
└── output/Reports
    ├── output/Reports/7assembly.html
    ├── output/Reports/CAT_krona.html
    ├── output/Reports/MetaQUAST_SAMPLE.html
    └── output/Reports/sourmash_contig_krona.html


```

See the general file information for the [`Benchmarking`](/shared_files#benchmark-files) files. MEGAHIT adds "fields" to every contig description:

<dl>
  <dt><em>flag</em></dt>
  <dd>Connectivity, 1: Standalone, 2: Looped Path, 0: Other</dd>
  <dt><em>multi</em></dt>
  <dd>Very rough coverage</dd>
  <dt><em>len</em></dt>
  <dd>Length</dd>
</dl>

### Contig coverage/depth

Two different coverage programs are applied to the mapped reads. `SAMPLE_cov_jgi_details.tsv` is produced with the METABAT2 tool `jgi_summarize_bam_contig_depths` and +`SAMPLE_cov_pileup_details.tsv` with `pileup.sh` from BBMap. 

`SAMPLE_cov_pileup_summary.tsv` has a single row for the sample with the following columns:

<dl>
  <dt><em>reads</em></dt>
  <dd>Number of read pairs in the sample</dd>
  <dt><em>mappedreads</em></dt>
  <dd>Number of read pairs that mapped to the assembly</dd>
  <dt><em>mappedbp</em></dt>
  <dd>Number of base pairs that mapped</dd>
  <dt><em>contigs</em></dt>
  <dd>Number of contigs in the assembly</dd>
  <dt><em>contigbp</em></dt>
  <dd>Number of base pairs in the assembly</dd>
  <dt><em>properpairsperc</em></dt>
  <dd>Percentage of mapped read pairs, that were mapped together</dd>
  <dt><em>avgcov</em></dt>
  <dd>Average coverage</dd>
  <dt><em>stddev</em></dt>
  <dd>Standard deviation of the coverage</dd>
  <dt><em>contigswithanycovperc</em></dt>
  <dd>Percentage of contigs which had any coverage</dd>
  <dt><em>bpswithanycovperc</em></dt>
  <dd>Percentage of base pairs which had any coverage</dd>
</dl>

On the other hand `SAMPLE_cov_pileup_details.tsv` contains one row for every contig:

<dl>
  <dt><em>contig</em></dt>
  <dd>Full name of the contig</dd>
  <dt><em>avgcov</em></dt>
  <dd>Average coverage</dd>
  <dt><em>len</em></dt>
  <dd>Contig length</dd>
  <dt><em>refgc</em></dt>
  <dd>Contig GC content</dd>
  <dt><em>covperc</em></dt>
  <dd>Percentage of bases covered</dd>
  <dt><em>plus_reads</em></dt>
  <dd>Number of reads that mapped on the + sequence</dd>
  <dt><em>minus_reads</em></dt>
  <dd>Number of reads that mapped on the reverse complemented sequence</dd>
  <dt><em>readgc</em></dt>
  <dd>Average read GC content</dd>
  <dt><em>mediancov</em></dt>
  <dd>Median coverage</dd>
  <dt><em>stddev</em></dt>
  <dd>Standard deviation of the coverage</dd>
  <dt><em>covbases</em></dt>
  <dd>Bases with coverage</dd>
</dl>

`SAMPLE_cov_jgi_details.tsv` only contains 3 columns:

<dl>
  <dt><em>contig</em></dt>
  <dd>Full name of the contig</dd>
  <dt><em>len</em></dt>
  <dd>Contig length</dd>
  <dt><em>avgcov</em></dt>
  <dd>Average coverage</dd>
  <dt><em>var</em></dt>
  <dd>Coverage variance</dd>
</dl>

### Contig classification

The `SAMPLE_contigs_cat.tsv` contains the CAT output for every contig.

<dl>
  <dt><em>contig</em></dt>
  <dd>Full name of the contig</dd>
  <dt><em>classified</em></dt>
  <dd>Was this contig classified (unclassified/classified)</dd>
  <dt><em>reason</em></dt>
  <dd>Reason (ORF hit count) for this classification</dd>
  <dt><em>lineage</em></dt>
  <dd>; seperated tax id lineage</dd>
  <dt><em>lineage scores</em></dt>
  <dd>Fraction of the ORFs that agree on the different levels (; seperated)</dd>
  <dt>Tax columns (<em>superkingdom</em>)</dt>
  <dd>The tax names ("*" from CAT removed)</dd>
  <dt>Tax scores (<em>superkingdom_score</em>)</dt>
  <dd>Fraction of the ORFs that agree on that classification</dd>
  <dt>Monocladic tax columns  (<em>superkingdom_monocladic</em>)</dt>
  <dd>This classification has no siblings in the database (CAT "*" mark)</dd>
</dl>

`SAMPLE_contigs_sourmash_lca.tsv` uses the same format as the [read classification Sourmash LCA](/KnuttReads2Bins/ReadClassification#polynucleotide-hash-sketches-sourmash) file. The same scaling factor (1000 default) is applied to the assembly.
