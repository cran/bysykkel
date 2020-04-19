
<!-- README.md is generated from README.Rmd. Please edit that file -->

# bysykkel

<!-- badges: start -->

[![CRAN
status](https://www.r-pkg.org/badges/version/bysykkel)](https://cran.r-project.org/package=bysykkel)
[![license](https://img.shields.io/github/license/mashape/apistatus.svg?maxAge=2592000)](https://github.com/imangR/bysykkel/blob/master/LICENSE)
[![Downloads](http://cranlogs.r-pkg.org/badges/grand-total/bysykkel)](https://cran.r-project.org/package=bysykkel)
<!-- badges: end -->

## Overview

bysykkel is an R package that provides functions that simplifies the
task of gathering Norwegian city bike data for data analysis. bysykkel
provides functions to read city bike data directly to R or download it
to your R session’s working directory.

  - `read_trips_data()` reads bike trip records to R as a data frame.
  - `fread_trips_data()` fast reads bike trip records to R as a data
    frame by utilizing `fread()` from data.table.
  - `dl_trips_data()` downloads bike trip records to your working
    directory
  - `get_api_data()` gets real-time data from the specified city bike
    API service.

bysykkel lets you, the user, focus on data exploration, visualization,
statistical analysis, and building machine learning models on Norwegian
city bike data, by simplifying the task of getting the data. Indeed, the
purpose of bysykkel is to reduce time spent on getting Norwegian city
bike data, and lower barriers to start analyzing it.

The package name, *bysykkel*, is the Norwegian word for “city bikes”,
where *by* means “city”, and *sykkel* means “bike” (or “bicycle”).

## Installation

You can install the released version of bysykkel from
[CRAN](https://CRAN.R-project.org) with:

``` r
install.packages("bysykkel")
```

### Development version

Alternatively, you can install a development version of bysykkel from
GitHub to get bug fixes or new features before the next package version
is released on CRAN. To install the development version, you can use
devtools to install bysykkel from GitHub.

``` r
#! install.packages("devtools")
devtools::install_github("imangR/bysykkel")
```

## Bike data

bysykkel currently retrieves data from three\[1\] city bike services in
Norway that make bike data publicly available\[2\]:

  - [Oslo City Bike](https://oslobysykkel.no/en/open-data)
  - [Bergen City Bike](https://bergenbysykkel.no/en/open-data)
  - [Trondheim City Bike](https://trondheimbysykkel.no/en/open-data)

Each city bike service provide two data-related services:

  - Historical trip data
  - Real-time data

The historical trip data is available both as a CSV-file, and a
JSON-file, that contains monthly anonymized historical bike trip data.
Real-time data is available in the [GBFS
format](https://github.com/NABSA/gbfs/blob/master/gbfs.md), and must be
accessed with each city bike’s API service, which provide information
about

  - bike and dock availability;
  - stations (including geolocation, addresses and station
    descriptions); and
  - machine readable basic information about the city bike service.

The data is made available under the Norwegian License for Open Data
2.0, abbreviated as NLOD 2.0, which you can read about
[here](https://data.norge.no/nlod/en/2.0).

## Examples

### Read bike trips data to R

``` r
library(bysykkel)

# Get bike trip data for April, 2019 for Oslo as a data frame
oslo_trips <- read_trips_data(year = 2019, month = 04, city = "Oslo")

# Get winter bike data for January, 2019 for Oslo as a data frame
oslo_trips <- read_trips_data(2019, 1, "Oslo")

# Fast read bike data from June to August in 2018 for Bergen with `lapply()`,
# and `rbind()` the resulting `list` with `do.call()` to get a data frame

#! install.packages("data.table")

bergen_trips <- lapply(06:08, fread_trips_data, year = 2018, city = "Bergen")
bergen_trips <- do.call(rbind, bergen_trips)

# Alternatively, use `map_dfr()` from `purrr` instead of `lapply()`,
# `rbind()`, and `do.call() to get the same result: a data frame

#! install.packages("purrr")

library(purrr)

bergen_trips <- map_dfr(6:8, fread_trips_data, year = 2018, city = "Bergen")
```

**NB\!** I recommend that you use `fread_trips_data()` to fast read city
bike data, especially if you want to read bike data for several months.

**NB\!** data.table is not automatically installed with bysykkel, and
must be installed separately with `install.packages("data.table")` if
you want to use `fread_trips_data()`.

### Download bike trips data

``` r
library(bysykkel)

# Download bike trip data for April 2019 for Trondheim
dl_trips_data(2019, 04, "Trondheim", filetype = "JSON")
#> The CSV-file is downloaded to your R session's current working directory

# Download bike trip data for summer 2018 for Oslo
lapply(06:08, dl_trips_data, year = 2018, city = "Oslo", filetype = "CSV")
#> The CSV-file for each month is downloaded to your R session's working directory
```

### Get real-time data from the API service

**NB\!** Please read each City Bike’s guide on how to correctly use
their API service before using `get_api_data()`. See [Oslo City Bike’s
guide](https://oslobysykkel.no/en/open-data/realtime) as an example.

The `return_df` argument in `get_api_data()` specifies whether you want
to return the result as a data frame. If `return_df = FALSE` (default),
then the function returns a list that contains a data frame, and a
number that represents the datetime (in POSIX format) of when you made
the API request.

``` r
library(bysykkel)

# Get API data on bike stations as a data frame
oslo_stations <- get_api_data(client_id = "myname-myapp", 
                              data = "stations",
                              city = "Oslo",
                              return_df = TRUE)

# Get API data for bike availability as a list that contains a data frame, and
# a number that represents the (POSIX) time of when you made the API request
bergen_availability <- get_api_data(client_id = "mycompany-myservice",
                                    data = "availability",
                                    city = "Bergen",
                                    return_df = FALSE)

# Get API data on bike system information
trondheim_system <- get_api_data("Ola Nordmann-bike dashboard",
                                 "system",
                                 "Trondheim",
                                 return_df = FALSE)
```

## File an issue or suggest an improvement

If you want to report a discovered bug, raise some other issue, or
suggest an improvement to bysykkel, then please file an issue on
[GitHub](https://github.com/PersianCatsLikeToMeow/bysykkel/issues). For
bugs, please file a minimal reproducible example.

## Known issues

No issues have been identified at this time in version 0.3.1.

-----

1.  It used to be four services, but the Oslo Winter Bike service has
    been shut down, and associated data is now unavailable. The bysykkel
    package has updated all functions in version 0.3.1 to remove any
    interface to the Oslo Winter Bike service.

2.  Bike data for [Bærum City
    Bike](https://www.baerum.kommune.no/tjenester/vei-trafikk-og-parkering/sykkel-i-barum/bysykkel/)
