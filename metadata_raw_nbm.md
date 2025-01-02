---
title: FCC NBM CORI metadata and storage
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

## DB fields Descriptions

We are storing a transformed FCC’s NBM version for differents
geographyical units:

- Census blocks: `sch_broadband.bb_map_bl_2024june_data`
- Census tracts: `sch_broadband.bb_map_tr_2024june_data`
- Census counties: `sch_broadband.bb_map_co_2024june_data`
- Census places (Incomming)

Here we will describe fields used and value stored for each of them:

``` r
source("R/table_with_options.R")
metadata <- read.csv("data/metadata_fcc.csv")
table_with_options(metadata)
```

<div class="datatables html-widget html-fill-item" id="htmlwidget-ad3645af7c1f155d5d2e" style="width:100%;height:auto;"></div>
<script type="application/json" data-for="htmlwidget-ad3645af7c1f155d5d2e">{"x":{"filter":"none","vertical":false,"extensions":["Buttons"],"data":[["geoid_st","geoid_cty","geoid_tr","geoid_pl ","geoid_bl","cnt_total_locations","cnt_bcat_locations","cnt_25_3","cnt_100_20","cnt_100_100","cnt_fiber","pct_25_3","pct_100_20","bl_100_20_area","bl_25_3_area","category","pct_is_fiber","pct_is_underserved","pct_is_unserved","geom","distinct_frn","distinct_frn_cnt","tech_code","max_ad_down","max_ad_up","max_ad_down_gig_other","max_ad_up_gig_other","max_ad_down_gig_fiber","max_ad_up_gig_fiber","max_ad_down_all_fiber","max_ad_up_all_fiber","max_ad_down_cable","max_ad_up_cable","max_ad_down_adsl","max_ad_up_adsl","max_ad_down_fixed_wireless","max_ad_up_fixed_wireless","cnt_gig_other","cnt_gig_fiber","cnt_fiber_cable_access","cnt_dsl"],["char(2)","char(5)","char(11)","TBD","char(15)","count of every locations reported","count of every locations that are not:/low_latency = FALSE/max up/down = 0/technology 60-61-70","count of bcat locations that match 25/3 speeds","count of bcat locations that match 100/20 speeds","count of bcat locations that match 100/100 speeds","count of bcat locations with fiber","cnt_100_20/cnt_total_locations","cnt_25_3/cnt_total_locations","Temp logic to determine if block is not_reported/unserved_area/unserved_area/underserved_area/served_area","Temp logic to determine if block is not_reported/unserved_area/unserved_area/underserved_area/served_area","Determine if block is Not Reported/Unserved/Underserved/Served","cnt_fiber_locations / cnt_total_locations","1 - cnt_100_20 / cnt_total_locations","1 - cnt_25_3 / cnt_total_locations","geometry, MultiPolygon, 4269","PG array of distinct FRN","count of distinct FRN","PG array of disctint technology code","Max speed download","Max speed upload","Max speed download when 1k/1k and not fiber","Max speed upload  when 1k/1k and not fiber","Max speed download when 1k/1k and fiber","Max speed upload when 1k/1k and fiber","Max speed download when fiber","Max speed upload when fiber","Max speed download when cable","Max speed upload when cable","Max speed download when adsl","Max speed upload when adsl","Max speed download when wireless (not unlicensed)","Max speed upload when wireless (not unlicensed)","cnt of bcat when 1k/1k and not fiber","cnt of bcat when 1k/1k and fiber","cnt of bcat that is fiber or cable","cnt of bcat that is dsl"],["Yes","Yes","Yes","TBD","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes","Yes"],["Yes","Yes","Yes","TBD",null,"Yes","Yes","Yes","Yes","Yes","Yes","No","No","No","No","No","No","No","No","No","Yes","Yes","Yes","No","No","No","No","No","No","No","No","No","No","No","No","No","No","Yes","Yes","Yes","Yes"],["Yes","Yes","Yes","TBD",null,"Yes","Yes","Yes","Yes","Yes","Yes","No","No","No","No","No","No","No","No","No","Yes","Yes","Yes","No","No","No","No","No","No","No","No","No","No","No","No","No","No","Yes","Yes","Yes","Yes"],["Yes","Yes","Yes","Yes",null,"Yes","Yes","Yes","Yes","Yes","Yes","No","No","No","No","No","No","No","No","No","Yes","Yes","Yes","No","No","No","No","No","No","No","No","No","No","No","No","No","No","Yes","Yes","Yes","Yes"]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th>Fields<\/th>\n      <th>Description<\/th>\n      <th>Census.Block<\/th>\n      <th>Census.Tract<\/th>\n      <th>Census.Counties<\/th>\n      <th>Census.Places<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"dom":"Blfrtip","buttons":["copy","print",{"extend":"collection","buttons":["csv","excel"],"text":"Download"}],"columnDefs":[{"name":"Fields","targets":0},{"name":"Description","targets":1},{"name":"Census.Block","targets":2},{"name":"Census.Tract","targets":3},{"name":"Census.Counties","targets":4},{"name":"Census.Places","targets":5}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

### Tests on data:

- Eyeballed them

TODO:

- wrap into an R script/function (should return 0 row): check logic
  ideas

``` sql
select 
sign(cnt_total_locations - cnt_bcat_locations) as is_positive_dif_tot_bcat, 
sign(cnt_bcat_locations - cnt_fiber_locations) as is_positive_dif_bcat_fiber,
sign(cnt_100_20 - cnt_100_100) as is_positive_dif_20_100,
sign(cnt_25_3 - cnt_100_20) as is_positive_dif_25_100,
sign(cnt_fiber_cable - cnt_fiber_locations) as is_positive_dif_fiber_cable
from sch_broadband.bb_map_tr_2023dec_data
where 
    sign(cnt_total_locations - cnt_bcat_locations) = -1 or 
    sign(cnt_bcat_locations - cnt_fiber_locations) = -1 or 
    sign(cnt_100_20 - cnt_100_100) = -1 or
    sign(cnt_25_3 - cnt_100_20) = -1;
    
    
select 
sign(cnt_total_locations - cnt_bcat_locations) as is_positive_dif_tot_bcat, 
sign(cnt_bcat_locations - cnt_fiber_locations) as is_positive_dif_bcat_fiber,
sign(cnt_100_20 - cnt_100_100) as is_positive_dif_20_100,
sign(cnt_25_3 - cnt_100_20) as is_positive_dif_25_100,
sign(cnt_fiber_cable - cnt_fiber_locations) as is_positive_dif_fiber_cable
from sch_broadband.bb_map_co_2023dec_data
where 
    sign(cnt_total_locations - cnt_bcat_locations) = -1 or 
    sign(cnt_bcat_locations - cnt_fiber_locations) = -1 or 
    sign(cnt_100_20 - cnt_100_100) = -1 or
    sign(cnt_25_3 - cnt_100_20) = -1;
```

A quick check on 3 counties (also should be wrapped in R)

``` sql
select * from sch_broadband.bb_map_co_2023dec_data
where geoid_co = any(array['06023', '15009', '41045']) ;

select 
    min(state_abbr) as state_abbr,
    min(geoid_st) as geoid_st,
    min(geoid_co) as geoid_co,
    --
    sum(cnt_total_locations) as cnt_bcat_locations,
    sum(cnt_bcat_locations) as cnt_total_locations,
    sum(cnt_fiber_locations) as cnt_fiber_locations,
    sum(cnt_100_100) as cnt_100_100,
    sum(cnt_100_20) as cnt_100_20,
    sum(cnt_25_3) as cnt_25_3,
    sum(cnt_gig_other) as  cnt_gig_other,
    sum(cnt_gig_fiber) as  cnt_gig_fiber,
    sum(cnt_fiber_cable) as cnt_fiber_cable,
    sum(cnt_dsl)

from 
    sch_broadband.bb_map_tr_2023dec_data
where geoid_co = any(array['06023', '15009', '41045'])
group by geoid_co
 ;

 select 
    min(state_abbr) as state_abbr,
    min(geoid_st) as geoid_st,
    min(geoid_co) as geoid_co,
    --
    sum(cnt_total_locations) as cnt_bcat_locations,
    sum(cnt_bcat_locations) as cnt_total_locations,
    sum(cnt_fiber_locations) as cnt_fiber_locations,
    sum(cnt_100_100) as cnt_100_100,
    sum(cnt_100_20) as cnt_100_20,
    sum(cnt_25_3) as cnt_25_3,
    sum(cnt_gig_other) as  cnt_gig_other,
    sum(cnt_gig_fiber) as  cnt_gig_fiber,
    sum(cnt_fiber_cable) as cnt_fiber_cable,
    sum(cnt_dsl)

from 
    sch_broadband.bb_map_bl_2023dec_data
where geoid_co = any(array['06023', '15009', '41045'])
group by geoid_co
 ;
```

## S3 Archive:

We stored the raw data of FCC NBM we downloaded in a s3 bucket.

This is how the structure of the bucket look likes:

``` bash
.
├── D22
│   ├── 01july2023
│   ├── 09august2023
│   ├── 10october2023
│   └── old-api
├── D23
│   └── 14may2024
├── J22
│   ├── 03november2023
│   ├── 10may2024
│   └── old-api
└── J23
    └── 14november2023
```

In each directory we get a zip file per csv (technology/state).
