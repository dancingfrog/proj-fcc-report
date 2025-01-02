---
title: Testing other eligibilities
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
source("https://gist.githubusercontent.com/defuneste/32d5bffce8c6e88b6322ca4c9861793b/raw/893462fed28362601e63434ecc140bbdcbf6928b/us_states_df.R")
```

The goal of this page is to record the effect on not including some
technologies on the eligibility of BSL and census block.

## Excluding DSL service

``` sql
select
    geoid_st,
    sum(cnt_total_locations) as cnt_total_locations,
    sum(cnt_underserved) as cnt_underserved,
    sum(cnt_underserved_dsl_excluded) as cnt_loc_underserved_dsl_excluded,
    count(*) as cnt_blocks,
    sum(case when bl_100_20_area = 'underserved_area' then 1 else 0 end) as cnt_underserved_block,
    sum(case when bl_100_20_area_dsl_excluded = 'underserved_area' then 1 else 0 end) as cnt_underserved_block_dsl_excluded
from staging.bead_source_tiger_2020_blocks
group by geoid_st
order by geoid_st
```

This table will summarize some effects on locations and census blocks
categories.

Our previous definition:

- We are excluding satellites and unlicensed wireless

- If a location has only services lower than 25/3 it is unserved. If a
  location only has services with speed between 25/3 and 100/20 it is
  underserved. It will be served if it equal and above 100/20.

New definition:

- We are now excluding DSL (technology 10).

- Unserved are not changing (if they had DSL under 25/3 it was already
  unserved). Underseved and Served will change because some locations
  who were served will now move to underserved.

This was for locations. If we move to census block (where we apply this
“uncommun” 80/20) it will also change the category of eligibility
(because we are changing the classification of locations).

Table dicvtionnary:

- “United.States.of.America”: names of States

- “geoid_st: ANSI”number”

- “number_block”: Number of Census Block per states

- “cnt_underserved_block” : count of blocks underserved with the
  previous definition

- “cnt_underserved_block_dsl_excluded”: count of block if we exclude DSL

- “diff_block” : cnt_underserved_block_dsl_excluded -
  cnt_underserved_block

- “cnt_total_locations”: count of total locations per States

- “cnt_underserved”: count of locations underserved with previous
  definition

- “cnt_loc_underserved_dsl_excluded”: count of locations underserved if
  we removed DSL

I provided the locations to:

- do a bit of sanity check

- Working at the block level imply using the 80/20 rules and kind of
  assum all block are the same.

``` r
eligibiliy_st_ntia <- read.csv("data/dsl-exluded.csv", colClasses =c("geoid_st" = "character"))
US_slim <- US_states[,c("United.States.of.America", "ANSI_num", "ANSI_let")]
easy_table <- merge(eligibiliy_st_ntia, US_slim, 
                    by.x = "geoid_st", by.y = "ANSI_num",
                    all.x = TRUE, all.y = TRUE)

easy_table$diff_block <- easy_table$cnt_underserved_block_dsl_excluded - easy_table$cnt_underserved_block

easy_table <- easy_table[, c("United.States.of.America",
                            "geoid_st",
                            "number_block", 
                            "cnt_underserved_block",
                            "cnt_underserved_block_dsl_excluded",
                            "diff_block",
                            "cnt_total_locations",
                            "cnt_underserved",
                            "cnt_loc_underserved_dsl_excluded"
                            )]

table_with_options(easy_table)
```

<div class="datatables html-widget html-fill-item" id="htmlwidget-66513afec25de3782fc1" style="width:100%;height:auto;"></div>
<script type="application/json" data-for="htmlwidget-66513afec25de3782fc1">{"x":{"filter":"none","vertical":false,"extensions":["Buttons"],"data":[["Alabama","Alaska","Arizona","Arkansas","California","Colorado","Connecticut","Delaware","District of Columbia","Florida","Georgia","Hawaii","Idaho","Illinois","Indiana","Iowa","Kansas","Kentucky","Louisiana","Maine","Maryland","Massachusetts","Michigan","Minnesota","Mississippi","Missouri","Montana","Nebraska","Nevada","New Hampshire","New Jersey","New Mexico","New York","North Carolina","North Dakota","Ohio","Oklahoma","Oregon","Pennsylvania","Rhode Island","South Carolina","South Dakota","Tennessee","Texas","Utah","Vermont","Virginia","Washington","West Virginia","Wisconsin","Wyoming","American Samoa","Guam","Northern Mariana Islands","Puerto Rico","U.S. Virgin Islands"],["01","02","04","05","06","08","09","10","11","12","13","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30","31","32","33","34","35","36","37","38","39","40","41","42","44","45","46","47","48","49","50","51","53","54","55","56","60","66","69","72","78"],[185976,28568,155444,136422,519723,140345,49926,20198,6012,390066,232717,14732,81879,369978,204568,175199,172529,132662,142874,47138,83827,107278,254730,198705,112241,253632,88417,119103,57409,31948,137972,107215,288819,236638,84566,276428,180154,130807,336985,25649,146844,71383,179717,668757,71207,24611,163491,158093,72558,203059,53769,565,1816,873,41987,2657],[7199,1227,9494,5478,13461,10640,40,476,3,8622,7716,71,6752,15956,7882,11188,12375,4803,6156,3203,1433,164,7396,8203,4037,9359,6040,7234,1122,834,166,7340,1227,7831,383,6728,12655,5785,5598,26,3957,2393,3514,21624,1793,2745,4763,6233,2673,20289,2723,256,526,3,1198,0],[7818,1316,9779,6012,13852,10884,40,476,3,10613,10997,71,6902,16262,8183,13733,12598,6495,6264,3322,1433,165,7516,8493,4397,9359,6042,8199,1590,854,167,7882,1355,8391,384,6949,14708,6031,6785,26,4191,2544,3919,24515,1877,2858,4868,7059,2674,21178,2813,256,1097,507,1198,0],[619,89,285,534,391,244,0,0,0,1991,3281,0,150,306,301,2545,223,1692,108,119,0,1,120,290,360,0,2,965,468,20,1,542,128,560,1,221,2053,246,1187,0,234,151,405,2891,84,113,105,826,1,889,90,0,571,504,0,0],[2176002,281309,2696640,1359311,10224300,1968886,1091195,401523,123208,7426863,3796648,308967,738037,4166670,2713360,1372819,1204229,1851005,1875873,631930,1857192,1954671,4076669,2098318,1319976,2509891,488043,797960,983845,518391,2578933,860769,4715750,4273900,348661,4515985,1698280,1504496,4848216,339030,2153216,399171,2730981,9844791,1018752,285333,2920197,2540953,906872,2313423,262998,8794,36960,9760,1179988,32285],[78176,22844,109432,64638,147946,88608,1289,9207,5,122914,100898,837,63772,90364,58375,63830,47106,64020,72988,39578,19537,4140,87029,70500,40605,68915,47088,29865,11247,10903,2828,59995,13033,135892,1428,87118,104337,61723,53008,382,55820,12008,44527,233210,20438,30498,67255,76110,28803,198104,20940,7032,18792,12,32119,0],[83428,24209,111689,69544,152880,90980,1289,9207,5,141878,128679,837,65078,92960,60942,78835,48057,79849,74118,40549,19537,4143,88456,73373,43212,68915,47103,34256,15668,11072,2831,64039,14210,141758,1431,90055,118569,63143,59970,382,57932,13220,47047,253384,21132,31284,68204,83471,28807,203053,21439,7032,33513,8005,32119,0]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th>United.States.of.America<\/th>\n      <th>geoid_st<\/th>\n      <th>number_block<\/th>\n      <th>cnt_underserved_block<\/th>\n      <th>cnt_underserved_block_dsl_excluded<\/th>\n      <th>diff_block<\/th>\n      <th>cnt_total_locations<\/th>\n      <th>cnt_underserved<\/th>\n      <th>cnt_loc_underserved_dsl_excluded<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"dom":"Blfrtip","buttons":["copy","print",{"extend":"collection","buttons":["csv","excel"],"text":"Download"}],"columnDefs":[{"className":"dt-right","targets":[2,3,4,5,6,7,8]},{"name":"United.States.of.America","targets":0},{"name":"geoid_st","targets":1},{"name":"number_block","targets":2},{"name":"cnt_underserved_block","targets":3},{"name":"cnt_underserved_block_dsl_excluded","targets":4},{"name":"diff_block","targets":5},{"name":"cnt_total_locations","targets":6},{"name":"cnt_underserved","targets":7},{"name":"cnt_loc_underserved_dsl_excluded","targets":8}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

## Some tl:dr:

Not keeping DSL move 26907 blocks from being served to be underserved.

It has heterogneous effects: for some States it has nearly or very low
impacts but for other it is importants.
