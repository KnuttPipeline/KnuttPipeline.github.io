
# Using KnuttReads2Bins

This guide will give you information on how to run the KnuttReads2Bins
pipeline and the schemata the output files have.

You only need to run the step producing the files you want to use, all
preceding files will be generated automatically. If you tell Snakemake
to perform the binning, read preparation and assembly will be run aswell.

## Supplying your samples

Open the `config.yml` and take a look at the options in the first section.

You can either choose to rename your read files before placing/symlinking
them in the `input` directory or change the naming pattern. For directly
using Illumina MiSeq exported reads (`<run>/Data/Intensities/BaseCalls/`)
it might look something like this:

```yaml
paired_read_files: "{sample}_L001_{read}_001.fastq.gz"
```

The reference data directory will be quite good. If you are using it
with multiple users, see [AAAAAAAAAA](google.com) how to share the
reference data.

## 1. Read preparation

All read preparation steps can be executed with the following rules.

| Rule type | Rule | Description | Output files | File description |
|-----------|-----------------------|--------------------------------------------------|--------------------------------------------------------------------|--------------------|
| Tooling | `readPreparation` | Produce all FASTQ files used in subsequent steps | See below | All FASTQ files |
| Tooling | `readPreparationQC` | All FASTQC reports for these files | See below | All FASTQC reports |
| Data | `readPreparationData` | Combine all data files for every read and sample | `Data/ReadPrep/ReadPrep.tsv` `Data/ReadPrep/ ReadPrepDetails .tsv` | see here |

### 1.1 Manual read quality checking

The first step consists of multiple substeps: adapter trimming (optional)
and read merging. For read annotation and classification additional step
are quality trimming, sampling (optional) and low complexity masking.

The workflow can give you information on the supplied read files. This
is done with FASTQC and a integrated report.

| Rule type | Rule | Description | Output files | File description |
|-----------|-------------|--------------------------------------------|---------------------------------------------------------------------------|--------------------------------------------------------------------------------------|
| Tooling | `rawQC` | Produce all FASTQC reports | `ReadPrep/Reads_QC/{s}/{s}_{r}.{zip&html}` | The FASTQC HTML report and a zip with the figures as png files and the sampled data. |
| Data | `rawSeqData` | Calculate quality/length data | `Data/Sequence_Data/raw_overview.tsv` `Data/Sequence_Data/raw_toplot.tsv` | Data describing the raw FASTQ files, see [here](google.de) for more info. |
| Report | `rawReport` | Render a report comparing the raw samples. | Reports/1raw-reads.html | A Flexdashboard/Plotly report comparing the raw sequence files provided |

### 1.2 Adapter trimming

If the template molecule for a cluster during sequence is shorter than
the cycle count, the 3' end of that read will contain the adapter sequence.
Some protocols like amplification or pooling not using the Illumina
pooling protocol may also introduce adapter sequences. These adapter sequences
are a technical artifact and need to be trimmed. This trimming is performed
cutadapt.

Adapter trimming can be turned off in the configuration. Trimmed reads are used for read
classification, read annotation and assembly. The reads mapped to the
the assembly are always the raw, untrimmed  ones.

| Rule type | Rule | Description | Output files | File description |
|-----------|-----------------------|--------------------------------------------|-----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|
| Tooling | `trimQC` | Produce all FASTQC reports | `ReadPrep/Reads_QC/{s}/{s}_{r}.{zip&html}` | The FASTQC HTML report and a zip with the figures as png files and the sampled data. |
| Tooling | `trimAdapters` | Run cutadapt | `ReadPrep/Adapter_Trimming/{s}/{s}_{r}_adptr_tr.fastq.gz` | Adapter trimmed FASTQ files |
| Data | `trimmedSeqData` | Calculate quality/length data | `Data/Sequence_Data/trimmed_overview.tsv` `Data/Sequence_Data/trimmed_toplot.tsv` | Data describing the raw FASTQ files, see [here](google.de) for more info. |
| Data | `adapterTrimmingData` | Collect cutadapt report data | `Data/ReadPrep/cutadapt_adptr_trimming.tsv` | Cutadapt statistics for every sample |
| Report | `trimReport` | Render a report comparing the raw samples. | Reports/1raw-reads.html | A Flexdashboard/Plotly report comparing the sequences after adapter trimming. Uses the merge results for adapter reporting! |

### 1.3 Read merging

The R1 and R2 reads are reverse complementary to each other and start on
opposite ends of the template. They can therefore overlap. This can be used
to generate longer reads by merging R1 and R2 on this overlap. Merging is
performed with BBmerge.

| Rule type | Rule | Description | Output files | File description |
|-----------|-------------------|------------------------------------------------|------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Tooling | `mergeQC` | Produce all FASTQC reports | `ReadPrep/Merging_{tr&untr}/{s}/{s}_{tr&untr}_{merged&unmgd_R1&unmgd_R2}_fastqc.{zip&html}` | The FASTQC HTML report and a zip with the figures as png files and the sampled data. |
| Tooling | `merge` | Run BBmerge on the trimmed and untrimmed reads | `ReadPrep/Merging_{tr&untr}/{s}/{s}_{tr&untr}_{merged&unmgd_R1&unmgd_R2}.fastq.gz` | Merged and not merged reads for adapter trimmed and untrimmed input files |
| Data | `mergeSeqData` | Calculate quality/length data | `Data/Sequence_Data/merging_{tr&untr}_overview.tsv` `Data/Sequence_Data/merging{tr&untr}_toplot.tsv` | Data describing the merged FASTQ files, see [here](google.de) for more info. |
| Data | `mergeData` | Collect BBmerge overview data | `Data/ReadPrep/bbmerge_overview_adptr_{tr&untr}.tsv` | BBmerge overview |
| Data | `mergeInsertData` | Collect BBmerge per read info | `Data/ReadPrep/bbmerge_inserts_adptr_{tr&untr}.tsv` | The results for every read pair |
| Report | `mergeReport` | Render a report comparing the merge results | Reports/3read-merging.html | A Flexdashboard/Plotly report comparing the sequences after adapter merging. Uses the trimmed and untrimmed sequences. |

### 1.4 Read preparation for annotation

For classification and annotation the reads are masked, quality trimmed and sampled
(optionally). The merged and unmerged R1 reads are used, not the unmerged R2, to prevent
overestimation. This may change in future versions.

| Rule type | Rule | Description | Output files | File description |
|-----------|--------------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------|
| Tooling | `qualityTrimQC` | Produce all FASTQC reports | `ReadPrep/Merging_{tr&untr}/{s}/{s}_{tr&untr}_{merged&unmgd_R1}_qtr_fastqc.{zip&html}` | The FASTQC HTML report and a zip with the figures as png files and the sampled data. |
| Tooling | `qualityTrim` | Perform quality trimming | `ReadPrep/Merging_{tr&untr}/{s}/{s}_{tr&untr}_{merged&unmgd_R1}_qtr.fastq.gz` | Quality trimmed merged and unmerged R1 (For adapter trimmed and not trimmed file) |
| Data | `qualityTrimSeqData` | Calculate quality/length data | `Data/Sequence_Data/qctrimmed_{tr&untr}_overview.tsv` `Data/Sequence_Data/qctrimmed_{tr&untr}_toplot.tsv` | Data describing the merged FASTQ files, see [here](google.de) for more info. |
| Data | `qulityTrimData` | Get cutadapt output | `Data/ReadPrep/classreads_qualtrim_adptr_{tr&untr}.tsv` | Cutadapt minimal report output |
| Tooling | `annotationReadsQC` | FASTQC for annotation input files | `ClassificationReads/{s}/{s}_{tr/untr}_classreads_{smpld/unsmpld}_fastqc.{zip&html}` | FASTQC report files for the reads used for annotation |
| Tooling | `annotationReads` | Produce the reads for annotation | `ClassificationReads/{s}/{s}_{tr/untr}_classreads_{smpld/unsmpld}.fastq.gz` | Sampled (if configured) |
| Data | `annotationReadsSeqData` | Quality/Length data | `Data/Sequence_Data/readanno_overview.tsv` `Data/Sequence_Data/readanno_toplot.tsv` |  |
