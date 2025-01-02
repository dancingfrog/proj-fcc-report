---
title: FCC Funding Map
date: last-modified
draft: false
format:
  gfm:
    variant: +yaml_metadata_block
prefer-html: true
---


<script src="../../../site_libs/htmlwidgets-1.6.4/htmlwidgets.js"></script>
<link href="../../../site_libs/datatables-css-0.0.0/datatables-crosstalk.css" rel="stylesheet" />
<script src="../../../site_libs/datatables-binding-0.33/datatables.js"></script>
<script src="../../../site_libs/jquery-3.6.0/jquery-3.6.0.min.js"></script>
<link href="../../../site_libs/dt-core-1.13.6/css/jquery.dataTables.min.css" rel="stylesheet" />
<link href="../../../site_libs/dt-core-1.13.6/css/jquery.dataTables.extra.css" rel="stylesheet" />
<script src="../../../site_libs/dt-core-1.13.6/js/jquery.dataTables.min.js"></script>
<script src="../../../site_libs/jszip-1.13.6/jszip.min.js"></script>
<link href="../../../site_libs/dt-ext-buttons-1.13.6/css/buttons.dataTables.min.css" rel="stylesheet" />
<script src="../../../site_libs/dt-ext-buttons-1.13.6/js/dataTables.buttons.min.js"></script>
<script src="../../../site_libs/dt-ext-buttons-1.13.6/js/buttons.html5.min.js"></script>
<script src="../../../site_libs/dt-ext-buttons-1.13.6/js/buttons.colVis.min.js"></script>
<script src="../../../site_libs/dt-ext-buttons-1.13.6/js/buttons.print.min.js"></script>
<link href="../../../site_libs/crosstalk-1.2.1/css/crosstalk.min.css" rel="stylesheet" />
<script src="../../../site_libs/crosstalk-1.2.1/js/crosstalk.min.js"></script>

``` r
source("R/table_with_options.R")
```

## Important links:

Link: <https://fundingmap.fcc.gov/home>

Documentation: <https://us-fcc.app.box.com/v/bfm-data-downloads-output>

The download page has two tabs:

- [Funding data](https://fundingmap.fcc.gov/data-download/funding-data)

- [Unserved/unfunded](https://fundingmap.fcc.gov/data-download/unserved-data)

# Funding data

## Program Data

It is organized by Agency and can be downloaded by projects
(`fundingdata_projectXXXX`) or for all projects in a program
(`fundingdata_programXX`).

For each download it will have a `csv` about the program. For example
RDOF is just a two rows csv, header included.

## Project Data

FCC is defining 3 types of projects:

- Defined by “Area”

- Defined by “List of locations”

- Defined by “Middle Mile” (No project representing this one:
  02-05-2024)

All of those types of project will have a Project Attribute Information
table (`areaattributes_program` or `locationattributes_program`). The
structure of those files are close but not similar for example location
project has columns related to locations (`build_req`, `loc_plan`,
`loc_sup`).

The one for RDOF (`areaattributes_program24_J23_12feb2024.csv`) has 474
rows (inclunding headers ie 473 projects).

Area projects will have an associated `areapolygons_XXX.gpkg`. A quick
glance on the one from RDOF show full valid geometries with an expected
number of rows (473).

Location projects have, instead, of a `gpkg` a `csv` with `location_id`,
`lat`/`long` and addresses field (but those last those are not filled).

## List of dataset avalaible: 07-03-2024

``` r
agency_name <- "Federal Communications Commission"
program_name <- c("Bringing Puerto Rico Together",
                "Connect America Fund Phase II",
                "Connect USVI", 
                "Enhanced Alternative Connect America Cost Model",
                "Rural Digital Opportunity Fund")
program_id <-  c("25", "28", "26", "35", "24")
fcc_dat <- data.frame(agency_name = rep(agency_name, length(program_name)),
                     program_name,
                     program_id 
                      )
agency_name <- "NTIA"
program_name <- c(
  "Broadband Infrastructure Program",
  "Tribal Broadband Connectivity Program NOFO 1"
)
program_id <-  c("11", "27")
ntia_dat <- data.frame(agency_name = rep(agency_name, length(program_name)), 
                      program_name,
                      program_id 
                      )
agency_name <- "Rural Utilities Service"
program_name <- c(
  "COMMUNITY CONNECT GRANT PROGRAM",
  "RURAL ECONNECTIVITY PROGRAM",
  "TELEPHONE LOAN PROGRAM"
)
program_id <-  c("10", "6", "12")
rural_dat <- data.frame(agency_name = rep(agency_name, length(program_name)), 
                      program_name,
                      program_id 
                      )
agency_name <- "US Department of Treasury"
program_name <- c(
  "Capital Projects Fund",
  "State and Local Fiscal Recovery Fund"
)
program_id <-  c("18", "19")
usdt_dat <- data.frame(agency_name = rep(agency_name, length(program_name)), 
                      program_name,
                      program_id 
                      )

fcc_all_dat <- rbind(fcc_dat, ntia_dat, rural_dat, usdt_dat )

# ls > path/to/list_file_fcc_feb2024.txt
fcc_files <- readLines("data/list_file_fcc_feb2024.txt")
# remove zip
fcc_files_slim <- fcc_files[!grepl(".zip", fcc_files)]

fcc_files_tidy <- as.data.frame(
                                do.call(rbind, 
                                        strsplit(fcc_files_slim, "_"))
)
# remove programdata, but it is nice to see for every files 
fcc_files_tidy <- fcc_files_tidy[fcc_files_tidy[["V1"]] != "programdata",]

fcc_files_tidy[["program_id"]] <- gsub("program", "", fcc_files_tidy[["V2"]])

fcc_files_tidy[["is_area"]] <- grepl("area", fcc_files_tidy[["V1"]])

first_V4 <- function(x) {unlist(strsplit(x, ".", fixed = TRUE))[1]}

fcc_files_tidy[["file_release"]] <- sapply(fcc_files_tidy[["V4"]], first_V4)

# works for now but will breack if I have the third type of project
fcc_files_tidy[["type_proj"]] <- ifelse(fcc_files_tidy[["is_area"]], "area", "location")

type_proj_temp <- sapply(split(fcc_files_tidy[["type_proj"]], 
             fcc_files_tidy[["program_id"]]), 
             unique)
file_release <- sapply(split(fcc_files_tidy[["file_release"]], 
             fcc_files_tidy[["program_id"]]), 
             unique) 

type_proj <- data.frame(
  program_id = names(type_proj_temp),
  type_proj  = type_proj_temp, 
  # a bit lazy and should be a join 
  file_release = file_release
)

fcc_all_dat <- merge(fcc_all_dat, type_proj,
      by.x = "program_id", by.y = "program_id", 
      all.x = TRUE, all.y = TRUE)

table_with_options(fcc_all_dat)
```

<div class="datatables html-widget html-fill-item" id="htmlwidget-6e94d1bcbb9574d68f28" style="width:100%;height:auto;"></div>
<script type="application/json" data-for="htmlwidget-6e94d1bcbb9574d68f28">{"x":{"filter":"none","vertical":false,"extensions":["Buttons"],"data":[["10","11","12","18","19","24","25","26","27","28","35","6"],["Rural Utilities Service","NTIA","Rural Utilities Service","US Department of Treasury","US Department of Treasury","Federal Communications Commission","Federal Communications Commission","Federal Communications Commission","NTIA","Federal Communications Commission","Federal Communications Commission","Rural Utilities Service"],["COMMUNITY CONNECT GRANT PROGRAM","Broadband Infrastructure Program","TELEPHONE LOAN PROGRAM","Capital Projects Fund","State and Local Fiscal Recovery Fund","Rural Digital Opportunity Fund","Bringing Puerto Rico Together","Connect USVI","Tribal Broadband Connectivity Program NOFO 1","Connect America Fund Phase II","Enhanced Alternative Connect America Cost Model","RURAL ECONNECTIVITY PROGRAM"],["area","area","area","location","location","area","area","area","area","area","area","area"],["09feb2024","09feb2024","09feb2024","23feb2024","12feb2024","12feb2024","09feb2024","09feb2024","09feb2024","09feb2024","23feb2024","09feb2024"]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th>program_id<\/th>\n      <th>agency_name<\/th>\n      <th>program_name<\/th>\n      <th>type_proj<\/th>\n      <th>file_release<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"dom":"Blfrtip","buttons":["copy","print",{"extend":"collection","buttons":["csv","excel"],"text":"Download"}],"columnDefs":[{"name":"program_id","targets":0},{"name":"agency_name","targets":1},{"name":"program_name","targets":2},{"name":"type_proj","targets":3},{"name":"file_release","targets":4}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

## From FCC program csv

I just stacked them:

``` bash
csvstack data/programdata_program* > data/all_program.csv
```

Then remove their `elig_rules_desc` and `program_desc` so it can fit in
a table.

``` r
all_prog <- read.csv("data/all_program.csv")
list_of_names_to_keep <- c("agency_name" ,  "program_id",
                          "program_start_date","program_end_date",    "funding_source",   "funding_type",        "funding_obligated",  "funding_disbursed",   "funding_defaulted",   "min_download_spd",    "min_upload_spd",     "low_latency",      "funding_grant",       "program_cost",       "funding_loan",        "assistance_listings", "program_acronym",     "program_url"  
)
table_with_options(all_prog[, list_of_names_to_keep])
```

<div class="datatables html-widget html-fill-item" id="htmlwidget-dfe9199d1eb1cd19566a" style="width:100%;height:auto;"></div>
<script type="application/json" data-for="htmlwidget-dfe9199d1eb1cd19566a">{"x":{"filter":"none","vertical":false,"extensions":["Buttons"],"data":[["Rural Utilities Service","NTIA","Rural Utilities Service","US Department of Treasury","US Department of Treasury","Federal Communications Commission","Federal Communications Commission","Federal Communications Commission","NTIA","Federal Communications Commission","Federal Communications Commission","Rural Utilities Service"],[10,11,12,18,19,24,25,26,27,28,35,6],["2019-02-12","2020-12-27","2018-10-01","2022-01-25","2021-05-01","2020-10-29","2020-08-06","2020-08-06","2020-12-27","2018-07-24","2023-10-30","2019-04-23"],["","","","2026-12-31","","","","","","","2038-12-31",""],["Annual Appropriations","Consolidated Appropriations Act, 2021","Annual Appropriations","ARPA","ARPA","Federal Communications Commission","Federal Communications Commission","Federal Communications Commission","Consolidated Appropriations Act of 2021 and the Infrastructure Investment and Jobs Act ","Federal Communications Commission","Universal Service Funding","Consolidated Appropriations Act, 2018"],["G","G","L","G","G","G","G","G","G","G","G","C"],[97017262,272285261,413224000,0,0,6062445073,127095164,84456870,1648310368,1147865055,15831733248,3355855232],[0,29020927,0,0,0,673146254,23300780,15483760,17110286,459053516,0,0],[0,0,0,0,0,0,0,0,0,0,0,0],[25,25,25,100,100,25,100,1000,25,100,100,100],[3,3,3,100,100,3,20,500,3,20,20,100],["","t","","","","t","t","t","t","t","t",""],[97017262,272285261,null,null,null,null,null,null,null,null,15831733248,2702174137],[null,null,null,null,null,null,null,null,null,null,null,null],[null,null,413224000,null,null,null,null,null,null,null,0,653681095],[10.863,11.031,10.851,21.209,21.027,32.002,32.002,32.002,11.029,32.008,32.002,10.752],["Community Connect","BIP","Infrastructure","CPF","SLFRF","RDOF","","","TBCP NOFO 1","CAF-II","E-ACAM","ReConnect"],["https://www.rd.usda.gov/programs-services/telecommunications-programs/community-connect-grants","https://broadbandusa.ntia.doc.gov/broadband-infrastructure-program","https://www.rd.usda.gov/programs-services/telecommunications-programs/telecommunications-infrastructure-loans-loan-guarantees","","","https://www.fcc.gov/auction/904","https://www.fcc.gov/bringing-puerto-rico-together-and-connect-usvi-fund-stage-2","https://www.fcc.gov/bringing-puerto-rico-together-and-connect-usvi-fund-stage-2","","https://www.fcc.gov/auction/903","","https://www.usda.gov/reconnect"]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th>agency_name<\/th>\n      <th>program_id<\/th>\n      <th>program_start_date<\/th>\n      <th>program_end_date<\/th>\n      <th>funding_source<\/th>\n      <th>funding_type<\/th>\n      <th>funding_obligated<\/th>\n      <th>funding_disbursed<\/th>\n      <th>funding_defaulted<\/th>\n      <th>min_download_spd<\/th>\n      <th>min_upload_spd<\/th>\n      <th>low_latency<\/th>\n      <th>funding_grant<\/th>\n      <th>program_cost<\/th>\n      <th>funding_loan<\/th>\n      <th>assistance_listings<\/th>\n      <th>program_acronym<\/th>\n      <th>program_url<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"dom":"Blfrtip","buttons":["copy","print",{"extend":"collection","buttons":["csv","excel"],"text":"Download"}],"columnDefs":[{"className":"dt-right","targets":[1,6,7,8,9,10,12,14,15]},{"name":"agency_name","targets":0},{"name":"program_id","targets":1},{"name":"program_start_date","targets":2},{"name":"program_end_date","targets":3},{"name":"funding_source","targets":4},{"name":"funding_type","targets":5},{"name":"funding_obligated","targets":6},{"name":"funding_disbursed","targets":7},{"name":"funding_defaulted","targets":8},{"name":"min_download_spd","targets":9},{"name":"min_upload_spd","targets":10},{"name":"low_latency","targets":11},{"name":"funding_grant","targets":12},{"name":"program_cost","targets":13},{"name":"funding_loan","targets":14},{"name":"assistance_listings","targets":15},{"name":"program_acronym","targets":16},{"name":"program_url","targets":17}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

# Unserved / Unfunded

The data is available by State and recorded at the location level
(`location_id`) caracterized by their census block (`block_geoid`), H3
id (`h3_res8_id`) and a coninations of services described below.

The service can be “residential” (`r`) or “business” (`b`).

Each services is categorized as `U` for Unserved and unfunded or `C`
meaning the location is Covered (ie “availability data and or funding
exist at the selected technology/speed combination if that location”).

- *wired*: Copper, Cable, fiber

- *terrestrial*: Copper, Cable, Fiber, Unlicensed Fixed Wireless,
  Licensed Fixed Wireless, LBR Wireless

- *wiredlfw*: Copper, Cable, Fiber, Licensed Fixed Wireless, LBR
  Wireless

If a location is all ‘C’ it will not be in thoses files. In march 07 we
got 36 247 609 locations.

``` r
sum_unserved_unfunded <- read.csv("data/unfunded_unserved.csv")
temp <- as.data.frame(t(sum_unserved_unfunded))
dat <- cbind(temp,
            do.call(rbind, strsplit(row.names(temp), '_'))
)
names(dat) <- c("Count", "cnt", "technology", "dl", "ul", 'res/biz')
dat <- dat[, c("technology", "res/biz",  "dl", "ul", "Count")]

table_with_options(dat)
```

<div class="datatables html-widget html-fill-item" id="htmlwidget-ecc822a90f8c38d9e4af" style="width:100%;height:auto;"></div>
<script type="application/json" data-for="htmlwidget-ecc822a90f8c38d9e4af">{"x":{"filter":"none","vertical":false,"extensions":["Buttons"],"data":[["wired","wired","terrestrial","terrestrial","wiredlfw","wiredlfw","wired","wired","terrestrial","terrestrial","wiredlfw","wiredlfw"],["r","r","r","r","r","r","b","b","b","b","b","b"],["dl25","dl100","dl25","dl100","dl25","dl100","dl25","dl100","dl25","dl100","dl25","dl100"],["ul3","ul20","ul3","ul20","ul3","ul20","ul3","ul20","ul3","ul20","ul3","ul20"],[9294234,10691755,4673564,7512084,6139802,8581111,27592696,34310441,13246480,23736156,16472488,27229334]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th>technology<\/th>\n      <th>res/biz<\/th>\n      <th>dl<\/th>\n      <th>ul<\/th>\n      <th>Count<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"dom":"Blfrtip","buttons":["copy","print",{"extend":"collection","buttons":["csv","excel"],"text":"Download"}],"columnDefs":[{"className":"dt-right","targets":4},{"name":"technology","targets":0},{"name":"res/biz","targets":1},{"name":"dl","targets":2},{"name":"ul","targets":3},{"name":"Count","targets":4}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

Count of Unserved/Unfunded by type of services
