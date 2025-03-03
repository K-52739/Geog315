#### Geog 315 T2 2020
Make sure you have the libraries we need loaded
```{r}
library(sf)
library(tmap)
library(dplyr)
```

In what follows, I assume that you have loaded the datasets, and that they are called `abb` (for the Airbnb data) and `ca_tracts` (for the census tracts).
```{r}
abb <- st_read('la-abb.gpkg')
ca_tracts <- st_read('ca-tracts.gpkg')
```

# Map projections
As we might hope the `sf` package provides the ability to project spatial datasets, so that they can be properly aligned with one another for processing purposes, and so that reliable measurements of distances and areas in meaningful units (like metres) can be made.

We can't get into too many details in this class, since projections are a very large topic, but we can at least get a feel for how they work in this environment.

`sf` makes use of standard ways of specifying projections, namely the *EPSG codes* and/or *WKT* (well-known text) format infornmation. These are also used in *ArcGIS* (well hidden from users) and *QGIS* (not particularly well hidden!). In *R* they are right out in the open.

EPSG codes are numeric codes that correspond with specific projections. For example

+ **New Zealand Transverse Mercator** (NZTM) is 2193
+ **New Zealand Map Grid** (NZMG, which preceded NZTM) is 27200
+ Plain **latitude longitude** coordinates (which you might get from a GPS) are 4326
+ **Web Mercator** (the basis of most web maps until recently) is 3857

The codes are arbitrary, but geospatial software is aware of the codes and knows how to deal with them

You can see the EPSG information about a particular dataset by using `st_crs` (CRS stands for coordinate reference system)
```{r}
st_crs(abb)
```

This tells us that the `abb` dataset is in plain lat lon coordinates.
```{r}
st_crs(ca_tracts)
```

This tells us that these data are in some projected coordinate system althought the EPSG code has not been recorded.

Also shown in these examples is the *WKT* information for the projection. This information used to be presented as hard-to-interpret *proj* codes, but the WKT format is a more human-readable form, so you can tell that the `ca_tracts` dataset is in a Lambert Conformal Conic projection, and that the measurement units are feet (those crazy Americans!).

## Useful resources on EPSG codes and WKT codes
For more information on EPSG codes and WKT codes, you will find the resources below helpful:

+ [epsg.io](https://epsg.io) from MapTiler has details and information about EPSG codes
+ [Projection Wizard](https://projectionwizard.org) can suggest suitable projections conforming with various properties for specified areas of Earth's surface

## Different map projections
To see the problem we are up against, try mapping these two datasets on top of one another.

```{r}
tm_shape(ca_tracts) +
  tm_polygons() +
  tm_shape(abb) +
  tm_dots()
```

That worked fine. You should be able to make out the dots of the Airbnb listings in roughly the right spot in southern California. But imagine that we want to make our map only across the area covered by the Airbnb dataset. We might try this

```{r}
tm_shape(ca_tracts, bbox=st_bbox(abb)) +
  tm_polygons() +
  tm_shape(abb) +
  tm_dots()
```

Here, using the `bbox` option when I map the tracts layer, I have tried to restrict the map area to just the bounding box (i.e. the max/min x and y coordinate range) of the Airbnb data, but something seems to have gone wrong. That leads to the first question, which you should answer in your response to this lab:

### **Question 1 (IGNORE THIS QUESTION)**
#### ~Explain to the best of your ability what has gone wrong in the mapping that we just attempted. Look back to when you loaded the two datasets at the bounding box information provided when each dataset was loaded. (15%)~

## Changing the coordinate reference system by reprojecting
OK. So, this should make it clear that an important spatial data wrangling task is to get all the data you are working with into the same coordinate reference system (i.e. map projection), preferably one appropriate to the location of interest, and that uses sensible distance units, such as metres (and not loopy ones like feet).

**Note that if all we wanted to do was make a single map, reprojection wouldn't necessarily be required, we could do temporary workarounds, and get by. But for serious further work it makes sense to reproject all the datasets we are dealing with.**

Since we are in Los Angeles, I did a search on that place at `epsg.io` and it gave me a number of relevant options. I picked California zone 5, in a metric system which has EPSG code 2770. Reprojection of these data is simple using the `st_transform` function:

```{r}
abb <- st_transform(abb, 2770)
ca_tracts <- st_transform(ca_tracts, st_crs(abb))
```

You can check the projection information of the datasets again, just to make sure it all worked.

```{r}
st_crs(abb)
st_crs(ca_tracts)
```

### **Question 2**
#### Explain to the best of your ability what is happening when you use the command above,  `ca_tracts <- st_transform(ca_tracts, st_crs(abb))` with specific reference to how we specify the projection to be used in the transformation, that is the `st_crs(abb)` part. (12%)

## That map again
To check this has fixed the problem, make that map again:

```{r}
tm_shape(ca_tracts, bbox=st_bbox(abb)) +
  tm_polygons() +
  tm_shape(abb) +
  tm_dots()
```

Nice!

OK, so we don't lose all this good work, we should save the now reprojected datasets. We will use geopackage files since these reliably record projection information in a single file format (unlike shapefiles). We will also include some information in the file name about the projection, just so we can keep track of things

```{r}
st_write(abb, 'la-abb-p2770.gpkg', delete_layer=TRUE)
st_write(ca_tracts, 'ca-tracts-p2770.gpkg', delete_layer=TRUE)
```

If you need to reload the data at any point you should use these new files and not the originals. The new projected coordinates are only made permanent when we write the data out to the file system like this.

Now [go back to the overview](README.md) or on to [the next page of instructions](spatial-data-manipulation-03-spatial-joins.md).
