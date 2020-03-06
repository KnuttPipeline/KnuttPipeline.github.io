# Data Schemata

This documents describes the data format of the produced files. Try to use
name base references to the columns in your scripts/programs, as new columns
might be added over time. See the changelog for potential changes of names.

[Here](google.de) are some examples how to effectively use the data sets with R.

## FASTQ data files

Files describing read length, quality, GC content, etc. from a FASTQ file.

### Overview

Location: `Data/Sequence_Data/{seqtype}_overview.tsv`
Rows: Files
Tool: Custom R script using [ShortReads](google.de)

|  | sample | readdirection | minReadLen | quantile25ReadLen | medianReadLen | quantile75ReadLen | maxReadLen | meanReadLen | reads | basepairs |
|-----------------|----------------------------|----------------------------------------------|----------------------|-----------------------------|--------------------|-----------------------------|---------------------|---------------------|------------|-----------------------|
| Description | The name of the sample | Type of reads this file contained | Shortest read length | 25th percentile read length | Median read length | 75th percentile read length | Longest read length | Average read length | Read count | Total base pair count |
| Data type | String | String | Integer | Integer | Integer | Integer | Integer | Float | Integer | Integer |
| Expected values | User provided sample names | R1, merged, unmgd_R2, merged_and_R1_qtr, ... | >30 | Depends | Depends | Depends | Depends | Depends | Depends | Depends |

The read length expected depends on the origin of the sequence file (seqtype).

Read lengths for sequencer produced files depend on your cycle count during
sequencing. Reads can be have at most your cycle count in length. For a
high quality read file, even the 25th percentile read length should be near
the cycle count. The [FASTQC manual](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/3%20Analysis%20Modules/) contains help for quality control.

### FASTQ data plot files

Location: `Data/Sequence_Data/{seqtype}_toplot.tsv`
Rows: Data points

| Column Name | sample | readdirection | type | x | y | z |
|-----------------|----------------------------|----------------------------------------------|--------------------------------------------------------------------------------------------------|-------|-------|---------------------------------|
| Description | The name of the sample | Type of reads in the source FASTQ file | Data group for this point |  |  | Only used for cycle_qual_counts |
| Data type | String | String | String | Float | Float | Integer |
| Expected values | User provided sample names | R1, merged, unmgd_R2, merged_and_R1_qtr, ... | gccontent_density, avg_seq_qual_ecdf, cycle_qual_counts, readlength_density or avg_seq_qual_dens |  |  | Contains NA's! |

The following table describes the data groups.

| Data type | Description | x | y | z |
|--------------------|-------------------------------------|------------------|------------------|------------------|
| gccontent_density | Read GC content distribution | Read GC content | Density estimate | NA |
| avg_seq_qual | Mean read PHRED score distribution | Mean PHRED score | Density estimate | NA |
| cycle_qual_counts | Occurence table of the PHRED scores | Cycle/Position | PHRED score | Occurrence count |
| readlength_density | Read length distribution | Read length | Density estimate | NA |

Kernel density estimation is performed with the default values of the R density function, but limited to the range of the input values, otherwise the density estimate x values would contain not exisitng values. The area under the curve therefore isn't 1.

