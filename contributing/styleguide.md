---
layout: default
title: Styleguide
nav_order: 3
permalink: /contrib/style
---
# Styleguide

Some notes on how to format code contained in the MetaKnutt projects. Mostly based on PEP-8 for Snakefile and Python files.

## Source code in general

Remember to add a shebang line to your files. Use the `env` utility to allow custom installations.

Snakemake: None
Bash: `#!/usr/bin/env bash`
R Scripts: `#!/usr/bin/env Rscript`
Python: `#!/usr/bin/env python3`

Always use spaces, never use tabs. One level should be three spaces.

Lines, especially comments, should be limited to 72 characters. Longer lines can be at most 119 characters long.

## Snakefiles

Every pipeline has one `Snakefile` importing one `Snakefile_PIPELINE`. The `Snakefile_PIPELINE` can use multiple inclusions of other Snakefiles. The main `Snakefile` defines the configfile (YAML format, `config.yml`). Config entries need to have an comment above them explaining them and should link to more information in publications or manuals. The main input and output directory has to be configurable with a name specific to this pipeline. Wildcard patterns for input files also neeed to be configurable.

Simple directives/expressions should have a comment above them, if their purpose is not clear. 

Header template:
```
##
## FILENAME - short description
## project.link/here

# Longer
# description
# here (Unlimited lines)

import os # Imports here
import re

include: "greatfile" # Includes here
include: "greatfile2" 
```

Rules should have a short description above them. 

Rule/function/variable names should be lower case with _ seperating words.

Rules meant to by called by the user should use CamelCasing.

Path concatenating with a+"/b" is fine inside rules.

Places everything except thread counts on the line after the directive.

Output files should look like this:
"outputdir/onestepdir/sample/sample_readorsth_filename"

## YAML files

Their file ending should be `.yml`. The header description lines are the same ones shown for Snakefiles.



