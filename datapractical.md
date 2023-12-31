Do Crows Prefer the City Life or the Peaceful Countryside?
================
Andrea Cruz Alegria
(02 January 2024)

- [Introduction](#introduction)
  - [Packages Required](#packages-required)
  - [Research Question](#research-question)
  - [Hypothesis](#hypothesis)
- [Importation of Data](#importation-of-data)
  - [Importation of Data from GBIF](#importation-of-data-from-gbif)
  - [Importation from iNaturalist](#importation-from-inaturalist)
  - [Plots of GBIF and iNaturalist](#plots-of-gbif-and-inaturalist)
- [Elevation Data](#elevation-data)
  - [Elevation Plots](#elevation-plots)
- [Temporal Data](#temporal-data)
  - [Temporal Plots](#temporal-plots)
- [Land Cover](#land-cover)
  - [Land Cover Plots](#land-cover-plots)
- [Other plots](#other-plots)
- [Statistics](#statistics)
- [Conclusion](#conclusion)
- [References](#references)

# Introduction

To me, crows are one of the most fascinating animals to exist. I am a
biologist, so I think that about a lot of animals, but I think crows are
often the most misunderstood creatures. Through history they have been
seen as a bad omen, to the point where a group of crows is called a
“murder”. This has led to some people not taking a liking to crows,
affecting their behavior towards crows, you would think that maybe crows
would have been driven away by humans. Nevertheless, crows are quite
common wherever you go, they are in the woods and also in populated
areas.

How much does human disturbance impact the population of crows? In this
project, we will be looking at the population density in both rural and
urban areas to determine which habitat the crows prefer.

![](https://images.unsplash.com/photo-1580420045808-0a7deb24fea7?q=80&w=1933&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D)

## Packages Required

Here are the packages that will be needed throughout the project

``` r
library(rgbif)
library(rnaturalearth)
library(tidyverse)
library(rinat)
library(rnaturalearthdata)
library(raster)
library(viridis)
library(sf)
library(sp)
library(MODIStsp)
library(terra)
library(geodata)
library(xts)
library(rgeoboundaries)
library(rayshader)
library(rgdal)
library(elevatr)
library(remotes)
library(rasterVis)
library(dplyr)
library(ggfortify)
library(devtools)
library(tibble)
library(lubridate)
```

## Research Question

Do crows prefer the city life or the peaceful countryside? This is the
research question we will be investigating.

## Hypothesis

I hypothesize that there would be a higher crow population density in
the rural areas compared to urban areas.

# Importation of Data

## Importation of Data from GBIF

To import data first I will first specify the animal I am interested in,
which will be *Corvus corone*. I chose this instead of focusing on all
corvids because that would include magpies, rooks, etc., and I just want
to focus on the common crow.

``` r
myspecies <- c("Corvus corone")
```

Now that I’ve specified the species, I will be using
[GBIF](https://www.gbif.org/) as a source to import all *Corvus corone*
data (GBIF.org, n.d.).

``` r
gbif_data <- occ_data(scientificName = myspecies, hasCoordinate = TRUE, limit = 1000, )
occur <- gbif_data$data #naming occurrence data 
```

However, I don’t want to see all the crows in the world. I want to
specifically see those in Switzerland. Thus, I will filter the data to
show *Corvus corone* occurrences in Switzerland.

``` r
unique(occur$country) #Seeing if Switzerland is indeed listed
```

    ##  [1] "United Kingdom of Great Britain and Northern Ireland"
    ##  [2] "France"                                              
    ##  [3] "Netherlands"                                         
    ##  [4] "Germany"                                             
    ##  [5] "Spain"                                               
    ##  [6] "Kazakhstan"                                          
    ##  [7] "Austria"                                             
    ##  [8] "Ireland"                                             
    ##  [9] "Japan"                                               
    ## [10] "Denmark"                                             
    ## [11] "Italy"                                               
    ## [12] "Russian Federation"                                  
    ## [13] "Portugal"                                            
    ## [14] "Luxembourg"                                          
    ## [15] "Belgium"                                             
    ## [16] "Switzerland"                                         
    ## [17] "Kyrgyzstan"                                          
    ## [18] "Korea, Republic of"                                  
    ## [19] "Monaco"

``` r
gbif_swiss <- occur[occur$country == "Switzerland",]  
(gbif_swiss) #Trying to see first lines of the data
```

    ## # A tibble: 13 × 109
    ##    key        scientificName  decimalLatitude decimalLongitude issues datasetKey
    ##    <chr>      <chr>                     <dbl>            <dbl> <chr>  <chr>     
    ##  1 4014992188 Corvus corone …            47.5             9.17 cdc,c… 50c9509d-…
    ##  2 4015349316 Corvus corone …            46.2             6.15 cdc,c… 50c9509d-…
    ##  3 4039130790 Corvus corone …            46.6             9.60 cdc,c… 50c9509d-…
    ##  4 4021988739 Corvus corone …            47.0             8.32 cdc,c… 50c9509d-…
    ##  5 4028922448 Corvus corone …            47.4             8.42 cdc,c… 50c9509d-…
    ##  6 4029014942 Corvus corone …            47.5             7.62 cdc,c… 50c9509d-…
    ##  7 4028921438 Corvus corone …            47.5             7.61 cdc,c… 50c9509d-…
    ##  8 4028643267 Corvus corone …            47.2             8.68 cdc,c… 50c9509d-…
    ##  9 4046514961 Corvus corone …            46.2             8.84 cdc,c… 50c9509d-…
    ## 10 4076313600 Corvus corone …            46.9             9.01 cdc    50c9509d-…
    ## 11 4080652584 Corvus corone …            46.2             8.95 cdc,c… 50c9509d-…
    ## 12 4091583138 Corvus corone …            47.3             8.71 cdc,c… 50c9509d-…
    ## 13 4145434846 Corvus corone …            47.4             8.55 cdc,c… 50c9509d-…
    ## # ℹ 103 more variables: publishingOrgKey <chr>, installationKey <chr>,
    ## #   hostingOrganizationKey <chr>, publishingCountry <chr>, protocol <chr>,
    ## #   lastCrawled <chr>, lastParsed <chr>, crawlId <int>, basisOfRecord <chr>,
    ## #   occurrenceStatus <chr>, taxonKey <int>, kingdomKey <int>, phylumKey <int>,
    ## #   classKey <int>, orderKey <int>, familyKey <int>, genusKey <int>,
    ## #   speciesKey <int>, acceptedTaxonKey <int>, acceptedScientificName <chr>,
    ## #   kingdom <chr>, phylum <chr>, order <chr>, family <chr>, genus <chr>, …

``` r
### Renaming Columns of the data 
species <- gbif_swiss$scientificName
Latitude <- gbif_swiss$decimalLatitude
Longitude <- gbif_swiss$decimalLongitude
Date <- gbif_swiss$eventDate

### Creating new data frame with columns of interest 
matrix_gbif <- data.frame(species,     
                          Latitude, 
                          Longitude, 
                          Date)
```

Now we can plot some of this data using the
[ggplot](https://ggplot2.tidyverse.org/) function (Wickham, 2016).

``` r
## Plots of GBIF
Switzerland <- ne_countries(scale = "medium", returnclass = "sf", country = "Switzerland")


ggplot(data=Switzerland) + 
  geom_sf()  + 
  geom_point(data=matrix_gbif, aes(x = Longitude, y = Latitude), size = 4, shape = 23, fill = "darkgreen") + 
  theme_classic() + 
  labs(title=expression(paste("Occurrence of ",  italic("Corvus corone "), "in Switzerland"))) + 
  xlab("Longitude") + ylab("Latitude")
```

![](datapractical_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

## Importation from iNaturalist

Because the information that I obtained from GBIF is scarce, I will be
importing more data from another source called
[iNaturalist](https://www.inaturalist.org/), which is a site used by
both professionals and amateurs to record their observations.

``` r
### Adding a source column
matrix_gbif$source <- rep("gbif", nrow(matrix_gbif))

### importing data from iNaturalist
inat_swiss <- get_inat_obs(query = "Corvus corone", place_id = "switzerland")

### Renaming Columns of the data 

species_n <- inat_swiss$scientific_name
Latitude_n <- inat_swiss$latitude
Longitude_n <- inat_swiss$longitude
Date_n <- inat_swiss$datetime

### Creating new data frame with columns of interest 
matrix_inat <- data.frame(species_n,     
                          Latitude_n, 
                          Longitude_n, 
                          Date_n)

### Renaming the columns in order to merge them together 
colnames(matrix_inat) <- c("species", 
                           "Latitude", 
                           "Longitude", 
                           "Date")

### Adding inaturalist as a source
matrix_inat$source <- rep("inat", nrow(matrix_inat))
```

Now, that we’ve imported the data and cleaned it up a bit, we can merge
the two sources into one data frame.

``` r
## Merging the two sources of data together GBIF + iNat
full_matrix <- rbind(matrix_inat, matrix_gbif)
nrow(full_matrix)
```

    ## [1] 113

Now, we are able to create a graph showing the observations of Corvus
corone from both sources. We now have a total of 113 observations, which
we will work with throughout this paper.

## Plots of GBIF and iNaturalist

``` r
ggplot(data = Switzerland) +
  geom_sf()   +
  geom_point(data = full_matrix, aes(x = Longitude, y = Latitude, fill=source), size = 4, 
             shape = 23) + theme_classic() +
  labs(title=expression(paste("Occurrence of Crows in Switzerland"))) + xlab("Longitude") + ylab("Latitude")
```

![](datapractical_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

This plot allows us to see the crows in relation to Switzerland. We can
see that there are quite a lot of observations in Ticino. Apart from
that cluster it seems they are all spread out throughout Switzerland,
with no observations in Valais.

A further look at the data shows that there is not only *Corvus corone*
but also *Corvus cornix* however, I will not exclude these points
because their ability to produce hybrids that are fertile would mean
that the new species *Corvus corone* x *cornix* is equivalent to the old
species. If they were not able to produce fertile hybrids, such as those
between lions and tigers, it would mean they are different species. So,
I will take these as the same species (Turner, 2002)

# Elevation Data

In this section, we will be creating an Elevation map, to see the
occurrences of *Corvus corone* at different elevations. In order to do
this, we must first extract the elevation data of Switzerland.

``` r
#Obtaining elevation data 
elevation_data <- elevatr::get_elev_raster(locations = Switzerland, z = 6, clip = "locations")
points <- SpatialPoints(data.frame(full_matrix$Longitude,full_matrix$Latitude))   
```

## Elevation Plots

Now, we are able to plot a simple map showing the different points where
*Corvus corone* are spotted.

``` r
#Plots of elevation data 
plot(elevation_data, main=expression(paste("Elevation Points ",  italic("Corvus corone "))), legend.args = list(text="Elevation (m)", side =2, font =2))
plot(points,add=T)
```

![](datapractical_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
#Extracting the elevation values for each point 
elevation_points <- raster::extract(elevation_data, points, method='bilinear')

#Adding Elevation Column 
full_matrix$Elevation <- elevation_points
```

``` r
#Checking the data 

mean(full_matrix$Elevation)
```

    ## [1] NA

From this elevation plot, we can see that crows generally tend to be
observed below 1000m, with the highest elevation being 1444m. This means
that if they do like rural areas more than urban areas, they do not
prefer to be in high-altitude mountains. We can also check the mean
elevation, and this is 300m.

# Temporal Data

Next, we will have a look at what hours *Corvus corone* are being
spotted, and during what months they occur the most. To do this, we must
first add the date and the time to our matrix, and we shall begin with
hour occurrence.

``` r
full_matrix$Date <- ymd_hms(full_matrix$Date)

#Removal of outliers
full_matrix <- subset(full_matrix, full_matrix$Date >= as.Date("2000-01-01"))


#Creation variable with format of date and hour
x_time <- format(full_matrix$Date, format = "%H:%M:%S")   
x_date <- format(full_matrix$Date, format = "%d-%m-%y")  

#Addition of a date column
full_matrix <- add_column(full_matrix, x_date = as.Date(x_date,format = "%d-%m-%y"), .after = 5)

#Addition of a time column
full_matrix <- add_column(full_matrix, Hour = hour(full_matrix$Date), .after = 6)
```

## Temporal Plots

Now that we have added the hour and date to the matrix we can create
some plots.

``` r
#Plot of corvus corone occurrences at different times
ggplot(subset(full_matrix, Hour != 0), aes(x=Hour, colour = source)) +
  geom_histogram(binwidth=1, alpha=1) +
  labs(x = "Hour", y = "Occurrence", title=expression(paste("Occurrence of ", italic("Corvus corone "), "at Different Times ")))+
  scale_x_continuous(breaks = seq(0, 23, by = 1))
```

![](datapractical_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

This plot shows us that most observations occur in the morning at 7:00
a.m. and very few observations after 3:00 p.m., however this is quite
biased as it relies on human observations. So, it is not a true
reflection of when crows are around.

``` r
#Addition of columns of each variable 
full_matrix <- add_column(full_matrix, Month = month(full_matrix$Date), .after = 6)
full_matrix <- add_column(full_matrix, Day = day(full_matrix$Date), .after = 7)
full_matrix <- add_column(full_matrix, Year = year(full_matrix$Date), .after = 8)


#Creating a column as a factor to add the labels for each month
full_matrix <- add_column(full_matrix, Month_names = as.factor(cut(full_matrix$Month, 
                                                                   breaks = 12, 
                                                                   labels = c("Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"))), .after = 7)

# Plotting temporal occurrence of corvus corone over different months
ggplot(full_matrix, aes(x = Month, fill = Month_names)) +
  geom_histogram(bins = 12) +
  scale_x_continuous(breaks = seq(1, 12, by = 1), labels = month.abb) +
  scale_fill_discrete(guide = "none") +
  theme_bw() +
  labs(x = "Month", y = "Frequency", title = expression(paste("Temporal Occurrence of ", italic("Corvus corone "), "in Switzerland "))) +
  theme(strip.text.x = element_text(face = "bold", size = 12))
```

![](datapractical_files/figure-gfm/unnamed-chunk-14-1.png)<!-- --> This
graph shows us that most observations occur during September-January,
and very little between February-May. Most of the observations come from
November. This might reflect the breeding times which occur from early
April-June, or that perhaps people do not go out to observe during the
months of February-May.

``` r
# Plotting the temporal occurrence of Corvus corone at different altitudes
ggplot(full_matrix, aes(x= Elevation, color = Month_names)) +
  geom_boxplot(alpha = 0.5, linewidth=1) +
  scale_fill_discrete(name = "Month", guide = "legend") +
  theme_bw() +
  theme(plot.title = element_text(size = 12))+
  labs(x = "Elevation", y = "Density", title = expression(paste("Temporal Occurrence of ", italic("Corvus corone "), "in Switzerland ", "at Different Elevations")))
```

![](datapractical_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

We can also combine the temporal occurrence and the different
elevations. During January they seem to be at higher altitudes than the
rest of the months. Other than that, the rest of the year they seem to
prefer to be at around 250m.

# Land Cover

We can now start to look at land cover (LC) data to see if crows prefer
urban or rural areas more.

To do this we can create a spatial file path to save the geodata.
Afterwards, we can use
[MODIS](https://modis.gsfc.nasa.gov/data/dataprod/mod12.php) to download
the geodata, specifically land cover data MCD12Q1 (Friedl &
Sulla-Menashe, 2019).

The following code is not needed if you have the folder with the images:

``` r
#defining file path to save downloaded spatial file
# spatial_filepath <- "C:/Users/cruze/OneDrive/RCrow/switzerland_map.shp"
# 
# 
# #Saves spatial_filepath on our computer, but check the path to see if it worked!
# st_write(map_boundary, paste0(spatial_filepath))
# 
# 
# #M*D12Q1 is the code for landcover
# MODIStsp_get_prodlayers("M*D12Q1")
# 
# ### Downloading Landcover data
# MODIStsp(
#   gui = FALSE,
#   out_folder = "C:/Users/cruze/OneDrive/RCrow",
#   out_folder_mod = "C:/Users/cruze/OneDrive/RCrow",
#   selprod = "LandCover_Type_Yearly_500m (MCD12Q1)",
#   bandsel = "LC1",
#   user = "petite.annie14",
#   password = "mqa-qxr6nhp*JWB1zjv",
#   start_date = "2022.01.01",
#   end_date = "2022.12.31",
#   verbose = FALSE,
#   spatmeth = "file",
#   spafile = spatial_filepath,
#   out_format = "GTiff"
# )
```

This code is somewhat problematic but in the essence of reproducibility,
I decided to keep it in the document to show where I get the data from.
However, depending on the computer, this code may or may not work.
Nevertheless, you can continue to the next code chunks with the files
made available in [Github](https://github.com/acruzale/datapractical).

Now we can extract the land cover data and add it to our data frame.

``` r
#Reading specific land cover data
LC_raster <- raster(here::here("C:/Users/cruze/OneDrive/RCrow/switzerland_map/LandCover_Type_Yearly_500m_v61/LC1/MCD12Q1_LC1_2022_001.tif"))

#Transformation of data, ensuring that coordinates match the rest of the data
LC_raster <- projectRaster(LC_raster, crs = "+proj=longlat +ellps=WGS84 +datum=WGS84 +no_defs")

#Limiting the data to Switzerland
LC_raster <- raster::mask(LC_raster, as_Spatial(Switzerland ))

#Adding coordinates to the Land cover map
LC <- raster::extract(LC_raster, points, method='simple')
matrix_LC <- as.data.frame(LC_raster, xy = TRUE, na.rm = TRUE) 

# values rounded to the nearest whole number
full_matrix$LC <- round(LC)

# Create character and numeric values (LC and LC_names)
full_matrix$LC_names <- full_matrix$LC 
```

Now that we have the numbers of the land cover, we can add the
corresponding names using the [MCD12 User
Guide](https://lpdaac.usgs.gov/documents/101/MCD12_User_Guide_V6.pdf)
using Table 9. For simplicity, I put the forests, herbaceous, croplands,
and shrub lands all under “Vegetated”.

``` r
### Replace name only in the line LC (in which vector we want to replace the number) [filter]

full_matrix$LC_names[(full_matrix$LC > 0 & full_matrix$LC <= 1)] <- "Barren"
full_matrix$LC_names[(full_matrix$LC > 1 & full_matrix$LC <= 2)] <- "Permanent Snow and Ice"
full_matrix$LC_names[(full_matrix$LC > 2 & full_matrix$LC <= 3)] <- "Water"
full_matrix$LC_names[(full_matrix$LC > 3 & full_matrix$LC <= 9)] <- "Urban"
full_matrix$LC_names[(full_matrix$LC > 9 & full_matrix$LC <= 40)] <- "Vegetated"
full_matrix$LC_names[full_matrix$LC > 40] <- "Unclassified"


custom_colors <- c(
  "Barren" = "orange",
  "Permanent Snow and Ice" = "lightblue",
  "Water" = "blue",
  "Urban" = "red",
  "Vegetated" = "purple",
  "Unclassified" = "grey"
)

full_matrix$LC_colors <- custom_colors[full_matrix$LC_names]
```

## Land Cover Plots

Now that we’ve colored the land cover types, we can make plots for the
land cover to see where crows are observed the most.

``` r
opar <- par(no.readonly = TRUE)
par(mar = c(6, 5, 4, 6.5))

plot(LC_raster, legend=FALSE, main=expression(paste("Occurrence of ",  italic("Corvus corone "), "in Relation to Land Cover")))
points(points, pch = 20, col = full_matrix$LC_colors)
legend(x = "topright",
       inset = c(-0.5, 0), # You will need to fine-tune the first
       # value depending on the windows size
       legend = names(custom_colors),
       fill = custom_colors, 
       xpd = TRUE) # Needed to put the legend outside the plot
```

![](datapractical_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->

``` r
# Back to the default graphical parameters
on.exit(par(opar))

table(full_matrix$LC_names)
```

    ## 
    ##     Urban Vegetated 
    ##        27        85

This graph shows us that observations occur in vegetated and urban
areas, and no other areas. However again this is because these
observations depend on where humans tread. Visually however, it seems
that there are more observations in vegetated areas than in rural areas,
even in that big cluster in Ticino.

We can check the data to see if it correlates to what we can see
visually. Indeed it seems that there are 21 urban observations and 92
vegetated (rural) observations. This shows that with the given data set,
crows do prefer rural areas over urban areas.

# Other plots

We have the essential plots, but I would like to dig deeper and find out
more. This plot shows us the temporal occurrence by land cover. This
shows us that most of the observations of crows occur in the morning and
the majority in vegetated areas.

``` r
# Filter for Vegetated and Urban land covers
landcover_subset <- c("Vegetated", "Urban")
subset_matrix <- full_matrix[full_matrix$LC_names %in% landcover_subset, ]

# Plot temporal occurrence for Vegetated and Urban
ggplot(subset_matrix, aes(x = Hour, fill = LC_names)) +
  geom_histogram(binwidth = 1, position = "identity", alpha = 0.7) +
  labs(x = "Hour", y = "Occurrence", title=expression(paste("Temporal Occurrence of",  italic("Corvus corone "), "in Switzerland by Land Cover"))) +
  scale_fill_manual(values = custom_colors) +
  theme_minimal()
```

![](datapractical_files/figure-gfm/unnamed-chunk-20-1.png)<!-- -->

Furthermore, there is more density of crows in vegetated areas, and also
seen throughout the year. In urban areas, they have been observed on
January, July, November, and December.

``` r
ggplot(full_matrix, aes(x= LC_names, color = Month_names)) +
  geom_boxplot(alpha = 0.5, linewidth=1) +
  scale_fill_discrete(name = "Month", guide = "legend") +
  theme_bw() +
  theme(plot.title = element_text(size = 12))+
  labs(x = "Elevation", y = "Density", title = expression(paste("Temporal Occurrence of ", italic("Corvus corone "), "in Switzerland ", "at Different Elevations")))
```

![](datapractical_files/figure-gfm/unnamed-chunk-21-1.png)<!-- -->

This graphs allows us to see that indeed, there is more density of crows
in vegetated areas than in urban areas.

``` r
ggplot(full_matrix, aes(x=LC, fill=LC_names)) + 
    geom_histogram(binwidth=0.6, alpha=0.5) +
    labs(title=expression(paste("Density of ", italic("Corvus corone "), "by Land Cover"))) + xlab("Land Cover Types") + ylab("Density")
```

![](datapractical_files/figure-gfm/unnamed-chunk-22-1.png)<!-- -->

From this graph we can see that during January and November, the crows
are seen at higher altitudes in mostly vegetated areas. In October and
September, we can see more crows are present in urban areas at these
times.

``` r
ggplot(full_matrix, aes(x = Month, y = Elevation, fill = Month_names)) +
    geom_point(aes(colour = LC_names, alpha=1/20)) +
    scale_x_continuous(breaks = seq(1, 12, by = 1), labels = month.abb) +
    scale_size_continuous(breaks = c(500, 1000, 1500, 2000)) +
    scale_fill_discrete(guide = "none") +
    scale_alpha(guide = "none") +
    labs(title=expression(paste("Activity of ", italic("Corvus corone "), "During the Year at Different Altitudes"))) + xlab("Months") + ylab("Elevation")+
    theme_bw()
```

![](datapractical_files/figure-gfm/unnamed-chunk-23-1.png)<!-- -->

# Statistics

Now we can look at some statistics. We will determine whether the choice
of land cover is related to season/month, hour, or elevation.

``` r
# Pearson Correlation
  cor.test(full_matrix$LC,full_matrix$Month)
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  full_matrix$LC and full_matrix$Month
    ## t = -0.82246, df = 110, p-value = 0.4126
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  -0.2599629  0.1089581
    ## sample estimates:
    ##         cor 
    ## -0.07817824

``` r
# Does LC choice depend on season? 
mod1 <- lm(LC~Month,data = full_matrix)
anova(mod1)
```

    ## Analysis of Variance Table
    ## 
    ## Response: LC
    ##            Df Sum Sq Mean Sq F value Pr(>F)
    ## Month       1   3.26  3.2635  0.6764 0.4126
    ## Residuals 110 530.70  4.8246

The statistical tests show there is no significant relationship between
the season and land choice.

Is there perhaps a relationship between the moment in the day and the
choice of land?

``` r
# Pearson correlation
cor.test(full_matrix$LC,full_matrix$Hour) 
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  full_matrix$LC and full_matrix$Hour
    ## t = 0.45195, df = 110, p-value = 0.6522
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  -0.1436513  0.2267960
    ## sample estimates:
    ##        cor 
    ## 0.04305201

``` r
# Does LC choice depend on the time of day? 
mod2 <- lm(LC~Hour,data = full_matrix)
anova(mod2)
```

    ## Analysis of Variance Table
    ## 
    ## Response: LC
    ##            Df Sum Sq Mean Sq F value Pr(>F)
    ## Hour        1   0.99  0.9897  0.2043 0.6522
    ## Residuals 110 532.97  4.8452

There is also no significant relationship between the hour and land
choice.

``` r
# Pearson correlation
cor.test(full_matrix$LC,full_matrix$Elevation) 
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  full_matrix$LC and full_matrix$Elevation
    ## t = -2.8849, df = 110, p-value = 0.004712
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  -0.42962790 -0.08378239
    ## sample estimates:
    ##        cor 
    ## -0.2652151

``` r
# Does LC choice depend on the elevation? 
mod3 <- lm(LC~Elevation,data = full_matrix)
anova(mod3)
```

    ## Analysis of Variance Table
    ## 
    ## Response: LC
    ##            Df Sum Sq Mean Sq F value   Pr(>F)   
    ## Elevation   1  37.56  37.559  8.3227 0.004712 **
    ## Residuals 110 496.41   4.513                    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

There is also no significant relationship between the land choice and
the elevation.

# Conclusion

From the graphs that we have created we can see that:

- Crows tend to be more active during early morning from 6:30-8:30 a.m.
- They tend to prefer lower elevations ranging from 200-300m, and rarely
  are they observed at higher altitudes than 400m.
  - For reference, the Neuchâtel train station is at around 400m.

My research question was looking into whether crows preferred urban or
rural areas more. The graphs show that the observations made exclude all
land cover types except vegetated and urban. However, we have to take
into account that there’s probably no observations being made in
permanent snow and ice, barren or in water, simply because many people
do not go there, and if they do and see a crow, they might not register
it on either GBIF or iNaturalist. In addition, this data set relies on
amateur observations. This means that people might register their
observation as a crow but it might have been a raven, or a rook. The
best way to exclude erroneous data was to filter research grade
observations. However due to low amount of observations I decided to
keep them all and not filter for that.

The plots in the section “Other plots” further confirm that crows are
mostly observed in vegetated areas rather than urban areas. There is
also higher occurrence and density in vegetated areas. This confirms my
hypothesis. Furthermore, there was no correlation or statistical
significance for the season, time of day, or elevation as a reason for
their choice of land cover.

However, I must stress that this data might be biased due to it being
taken from observations that humans have noted down. This means that the
conclusions here should be taken lightly. It might be that there are
more crows in urban areas, and people do not note it down on GBIF or
iNaturalist, simply because it is common to see crows in those areas. In
addition, the number of observations may be too few to fully conclude
anything.

# References

1.  Friedl, M., & Sulla-Menashe, D. (2019). MCD12Q1 MODIS/Terra+Aqua
    Land Cover Type Yearly L3 Global 500m SIN Grid V006 \[data set\].
    NASA EOSDIS Land Processes Distributed Active Archive Center.
    <https://doi.org/10.5067/MODIS/MCD12Q1.006>
2.  GBIF.org. (n.d.). GBIF Occurence Download \[dataset\].
    <https://doi.org/10.15468/dl.0eksga>
3.  Turner, G. F. (2002). Parallel speciation, despeciation and
    respeciation: Implications for species definition. Fish and
    Fisheries, 3(3), 225–229.
    <https://doi.org/10.1046/j.1467-2979.2002.00085.x>
4.  Wickham, H. (2016). ggplot2: Elegant Graphics for Data Analysis.
    Springer-Verlag New York. <https://ggplot2.tidyverse.org>
