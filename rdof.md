---
title: RDOF EDA
date: last-modified
draft: false
format:
  gfm:
    variant: +yaml_metadata_block
prefer-html: true
---


We had some previous works on RDOF for BCAT. It used shapefiles and gpkg
to produce the output. This was not needed anymore as we could relly on
census boundary (with less topological error) and just use the
spreadsheet profided (`.xls`).

If we were about to change our workflow it was a good opportunity to
test the end results.

The R script used for that can be find in the doc section of
[data-bead-etl
repo](https://github.com/ruralinnovation/data-BEAD-ETL/tree/main/doc).

Here I will summarize some of the key results and dive a bit more on the
trouble we ran into.

## Key results:

- We do not have any difference between our previous pipeline and the
  new one for “Authorized RDOF”

- In 2024 they are no difference in “ready to authorized” to
  “authorized”: everything was authorized.

- We also verified that RDOF data is matching to a Census block 2010
  geoid.

- We have data issue between “default”: ie we have two contradictory
  data sources.

## Two data sources:

Our previous works was on a release from the end of 2022 (`16dec2022`
according to the file name) and the new one end of 2023 (`2023-12-20`
according to the file name).

The version of 2022 have 266 994 and 286 892 for 2023. Our first thought
was that it makes sense to have nore “default” as time go but some cases
of default in 2022 are missing in 2023 (is it possible for a default
RDOF to be not defaulted?).

48 121 rows are only present in 2022 (they are the potentially weirds
one). 68 019 rows are only present in 2023 version. Finally, 218873 are
identics.

### Other small “hiccups”

We have some cases that were defaulted but still are present in
authorized.: 47 cases, all in Wisconsin (County: 55043) and same
company.

They will be removed from Authorized.

### Using High Cost: Connect America Fund Broadband Map ([CAF Map](https://opendata.usac.org/High-Cost/High-Cost-Connect-America-Fund-Broadband-Map-CAF-M/r59r-rpip/about_data))

This data has a column for “Fund type” and one of this type is RDOF. It
has 447 939 rows matching for RDOF. Unlike RDOF this data set is at the
“location” scale, ie we can have multiple location per block.

We only get 54 825 Census Blocks, RDOF authorized has 489 811.

The vast majority of those are already present in RDOF data set, only
175 are ony present in the CAF data set.

According to the metadata it seems that the CAF data set was produced in
september 2023 but they did not provide their sources. It is hard to
tract what was used.

Our recomendation: use FCC source has much has possible and be mindfull
when using other data sets.
