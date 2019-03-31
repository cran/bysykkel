# bysykkel 0.1.1.0

## Patches

### fread_trips_data()

* Repaired function so that it correctly uses URL to read CSV-file that 
contains requested trip records data.

### read_trips_data()

* Repaired the function so that it properly reads special Norwegian characters
in the Norwegian language that appear with data on bike stations.

## Other

* Updated the example section in the object documentation for `dl_trips_data()`,
`fread_trips_data()`, and `read_trips_data()`.

# bysykkel 0.1.0.0

## Major
Package release. Functions released with the package are

* `dl_trips_data()`
* `fread_trips_data()`
* `get_api_data()`
* `read_trips_data()`
