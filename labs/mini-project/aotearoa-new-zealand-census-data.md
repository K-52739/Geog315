#### Geog 315 T2 2020
# Aoteroa New Zealand Census data
The primary data source for social data is the census of population. You will find source data here at [datafinder.stats.govt.nz/data/category/census/](https://datafinder.stats.govt.nz/data/category/census/).

## Statistical Area 1 (SA1) datasets
The most generally useful data are likely to be one or more of the tables at SA1 level from the 2018 census, found at [this link](https://datafinder.stats.govt.nz/data/category/census/2018/sa1-dataset/sa1/).

## Lower or higher levels of detail
For lower levels of detail at Statistical Area 2 (SA2) level or Electorate or whatever, you should be able to aggregate up by spatially joining the SA1 data to boundaries data for the larger units.

For more detailed data, you may be out of luck. You can get meshblock boundaries (i.e. the polygons) level [here](https://datafinder.stats.govt.nz/layer/92197-meshblock-2018-generalised/) but that data to populate the meshblocks with are trickier to get hold of and due to small numbers many entries in meshblock data tables are suppressed.

# Data tidying
You will almost certainly need to tidy up and reduce any census dataset you download so that its coverage is better aligned with your area of interest, and you retain only attributes you are interested in. You will also want to do things like get rid of sea areas, and so on. You will certainly need time to get a handle on working with census data, and organising and cleaning them, so they can be sensibly included in your project.

#### So again: **don't leave downloading them until the last minute!**
