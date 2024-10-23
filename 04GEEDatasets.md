# Searching and Accessing GEE datasets
With such large volumes of datasets available in the GEE dataset catalogue, one could be intimidated to search and select the
ones that are most relevant to their research or use case. 
A simple way to filter datasests is to go to the GEE catalogue website and search using tags.
Awesome-gee-community-catalog is another open-source catalogue that maintaines structured and easily filtered lists of 
GEE datasests.

## Loading Datasets into GEE editor
After browsing through the google catalog and identifying the dataset of interest, all the details of the dataset is given on
a dedicated page.
For example, for landsat images, if we select Landsat > Landsat 9 > Tier 2 > USGS Landsat 9 Level 2, 
Collection 2, Tier 2 image collection, the webpage shows all the metadata, time-period (Dataset availability), sample codes,
GEE snippet to import the dataset, among other details about the dataset. The following codes show how to access and process
this data in GEE editor:
```Javascript
//import the whole image collection
var landSat = ee.ImageCollection("LANDSAT/LC09/C02/T2_L2")
```
Usually, importing the whole image collection is not necessary as the image collection contains planetary-scale datasest for 
a large time period.
For this reason, we generally apply a filter to the datasets based on our area of interest (bounds) and/or timeframe or any
other parameters as and when necessary.
```Javascript
//filter the image cllection based on co-ordinates of a certain location on earth (here kathmandu)
var landSat = ee.ImageCollection("LANDSAT/LC09/C02/T2_L2")
.filterBounds(ee.Geometry.Point([85.31790903605975,27.694708089293155])) // Kathmandu images only
.filter(ee.Filter.lt('CLOUD_COVER',10)) // cloud cover less than 10
.filterDate('2022-09-01','2024-09-01') //filter date between September 1, 2022  to September 1, 2024
print(landSat)
//store a single image from an image collection
var singleImage = ee.Image('LANDSAT/LC09/C02/T2_L2/LC09_201024_20221209')
```
Properties that need to be filtered have specific names described in the datasets which are accessed on the console to 
the right of the GEE editor. 
To get the parameters required for a function (eg. lt()), press ctrl + space after typing "lt" . 
To get more information about the filters that can be used in GEE, search the docs for "filter".
