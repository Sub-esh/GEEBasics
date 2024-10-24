# GEE Chart Basics
Previously, we have presented our results by adding them to the map as a layer or by using inspection charts on time-series datasets.GEE allows for generating customized charts to show our results in a presentable manner.The basic functions used for charting in GEE fall under ```ui.Chart.``` . Here, we will be looking at the ```ui.Chart.image.series()``` for our tutorial. 
The initial steps are similar to the previous chapter where we import the satellite datasests for our purpose and carry ut the 
necessary filtering and pre-processing. We will first select a point of interest for this purpose using the gui and store it under the name ```geometry```.
```Javascript
 var evt = ee.ImageCollection('MODIS/061/MOD16A2GF')
 .filterDate('2023','2024')
 .map(function(img){      // to apply scale factor based on dataset metadata
          var bands =  img.select('ET').multiply(0.1);
          return bands
          .copyProperties(img, img.propertyNames());
});
```
We follow that up with the charting, which is conveniently used inside the ```print()``` funtion. 
```Javascript
print(ui.Chart.image.series(
evt,     // parameter for dataset to be charted
geometry, // parameter for the area or point of interest
ee.Reducer.first(), // parameter for reducer, in this case, the first value corresponding to point of interest
500, // scale parameter corresponding to the spatial resolution of dataset
'system:time_start', //properties parameter corresponding to the property charted along the x-axis
));
