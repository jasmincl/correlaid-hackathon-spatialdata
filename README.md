# correlaid-hackathon-spatialdata
Collection of data sources for the [CorrelAid Urban Planning X Spatial Data Hackathon with City Interaction Lab](https://www.eventbrite.com/e/spatial-data-for-urban-planning-joint-hackathon-with-city-interaction-lab-tickets-137419271741). 


## **Challenge 1: OSM + mobility: Anaylzing Urban Mobility Using Rental Bikeshare Data**

Origin-destination analysis is important for a deeper understanding of mobility and of many processes in the city.
However, just a pure origin-destination matrix analysis is not sufficient since it does not provide us enough information about the trip itself. By using additional information, which can be extracted from the openstreetmaps API we can detect and perform a more in-depth origin-destination matrix analysis.


This repo contains the following data sets:

### Trip and Station Data for Rental Bikeshare
Thanks to the [Deutsche Bahn Open Data Portal](https://data.deutschebahn.com/), we can access a [data set](https://data.deutschebahn.com/dataset/data-call-a-bike) of bikesharing trip start and end from 2014-2017 in several German cities, originating from the station-based bike rental "Call A Bike". 


For this hackathon an extract of the first two weeks of August 2016 in Hamburg is available ("bikeshare_trips_hh.csv"), containing trips and 208 stations in Hamburg. The data is already cleaned and contains only relevant variables:
  - *city_rental_zone*: name of the city
  - *trip_id*: unique identfier for each trip between two stations
  - *bike_id*: unique identifier for a rental bike
  - *customer_id*: unique identifier for a customer (no further information about users is provided)
  - *start_station_id*: unique identifier of station where bike was rented from
  - *end_station_id*: unique identifier of station where bike was returned (loop trips are possible)
  - *datetime_start*: start time and date of rental
  - *datetime_end*: end time and date of rental

Additionally, a data set of all 208 stations, their unique identifier, their name and latitude and longitude can be found in "bikeshare_stations_hh.csv".

More information about the original data set can be found [here (German only)](https://data.deutschebahn.com/dataset/data-call-a-bike).

## Getting OpenStreetMap Data

### R: [osmdata](https://cran.r-project.org/web/packages/osmdata/vignettes/osmdata.html)
osmdata is a useful R-package for simple osm-queries. Check out the code below for some examples (see the documentation linked above for more info).

```R
library(osmdata)
library(dplyr)
library(sf)
library(leaflet)
library(ggplot2)


# check available tags (sub categories)  inside keys ----------------------
available_features()
available_tags("amenity")
available_tags("shop")
available_tags("highway")

check <- readRDS("landusedf.rds")
glimpse(check)

# get all traffic lights in Hamburg --------------------------------------
## highway also includes bus stops, for example
query_traffic_signal <- getbb('Hamburg') %>%
  opq() %>% 
  add_osm_feature(key = 'highway', value = 'traffic_signals') %>% 
  osmdata_sf()

# extract points only
traffic_lights <- query_traffic_signal$osm_points

# plot
ggplot(landuse_sf) +
  geom_sf()


# get all cinemas in Hamburg ----------------------------------------------
query_cinema <- opq(bbox = 'Hamburg') %>%
  add_osm_feature(key = 'amenity', value = 'cinema') %>% 
  osmdata_sf()

# extract points only
cinemas <- query_cinemas$osm_points

# plot
ggplot(cinemas) +
  geom_sf()

# get all supermarkets in Hamburg ----------------------------------------
query_supermarket <- opq(bbox = 'Hamburg') %>%
  add_osm_feature(key = 'shop', value = 'supermarket') %>% 
  osmdata_sf()

# extract points only
supermarkets <- query_supermarket$osm_points

# plot
ggplot(supermarkets) +
  geom_sf()
```


### Landuse
The data for landuse in Hamburg is provided in the repo as a sf-dataframe. 


```R
# read in landuse for Hamburg
landuse_sf <- readRDS("data/landuse_sf.rds")

ggplot(landuse_sf) +
  geom_sf(aes(color=fclass))
```


### Python: [osmnx](https://osmnx.readthedocs.io/en/stable/)
