---
layout: default
title: Read annotation
nav_order: 3
permalink: /KnuttReads2Bins/ReadAnnotation
parent: KnuttReads2Bins
has_toc: false
---
## Read Annotation
{: .no_toc}

For some applications running an assembly and binning process might not be necessary. **KnuttReads2Bins** uses DIAMOND BLASTX searches against
protein databases to provide compositional insights into a sample. The **problems** with a simple BLAST search for functional annotation should be considered, when using data from this step.

The **HydDB** and **dbCAN (CAZyDB)** protein databases are integrated into the workflow. Users can create custom databases by providing **UniProtKB queries**.
The Krona reports use the taxonomy of the entries in the databases and the BLAST search results, but for dbCAN/CAZy an additional functional
Krona is available.

The steps for this stage are defined in the [`Snakefile_3AnnotateReads`](https://github.com/KnuttPipeline/KnuttReads2Bins/blob/master/Snakefile_3AnnotateReads) file. With two custom databases the reference data requires around 1 GB and 9 GB for the NCBI taxonomy.

<dl>
  <dt><em>Small metagenome</em></dt>
  <dd>Tbd. GB</dd>
  <dt><em>Medium metagenome</em></dt>
  <dd>Tbd. GB</dd>
  <dt><em>Large metagenome</em></dt>
  <dd>Tbd. GB</dd>
</dl>

Reference data can be generated with the rule `readAnnoRefData`. All classification steps can be run with the `readAnno`, `readAnnoKrona` and `readAnnoReport` rules.

Outputs are stored in `<outputdir>/ReadAnnotation/`, `<outputdir>/Data/ReadAnnotation/` and `<outputdir>/Reports/`.

---

The files for **specific databases** can be produced with `readAnnoDATABASEDBRefData`, `readAnnoDATABASE` and `readAnnoDATABASEKrona`. The functional Krona is currently only available for CAZy. `DATABASE` can be `Custom` for the UniProtKB based databases.

#### Output files
{: .no_toc }

``` tree
output
├── output/Benchmarking
│   └── output/Benchmarking/ReadAnnotation
│       └── output/Benchmarking/ReadAnnotation/diamond_run_DATABASE_SAMPLE.tsv
├── output/Data
│   └── output/Data/ReadAnnotation
│       └── output/Data/ReadAnnotation/SAMPLE_readanno_DATABASE.tsv
├── output/ReadAnnotation
│   └── output/ReadAnnotation/SAMPLE
│       ├── output/ReadAnnotation/SAMPLE/SAMPLE_prot_DATABASE_blast.log
│       └── output/ReadAnnotation/SAMPLE/SAMPLE_prot_DATABASE_blast.tsv
└── output/Reports
    ├── output/Reports/6readanno_DATABASE.html
    ├── output/Reports/readanno_CAZyDB_funkrona.html
    └── output/Reports/readanno_DATABASE_krona.html

```

Current database options are:
<dl>
  <dt><em>CAzyDB</em></dt>
  <dd>dbCAN2 project based database</dd>
  <dt><em>HydDB</em></dt>
  <dd>Sequences in the hydrogenase database project</dd>
  <dt><em>Custom</em></dt>
  <dd>Produce files for all custom databases</dd>
</dl>

See the general file information for the [`Benchmarking`](/shared_files#benchmark-files) files. The `SAMPLE_readanno_DATABASE.tsv` file contains the BLAST hits with the lowest e-value for each query.

<dl>
  <dt><em>qname</em></dt>
  <dd>Full id of the read</dd>
  <dt><em>sname</em></dt>
  <dd>Full id of the subject</dd>
  <dt><em>pident</em></dt>
  <dd>Percentage of identical base pairs in the alignment</dd>
  <dt><em>length</em></dt>
  <dd>Alignment length</dd>
  <dt><em>mismatch</em></dt>
  <dd>Number of mismatches</dd>
  <dt><em>gapopen</em></dt>
  <dd>Number of gap sequences opened</dd>
  <dt><em>qstart</em></dt>
  <dd>Start in the read</dd>
  <dt><em>qend</em></dt>
  <dd>End in the read</dd>
  <dt><em>sstart</em></dt>
  <dd>Start on the subject</dd>
  <dt><em>send</em></dt>
  <dd>End on the subject</dd>
  <dt><em>evalue</em></dt>
  <dd>E value</dd>
  <dt><em>bitscore</em></dt>
  <dd>Bitscore</dd>
  <dt><em>staxids</em></dt>
  <dd>All subject tax ids</dd>
  <dt><em>qlen</em></dt>
  <dd>Full length of the read</dd>
  <dt><em>slen</em></dt>
  <dd>Full length of the subject</dd>
</dl>

After these columns the data from the database follows.

---

#### CAZyDB
{: .no_toc }

<dl>
  <dt><em>taxid</em></dt>
  <dd>Tax id</dd>
  <dt><em>CAZyECs</em></dt>
  <dd>Space seperated list of CAZy classes</dd>
  <dt>Tax columns</dt>
  <dd>Taxonomy of the entry</dd>
</dl>

---

#### HydDB
{: .no_toc }
<dl>
  <dt><em>taxid</em></dt>
  <dd>Tax id</dd>
  <dt><em>Date1</em></dt>
  <dd>Added to the DB (?)</dd>
  <dt><em>Date2</em></dt>
  <dd>Updated to the DB (?)</dd>
  <dt><em>HydDB_species</em></dt>
  <dd>Species in HydDB</dd>
  <dt><em>HydrogenaseClass</em></dt>
  <dd>HydDB class</dd>
  <dt><em>Sequence</em></dt>
  <dd>Aminoacid sequence</dd>
  <dt><em>nt_sequence1</em></dt>
  <dd>Not always set</dd>
  <dt><em>nt_sequence2</em></dt>
  <dd>Not always set</dd>
  <dt><em>HydDB_phylum</em></dt>
  <dd>HydDB phylum</dd>
  <dt><em>HydDB_order</em></dt>
  <dd>HydDB order</dd>
  <dt><em>PredictedActivity</em></dt>
  <dd>Type of hydrogenase activity</dd>
  <dt><em>PredictedOxyTolerance</em></dt>
  <dd>Type of oxygen tolerance</dd>
  <dt><em>PredictedSubunitsNumber</em></dt>
  <dd>Number of subunits</dd>
  <dt><em>PredictedMetalCentres</em></dt>
  <dd>Ion center</dd>
  <dt><em>PredictedSubunits</em></dt>
  <dd>Subunit types</dd>
  <dt>Tax columns</dt>
  <dd>(Current) Taxonomy of the entry</dd>
</dl>

---

#### Custom
{: .no_toc }
<dl>
  <dt><em>Entry</em></dt>
  <dd>Entry ID</dd>
  <dt><em>Entry name</em></dt>
  <dd>Entry name</dd>
  <dt><em>Status</em></dt>
  <dd>reviewed or unreviewed</dd>
  <dt><em>Protein names</em></dt>
  <dd>Human readable name</dd>
  <dt><em>Gene names</em></dt>
  <dd>Genes encoding for this protein</dd>
  <dt><em>Organism</em></dt>
  <dd>Human readable organism name</dd>
  <dt><em>Cross-reference (CAZy)</em></dt>
  <dd>Annotation in the CAZy project</dd>
  <dt><em>Pathway</em></dt>
  <dd>UniProt Function: Pathway</dd>
  <dt><em>EC number</em></dt>
  <dd>UniProt EC numbers</dd>
  <dt><em>Organism ID</em></dt>
  <dd>Tax id of the organism</dd>
  <dt><em>Taxonomic lineage IDs</em></dt>
  <dd>Should be the same as Organism ID</dd>
  <dt><em>Sequence</em></dt>
  <dd>Full aminoacid sequences</dd>
  <dt><em>Cross-reference (KO)</em></dt>
  <dd>KEGG Orthology(KO) terms, only for entries in KEGG</dd>
  <dt>Tax columns</dt>
  <dd>Taxonomy of the entry (based on the Organism ID)</dd>
</dl>

---

### Defining custom databases

Additional databases can be added by placing queries to the UniProtKB database in `data/readanno_DATABASE.tsv`.

The first row is ignored and the second column is used for the terms, they are **chained with `OR`**.

``` tsv
group	query
Formate dehydrogenase ec:"1.17.1.9"
Formate dehydrogenase (NADP(+))	ec:"1.17.1.10"
Formate dehydrogenase (NAD(+), ferredoxin)	ec:"1.17.1.11"
Formate dehydrogenase-N	ec:"1.17.5.3" 
Formate dehydrogenase (coenzyme F420)	ec:"1.17.98.3" OR database:(type:ko k22516) OR database:(type:ko k22516) OR database:(type:ko k00125)
Formate dehydrogenase (acceptor)	ec:"1.17.99.7"
```