geoparser
=========

Installation
============

To install the package, you will need the devtools package.

``` r
library("devtools")
install_github("masalmon/geoparser")
```

This package is an interface to the [geoparser.io API](https://geoparser.io) that identifies places mentioned in text, disambiguates those places, and returns data about the places found in the text.

To get an API key, you need to register at <https://geoparser.io/pricing.html>. With an hobbyist account, you can make up to 1,000 calls a month to the API. For ease of use, save your API key as an environment variable as described at <https://stat545-ubc.github.io/bit003_api-key-env-var.html>.

The package will conveniently look for your API key using `Sys.getenv("GEOPARSER_KEY")` so if your API key is an environment variable called "GEOPARSER\_KEY" you don't need to input it manually.

Geoparsing
==========

You need to input a text whose size is less than 8kB.

The output is list of 2 data.frames (dplyr tbl\_df). The first one is called properties and contains

-   the apiVersion called apiVersion

-   the source of the results

-   the id of the query

The second data.frame contains the results and is called results:

-   properties.country is the ISO-3166 2-letter country code for the country in which this place is located, or NULL for features outside any sovereign territory.

-   properties.confidence is a confidence score produced by the place name disambiguation algorithm. Currently returns a placeholder value; subject to change.

-   properties.name is the best name for the specified location, with a preference for official/short name forms (e.g., "New York" over "NYC," and "California" over "State of California"), which may be different from exactly what appears in the text.

-   properties.admin1 is a code representing the state/province-level administrative division containing this place. (From GeoNames.org: "Most adm1 are FIPS codes. ISO codes are used for US, CH, BE and ME. UK and Greece are using an additional level between country and fips code. The code '00' stands for general features where no specific adm1 code is defined.")

-   properties.type is a text description of the geographic feature type — see GeoNames.org for a complete list. Subject to change.

-   geometry.type is the type of the geographical feature, e.g. "Point"

-   geometry.coordinates1 is the longitude

-   geometry.coordinates1 is the latitude

-   reference1 the start (index of the first character in the place reference) -- each reference to the this place name found in the input text is on one distinct line.

-   reference2 the end (index of the first character after the place reference) -- each reference to the this place name found in the input text is on one distinct line.

``` r
library("geoparser")
output <- geoparser_q("I was born in Vannes and I live in Barcelona")
output$properties
```

    ##   apiVersion       source                    id
    ## 1      0.3.4 geoparser.io BDx1bAbcrXV3u5rrZ1ALn

``` r
knitr::kable(output$results)
```

| type    | properties.country | properties.confidence | properties.name | properties.admin1 | properties.type                                | id      | geometry.type |  geometry.coordinates1|  geometry.coordinates2|  number|  reference1|  reference2|
|:--------|:-------------------|:----------------------|:----------------|:------------------|:-----------------------------------------------|:--------|:--------------|----------------------:|----------------------:|-------:|-----------:|-----------:|
| Feature | FR                 | 1                     | Vannes          | A2                | seat of a second-order administrative division | 2970777 | Point         |               -2.75000|               47.66667|       1|          14|          20|
| Feature | ES                 | 1                     | Barcelona       | 56                | seat of a first-order administrative division  | 3128760 | Point         |                2.15899|               41.38879|       1|          35|          44|
