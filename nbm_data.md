---
title: FCC NBM data
date: last-modified
draft: false
format:
  gfm:
    variant: +yaml_metadata_block
prefer-html: true
---


``` r
source("R/table_with_options.R")
```

This page will display all available data (minus challenge data[^1]\])
that can downloaded from FCC NBM.

## Getting the Information about NBM Release:

``` r
filing_url <- "https://broadbandmap.fcc.gov/nbm/map/api/published/filing"

# getting a list of release
get_release <- function(filing_url) {
  req <- curl::curl_fetch_memory(filing_url)
  release <- jsonlite::fromJSON(rawToChar(req$content))$data
  return(release)
}

release <- get_release(filing_url)
table_with_options(release)
```

## Getting links for every CSV in NBM:

Even if NBM have a biannual release cycle it is updated more frequently
(around every two weeks).

``` r
get_data_url <- paste0("https://broadbandmap.fcc.gov/nbm/map/api/",
                       "national_map_process/nbm_get_data_download/")

get_csv_to_dl <- function(release_file, release_nb) {
  get_data_url <- paste0(get_data_url,
                              release_file[release_nb, "process_uuid"])

  raw_dat <- curl::curl_fetch_memory(get_data_url)

  csv_to_dl <- jsonlite::fromJSON(rawToChar(raw_dat$content))$data
  csv_to_dl[["release"]] <- release_file[release_nb, "filing_subtype"] 
  return(csv_to_dl)
}

big_list <- lapply(1:nrow(release), get_csv_to_dl, release_file = release)

all_data <- do.call(rbind, big_list)

col_to_keep <- c("release", "data_type", "technology_code", "state_fips", "provider_id", "file_name", "file_type", "data_category")

slim_all_data <- all_data[, col_to_keep]

fixed <- 
  slim_all_data[slim_all_data$data_type == "Fixed Broadband" | slim_all_data$data_category == "Nationwide" , ]

table_with_options(fixed)
```

[^1]: this data is stored in a slighly different endpoint see
