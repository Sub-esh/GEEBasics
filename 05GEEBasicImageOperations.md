# Basic image operations in GEE
This section will cover some image operations in GEE. We will be using Sentinel 2 data for this section.
You may import the sentinel MSI 2A data as described in the previous section. For a quicker import, you may use the 
GEE editor search bar to search for the specific dataset and then import the data. The data will be stored in a variable
that is declared automatically (usually imageCollection) which can then be used in the current project as required.

```Javascript
//assign a variable to store geometry for AOI
var myCountry = ee.FeatureCollection('USDOS/LSIB_SIMPLE/2017')
.filter(ee.Filter.eq('country_co','NP'));
//assign the imported sentinel dataset to a new variable for easier operation
var sen2 = imageCollection // assuming imageCollection is the name of the imported dataset
.filterBounds(myCountry)
.filterDate('2023','2024)  // only include data between years 2023 and 2024
.filter(ee.Filter.lt('CLOUDY_PIXEL_OVER_LAND_PERCENTAGE',10))
```
It is often necessary to loop through each individual data from a collection of images when working in GEE. For this 
purpose, the map() function comes very handy. The map() is utilitised by passing another function to it.
An illustration is given below(continuation of previous coding block) to show how scale factor is applied to each of the 
12 bands of the hyperspectral images of sentinel data. The scale factor is provided in the metadata. The scale factor is applied
so that this correction may be used in an expression later for deriving important results like the NDVI.
```Javascript
//assign a variable to store geometry for AOI
var myCountry = ee.FeatureCollection('USDOS/LSIB_SIMPLE/2017')
.filter(ee.Filter.eq('country_co','NP'));
//assign the imported sentinel dataset to a new variable for easier operation
var sen2 = imageCollection // assuming imageCollection is the name of the imported dataset
.filterBounds(myCountry)
.filterDate('2023','2024)  // only include data between years 2023 and 2024
.filter(ee.Filter.lt('CLOUDY_PIXEL_OVER_LAND_PERCENTAGE',10))
.map(function(img){
  var bands = img.select('B.*').multiply(0.001)
  var NDVI = 
)};
```
