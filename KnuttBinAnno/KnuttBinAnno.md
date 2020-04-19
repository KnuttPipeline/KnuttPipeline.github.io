---
layout: default
title: KnuttBinAnno
nav_order: 4
permalink: /KnuttBinAnno
has_children: false
has_toc: false
---

# KnuttBinAnno
{: .no_toc}

[**KnuttBinAnno**](https://github.com/KnuttPipeline/KnuttBinAnno) combines different annotation pipelines into a single one while automating the
installation process.

The pipelines are: **MetaErg**, **dbCAN2**, **EggNOGMapper2**, **KofamKOALA**, **InterProScan** and **HydDB**. The HydDB annotation uses the online web service  and therfore requires an **internet connection**. InterProScan tries to use the online lookup service to speed up the annotation, but it can operate without it.

## Quickstart

The `all` rule combines the tools given in the config file into a single tsv and gff file for the predicted CDS. **MetaErg output is not included**.
1. Place your bin files (DNA) in the `input/` folder.
2. Check the `config.yml`
3. Use the following schema for your read files: `BIN.fa`
4. Run the whole workflow with:

``` sh
snakemake -kj 32 --use-conda all allReport 2>&1 | tee run.log & disown
```

#### Table of contents
{: .no_toc }

1. TOC
{:toc}

---

## MetaErg

MetaErg is the first pipeline and is **always** run, as it needs to run on the contigs directly and executes **Prodigal**. The **vCPU limitation isn't working correctly**, as MetaErg starts multiple processes with the given coure count at the same time. The rule for execution is `metaerg` and `metaergRefData` for the installation.

#### Output files
{: .no_toc }
``` tree
output
├── output/Benchmarking
│   └── output/Benchmarking/metaerg_BIN.tsv
└── output/MetaErg
    └── output/MetaErg/BIN
        ├── output/MetaErg/BIN/BIN_ddmmyyyy.fna
        ├── output/MetaErg/BIN/data
        │   ├── output/MetaErg/BIN/data/XXSrRNA.ffn
        │   ├── output/MetaErg/BIN/data/all.gff
        │   ├── output/MetaErg/BIN/data/cds.faa
        │   ├── output/MetaErg/BIN/data/cds.ffn
        │   ├── output/MetaErg/BIN/data/crispr.ffn
        │   ├── output/MetaErg/BIN/data/crispr.tab.txt
        │   ├── output/MetaErg/BIN/data/master.gff.txt
        │   ├── output/MetaErg/BIN/data/master.stats.txt
        │   ├── output/MetaErg/BIN/data/master.tbl.txt
        │   ├── output/MetaErg/BIN/data/master.tsv.txt
        │   ├── output/MetaErg/BIN/data/rRNA.tab.txt
        │   ├── output/MetaErg/BIN/data/taxon.cds.profile.datatable.json
        │   ├── output/MetaErg/BIN/data/taxon.cds.profile.tab.txt
        │   ├── output/MetaErg/BIN/data/taxon.lsu.profile.tab.txt
        │   ├── output/MetaErg/BIN/data/taxon.ssu.profile.tab.txt
        │   ├── output/MetaErg/BIN/data/tRNA.ffn
        │   ├── output/MetaErg/BIN/data/tRNA.tab.txt
        │   │── output/MetaErg/BIN/data/tRNA.tab.txt
        │   └── ...
        ├── output/MetaErg/BIN/index.html
        └── output/MetaErg/BIN/metaerg_run.log
```

See the general file information for the [`Benchmarking`](/shared_files#benchmark-files) files. Not all files are shown. The `index.html` is not standalone, it uses the `.json` files in the data directory for example.

To increase the ability to scale the execution of the workflow on HPC clusters, the **CDS fasta files are split into multiple chunks**. This can be configured in the config file. The `split` rule runs the splitting.

Some annotation tools and reports use the KEGG database. Make sure your use case is within the license agreement. 

## dbCAN2

At the time of the implementation the offical dbCAN2 workflow script was a little bit unstable and not distributable, therefore it has been **reimplemented** into this pipeline. You may want to compare results.

The reference data is created with `dbCANRefData`. The annotation is run with the `dbCAN` rule. A report is available with `dbCANReport`. The results are in the `output/dbCAN/BIN/BIN_dbCAN_all.tsv` file. It it is the base for a GFF3 file, you can use the following R code to extract data from those files:

``` R
source("scripts/commonGFF3Tools.R")
library(data.table)
library(tidyr)
bins <- list.files("output/dbCAN/")

dbcan <- paste0("output/dbCAN/", bins, "/", bins, "_dbCAN_all.tsv")
dbcan <- rbindlist(lapply(seq_along(bins), function(i)cbind(bin=bins[[i]], readBaseGFF(fread(dbcan[[i]])))))
cazydat <- as.data.table(unnest(as_tibble(dbcan[, .(bin, CAZY)]), "CAZY"))
cazydat[,class:=sub("^(\\D+)\\d.*", "\\1", CAZY)]
bincount <- cazydat[, .N, by=c("bin", "class", "CAZY")]
```

A custom version of a CGC finder places proposed clusters in `output/dbCAN/BIN/BIN_dbCAN_cgc.tsv`

``` R
cgc <- paste0("output/dbCAN/", bins, "/", bins, "_dbCAN_cgc.tsv")
cgc <- rbindlist(lapply(seq_along(bins), function(i)cbind(bin=bins[[i]], fread(cgc[[i]]))))
listcols <- c("members","sources")
cgc[, (listcols):=lapply(.SD,strsplit,split="|;|", fixed=T), .SD=listcols]
cgc <- as.data.table(unnest(cgc, all_of(listcols)))
```

## eggNOG Mapper

The annotation tool of the eggNOG database can be run with: `eggNOGRefData`, `eggNOG` and `eggNOGReport`.

The file `output/eggNOG/BIN/BIN_eggNOG.tsv` also uses the GFF base format:

``` R
eggnog <- paste0("output/eggNOG/", bins, "/", bins, "_eggNOG.tsv")
eggnog <- rbindlist(lapply(seq_along(bins), function(i)cbind(bin=bins[[i]], readBaseGFF(fread(eggnog[[i]])))))
```

## InterProScan

The following rules allow the execution of InterProScan: `interProRefData`, `interPro` and `interProReport`.

If you get this error: `libdw.so.1: cannot open shared object file: No such file or directory`, install `elfutils` on your system. If this is not possiblle, you can also turn off the `RPSBLAST` search by changing the InterProScan command line call.

The file `output/InterProScan/BIN/BIN_iprscan.tsv ` contains the following columns:

<dl>
  <dt><em>seqid</em></dt>
  <dd>Short ID of the CDS</dd>
  <dt><em>md5</em></dt>
  <dd>MD5 of the CDS aa sequence</dd>
  <dt><em>len</em></dt>
  <dd>Length of that sequence</dd>
  <dt><em>analysis</em></dt>
  <dd>Annotation tool/database</dd>
  <dt><em>sigacc</em></dt>
  <dd>Accession in the database</dd>
  <dt><em>sigdescr</em></dt>
  <dd>Human readable name of the signature hit</dd>
  <dt><em>start</em></dt>
  <dd>CDS start</dd>
  <dt><em>stop</em></dt>
  <dd>CDS end</dd>
  <dt><em>score</em></dt>
  <dd>Score or e-value, depending on the tool</dd>
  <dt><em>status</em></dt>
  <dd>Always T</dd>
  <dt><em>date</em></dt>
  <dd>Date of the annotation</dd>
  <dt><em>interpro</em></dt>
  <dd>IPR accession</dd>
  <dt><em>interprodescr</em></dt>
  <dd>Human readadble name of the InterPro entry</dd>
  <dt><em>GO</em></dt>
  <dd>GO terms (| seperated)</dd>
  <dt><em>pathways</em></dt>
  <dd>Pathways and term within (| seperated)</dd>
</dl>

## KofamKOALA

KofamKOALA is available through the following rules: `kofamRefData` and `kofam`.

The filtering of hits in KofamKOALA is based on **score thresholds** which are based on the score distribution of known family members. Some terms are to **rare** for this calculation. The files with `sure` only contain entries where a threshold was **available and exceeded**. `all` used the e-value in the config file to filter hits **without a score threshhold**.

The results are contained in a GFF base file (`output/KofamKOALA/BIN/BIN_KofamKOALA_gffbase.tsv`) and a normal tsv file (`output/KofamKOALA/BIN/BIN_KofamKOALA.tsv`).

<dl>
  <dt><em>hit</em></dt>
  <dd>Did this match meet the threshold</dd>
  <dt><em>seqid</em></dt>
  <dd>Short ID of the CDS</dd>
  <dt><em>KO</em></dt>
  <dd>K term hit</dd>
  <dt><em>thrshld</em></dt>
  <dd>Score threshold for this term</dd>
  <dt><em>score</em></dt>
  <dd>Score of this match</dd>
  <dt><em>eval</em></dt>
  <dd>E-value of this match</dd>
  <dt><em>descr</em></dt>
  <dd>Human readable name of the term</dd>
</dl>

`output/KofamKOALA/BIN/BIN_KofamKOALA_kos_all.tsv` and `output/KofamKOALA/BIN/BIN_KofamKOALA_kos_sure.tsv` can be uploaded to the KEGG BRITE mapping tool. This module search is also performed in the workflow. Results should be similar.

Module search results are stored in the files `output/KofamKOALA/BIN/BIN_KofamKOALA_kos_sure_modules.tsv` and `output/KofamKOALA/BIN/BIN_KofamKOALA_kos_all_modules.tsv`.

<dl>
  <dt><em>module</em></dt>
  <dd>Accession of the module</dd>
  <dt><em>name</em></dt>
  <dd>Human readable name</dd>
  <dt><em>Module.Type</em></dt>
  <dd>Type (Pathway/Signature)</dd>
  <dt><em>Upper.Class</em></dt>
  <dd>Carbohydrate/Amino acid metabolism for example</dd>
  <dt><em>Lower.Class</em></dt>
  <dd>Subtypes</dd>
  <dt><em>allterms</em></dt>
  <dd>All required terms in the module</dd>
  <dt><em>hitterms</em></dt>
  <dd>Terms found in the bins</dd>
  <dt><em>alloptionals</em></dt>
  <dd>All optional terms</dd>
  <dt><em>hitoptionals</em></dt>
  <dd>Optional terms in the bin</dd>
  <dt><em>optionalblockcount</em></dt>
  <dd>Number of optional blocks</dd>
  <dt><em>optionalblockhits</em></dt>
  <dd>Optional blocks complete</dd>
  <dt><em>blockcount</em></dt>
  <dd>Number of blocks in the module</dd>
  <dt><em>blockhits</em></dt>
  <dd>Completed blocks</dd>
  <dt><em>completion</em></dt>
  <dd>Number of completed blocks divided by the total block count</dd>
  <dt><em>hits</em></dt>
  <dd>CDS contributing to this module</dd>
  <dt><em>optionals</em></dt>
  <dd>(Optional) CDS contributing to this module</dd>
</dl>

## HydDB

The HydDB classifier is integrated into the workflow using the online tool. CDS selection is done using an RPSBLAST search. Rules for this step are:
`hydDB`, `hydDBRefData` and `hydDBReport`

The results are placed in the following file: `output/HydDB/BIN/BIN_HydDB.tsv`

<dl>
  <dt><em>query</em></dt>
  <dd>Short ID of the CDS</dd>
  <dt><em>subject</em></dt>
  <dd>RPSBLAST Hit</dd>
  <dt><em>identity</em></dt>
  <dd>Identity to the pattern</dd>
  <dt><em>allength</em></dt>
  <dd>Alignment length</dd>
  <dt><em>mismatches</em></dt>
  <dd>Mismatches on the alignment</dd>
  <dt><em>gapopenings</em></dt>
  <dd>Gap regions opened</dd>
  <dt><em>querystart</em></dt>
  <dd>Start on the CDS</dd>
  <dt><em>queryend</em></dt>
  <dd>End on the CDS</dd>
  <dt><em>subjectstart</em></dt>
  <dd>Start on the pattern</dd>
  <dt><em>subjectend</em></dt>
  <dd>End on the pattern</dd>
  <dt><em>eval</em></dt>
  <dd>E-value</dd>
  <dt><em>score</em></dt>
  <dd>Bitscore</dd>
  <dt><em>center</em></dt>
  <dd>Config defined group of this CDD</dd>
  <dt><em>shortname</em></dt>
  <dd>Short human readable name</dd>
  <dt><em>name</em></dt>
  <dd>Description of the CDD</dd>
  <dt><em>subjectlength</em></dt>
  <dd>Length of the pattertn</dd>
  <dt><em>hitid</em></dt>
  <dd>Short ID of the CDS:Query Start:Query End, used during submission</dd>
  <dt><em>Classification</em></dt>
  <dd>Classification returned by the online service</dd>
</dl>
