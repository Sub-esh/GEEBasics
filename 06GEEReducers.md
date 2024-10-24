# GEE Reducers

GEE provides certain statistical functions, called reducers, which come in handy especially when dealing with time-series data and 
anlyses.In this tutorial, we will be using the 16 day MODIS NDVI 250m reolutiono data. It is a course data and will not require much filtering like in the tutorial in the previous chapters. 

A simple way to visualize time-series data is to create onle layer over the other, or a stack of layers, with each layer
from a different time period.This can be achieved in GEE using the toBands() function as given below.
```Javascript
//define an area of interest(aoi) using the gui rectangle/polygon tool and import it as 'geometry'
Map.centerObject(geometry) // center the aoi in the map for better visualisation
var modis = ee.ImageCollection('MODIS/061/MOD13Q1')  //we will only require the NDVI band for this turorial
var ndvi = modis.select('NDVI') // we will only be using 'ndvi' band for this tutorial
.filterDate('2023','2024') //other filters are not necessary as resolution is course in MODIS
.map(function(img){
  var bands = img.multiply(0.001);
  return bands
  .copyProperties(img, img.propertyNames());
  
});

var stacked = ndvi.toBands(); // 
Map.addLayer(stacked.clip(geometry), {}, 'stacked_ndvi', false); // add result to the map for visualisation
```
The stacked layers of different time periods can be better visualized using the "inspector" near the console tab of the GEE editor.
When a point on the map is clicked with the inspector crosshair, the pixel-values of each layer at the point is displayed based on 
the time period in a bar graph, which facilitates better ease of data interpretation. 
It is often the case that, depending on the aoi, the code above could and often does result in error that is related to transformation of the co-ordinate systems. In such cases, we modify in line 20 above as ```stacked.clip(geometry.geometries())``` or ```stacked.clip(geometry.bounds())```. If the error persists, you might need to reproject our dataset(as shown in the following code block). One might be interested in performing pixel-wise simple statistical operations on the property of the layers over time. GEE's special functions, called reducers are useful for completing such tasks. An example is shown below:
```Javascript
  
Map.centerObject(geometry) // center the aoi in the map for better visualisation
var modis = ee.ImageCollection('MODIS/061/MOD13Q1')  //we will only require the NDVI band for this turorial
var ndvi = modis.select('NDVI') // we will only be using 'ndvi' band for this tutorial
.filterDate('2023','2024') //other filters are not necessary as resolution is course in MODIS
.map(function(img){
  var bands = img.multiply(0.001);
  var projected = bands.reproject('EPSG:4326', null, 250); //reprojecting the layer to WGS84 at 250m resolution
  return projected
  .copyProperties(img, img.propertyNames());
  
});

var ndviMean = ndvi.mean() //calculates mean ndvi for the aoi in the selected time period
Map.addLayer(ndviMean.clip(geometry), {}, 'ndvi_mean', false); // add result to the map for visualisation

var ndviMax = ndvi.max() //calculates maximum ndvi for the aoi in the selected time period
Map.addLayer(ndviMax.clip(geometry), {}, 'ndvi_max', false); // add result to the map for visualisation
/* other reducers include: minimum - .minimum(), median - .median(), standard deviation -
.reducer(ee.Reducer.stdDev()*/
```
## Applying Reducers to the whole image area

The code above is for a pixel selected by the user. What if we wanted to find the average value of pixel in the whole of the image?
In such cases, we will use the .Reducer function as shown below:
```Javascript
Map.centerObject(geometry) // center the aoi in the map for better visualisation
var modis = ee.ImageCollection('MODIS/061/MOD13Q1')  //we will only require the NDVI band for this turorial
var ndvi = modis.select('NDVI') // we will only be using 'ndvi' band for this tutorial
.filterDate('2023','2024') //other filters are not necessary as resolution is course in MODIS
.map(function(img){
  var bands = img.multiply(0.001);
  var projected = bands.reproject('EPSG:4326', null, 250); //reprojecting the layer to WGS84 at 250m resolution
  return projected
  .copyProperties(img, img.propertyNames());
  
});

var ndviMean = ndvi.mean() //calculates mean ndvi for the aoi in the selected time period

var ndviMax = ndvi.max() //calculates maximum ndvi for the aoi in the selected time period

var ndviMedian = ndvi.median();

var ndviMedianMax = ee.Number(ndviMean.reduceRegion({
                              reducer: ee.Reducer.max(), geometry: geometry, scale: 250
}).values().get(0));      // .values().get(0) extracts the first value of the output layer while ee.Number converts the resilting output to a numerical value

```
## Exporting Results
We intrduced in the previous blocks how map layers can be added in the map for visualisation. It is also possible to save the
output results. The code below is a simple illutration of saving the output result to google drive(provided we coninue from 
previous code block).
```Javascript
Export.image.toDrive(
 image : ndvi_mean,
 description: 'MeanNDVI',
 scale: 250,
 region: geometry,
 crs: 'EPSG:4326',
 folder: 'tutorial');
```
