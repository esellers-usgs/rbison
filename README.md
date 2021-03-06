rbison
======


[![cran checks](https://cranchecks.info/badges/worst/rbison)](https://cranchecks.info/pkgs/rbison)
[![Build Status](https://api.travis-ci.org/ropensci/rbison.png)](https://travis-ci.org/ropensci/rbison)
[![Build status](https://ci.appveyor.com/api/projects/status/cba5mqg33hakour1?svg=true)](https://ci.appveyor.com/project/sckott/rbison)
[![codecov.io](https://codecov.io/github/ropensci/rbison/coverage.svg?branch=master)](https://codecov.io/github/ropensci/rbison?branch=master)
[![rstudio mirror downloads](http://cranlogs.r-pkg.org/badges/rbison?color=E664A4)](https://github.com/metacran/cranlogs.app)
[![cran version](http://www.r-pkg.org/badges/version/rbison)](https://cran.r-project.org/package=rbison)

Wrapper for the [USGS Bison](https://bison.usgs.gov) API

## Description

USGS Biodiversity Information Serving Our Nation (BISON) is a web-based federal mapping resource that provides access to georeferenced (those with latitude and longitude coordinates) and non-georeferenced data describing the occurrence or presence of terrestrial and aquatic species recorded or collected by a person (or instrument) at a specific time in the United States and its Territories. Each record in a species occurrence dataset available in BISON will typically consist of a scientific name (genus and specific epithet), a date, and one or more geographic references such as a state name, county name, and/or decimal latitude and longitude coordinates. In addition to these typical data fields, species occurrence datasets often include many other data fields that describe each species occurrence event in more detail.

`rbison` allows one to pull species occurrence data from these datasets, inspect species occurance summaries, and then map species occurance within the US, within the contiguous 48 states, and/or at county or state level.

Current data providers for BISON can be found at <https://bison.usgs.gov/providers.jsp>

See <https://bison.usgs.gov/#api> for API docs for the BISON API.


## Installation

From CRAN


```r
install.packages("rbison")
```

Or the development version from Github


```r
install.packages("devtools")
devtools::install_github("ropensci/rbison")
library('rbison')
```

Load package


```r
library("rbison")
```

Notice that the function `bisonmap` automagically selects the map extent to plot for you, being one of the contiguous lower 48 states, or the lower 48 plus AK and HI, or a global map. If some or all points outside the US, a global map is drawn, and throws a warning. . You may want to make sure the occurrence lat/long coordinates are correct.

## get data


```r
out <- bison(species = "Phocoenoides dalli dalli", count = 10)
```


### inspect summary


```r
out$summary
#>   specimen specimen.1
#> 1        7          7
```

### map occurrences


```r
bisonmap(out)
```

![plot of chunk unnamed-chunk-7](tools/unnamed-chunk-7-1.png)

## All points within the US (including AK and HI)

### get data


```r
out <- bison(species = "Bison bison", count = 300)
```


### inspect summary


```r
out$summary
#>   occurrences.legend.fossil occurrences.legend.observation
#> 1                       359                            927
#>   occurrences.legend.centroid occurrences.legend.specimen
#> 1                           1                         946
#>   occurrences.legend.unknown fossil observation centroid specimen unknown
#> 1                          6    359         927        1      946       6
```

### map occurrences


```r
bisonmap(out)
```

![plot of chunk unnamed-chunk-10](tools/unnamed-chunk-10-1.png)

##  All points within the contiguous 48 states

### get data


```r
out <- bison(species = "Aquila chrysaetos", count = 300)
```


### inspect summary


```r
out$summary
#>   occurrences.legend.literature occurrences.legend.fossil
#> 1                          1641                       642
#>   occurrences.legend.observation occurrences.legend.centroid
#> 1                         128207                           1
#>   occurrences.legend.unknown occurrences.legend.specimen literature fossil
#> 1                       9734                        1899       1641    642
#>   observation centroid unknown specimen
#> 1      128207        1    9734     1899
```


### map occurrences


```r
bisonmap(out)
```

![plot of chunk unnamed-chunk-13](tools/unnamed-chunk-13-1.png)


## Map county or state level data

### Counties - using last data call for Aquila


```r
bisonmap(out, tomap = "county")
```

![plot of chunk unnamed-chunk-14](tools/unnamed-chunk-14-1.png)

### States - using last data call for Aquila


```r
bisonmap(out, tomap = "state")
```

![plot of chunk unnamed-chunk-15](tools/unnamed-chunk-15-1.png)


## BISON SOLR interface

### taxa

The taxa service searches for and gives back taxonomic names


```r
bison_tax(query = "*bear")
#> $numFound
#> [1] 48
#> 
#> $names
#>       lc_vernacularName       vernacularName
#> 1  Louisiana black bear Louisiana black bear
#> 2            Sloth Bear           Sloth Bear
#> 3          grizzly bear         grizzly bear
#> 4              bear oak             bear oak
#> 5     yellow woollybear    yellow woollybear
#> 6            bear daisy           bear daisy
#> 7     banded woollybear    banded woollybear
#> 8    Asiatic black bear   Asiatic black bear
#> 9           Kodiak bear          Kodiak bear
#> 10     black-ended bear     black-ended bear
#> 
#> $highlight
#> NULL
#> 
#> $facets
#> NULL
```

And you can search by scientific name


```r
bison_tax(query = "Helianthus*", method = "scientificName")
#> $numFound
#> [1] 212
#> 
#> $names
#>                       scientificName
#> 1              Discoaster helianthus
#> 2  Helianthus divaricatus latifolius
#> 3             Helianthus decapetalus
#> 4                Helianthus ambiguus
#> 5             Helianthus dowellianus
#> 6               Helianthus luxurians
#> 7               Helianthus arenicola
#> 8              Helianthus atrorubens
#> 9               Helianthus frondosus
#> 10   Helianthus nuttallii canadensis
#> 
#> $highlight
#> NULL
#> 
#> $facets
#> NULL
```

### occurrence search

The occurrence service searches by scientific names and gives back occurrence 
data similar to data given back by the `bison()` function

Searching for data and looking at output


```r
x <- bison_solr(scientificName = "Ursus americanus", rows = 10, 
    fl = "scientificName,decimalLongitude,decimalLatitude")
x$points
#>    decimalLongitude   scientificName decimalLatitude
#> 1          -92.8370 Ursus americanus        48.48344
#> 2         -150.5500 Ursus americanus        59.59000
#> 3         -150.5500 Ursus americanus        59.59000
#> 4         -149.6600 Ursus americanus        59.83000
#> 5         -124.0676 Ursus americanus        46.28618
#> 6         -121.5178 Ursus americanus        41.75872
#> 7         -143.4417 Ursus americanus        64.95393
#> 8         -109.6747 Ursus americanus        34.98864
#> 9         -123.4106 Ursus americanus        47.78021
#> 10        -116.1590 Ursus americanus        33.98085
```

Mapping the data


```r
out <- bison_solr(scientificName = "Ursus americanus", rows = 200)
bisonmap(out)
```

![plot of chunk unnamed-chunk-19](tools/unnamed-chunk-19-1.png)

## Meta

* Please [report any issues or bugs](https://github.com/ropensci/rbison/issues).
* License: MIT
* Get citation information for `rbison` in R doing `citation(package = 'rbison')`
* Please note that this project is released with a [Contributor Code of Conduct](CODE_OF_CONDUCT.md). By participating in this project you agree to abide by its terms.

This package is part of a richer suite called [SPOCC Species Occurrence Data](https://github.com/ropensci/spocc), along with several other packages, that provide access to occurrence records from multiple databases. We recommend using SPOCC as the primary R interface to rbison unless your needs are limited to this single source.

[![ropensci_footer](https://ropensci.org/public_images/github_footer.png)](https://ropensci.org)
