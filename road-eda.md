---
title: TIGER Roads
date: last-modified
draft: false
format:
  gfm:
    variant: +yaml_metadata_block
prefer-html: true
---


Requirement:

> Roughly estimate route miles of potential deployments

## what is inside the data:

We have linestrings with those attributes:

- `linearid`: primary id, used by ither TIGER product

- `fullname`: road name, humane-readable

- `rttyp`: route type code, type of road.

| Route Type Code | Route Type Code Description |
|-----------------|-----------------------------|
| C               | County                      |
| I               | Interstate                  |
| M               | Common Name                 |
| O               | Other                       |
| S               | State recognized            |
| U               | U.S.                        |

- `mtfcc`: MAF/TIGER Feature Class Codes, example, S1400 (Local
  Neighborhood Road, Rural Road, City Street)

### Classes of Roads according to TIGER/line census

<div class="column-margin">

TIGER: Topologically Integrated Geographic Encoding and Referencing

</div>

| mtfcc | Features |
|----|----|
| “S1100” | Primary Road |
| “S1200” | Secondary Road |
| “S1400” | Local Neighborhood Road, Rural Road, City Street |
| “S1500” | Vehicular Trail (4WD) |
| “S1630” | Ramp |
| “S1640” | Service Drive usually along a limited access highway |
| “S1710” | Walkway/Pedestrian Trail |
| “S1720” | Stairway |
| “S1730” | Alley |
| “S1740” | Private Road for service vehicles (logging, oil fields, ranches, etc.) |
| “S1750” | Internal U.S. Census Bureau use |
| “S1780” | Parking Lot Road |
| “S1810” | Winter Trail |
| “S1820” | Bike Path or Trail |
| “S1830” | Bridle Path |

## Should this information provided and how?

We should probably not keep the bridle paths?

Should it be provided at block level: ie an estimate route miles?

How the user will interact with it?

### Resources

- https://www2.census.gov/geo/pdfs/reference/mtfccs2020.pdf

- https://www.census.gov/library/reference/code-lists/route-type-codes.html
