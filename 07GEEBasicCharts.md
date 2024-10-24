# GEE Chart Basics

Previously, we have presented our results by adding them to the map as a layer or by using inspection charts on time-series datasets.GEE allows for generating customized charts to show our results in a presentable manner.The basic functions used for charting in GEE fall under ```ui.Chart.``` . Here, we will be looking at the ```ui.Chart.image.series()``` for our tutorial. 
The initial steps are similar to the previous chapter where we import the satellite datasests for our purpose and carry ut the 
necessary filtering and pre-processing. We will first select a point of interest for this purpose using the gui and store it under the name ```geometry```.
```Javascript
 var evt = ee.ImageCollection('MODIS/061/MOD16A2GF')
 .filterDate('2023','2024')
 .map(function(img){      // to apply scale factor based on dataset metadata
          var bands =  img.select('ET').multiply(0.1);
          return bands.rename('modisEVT')
          .copyProperties(img, img.propertyNames());
});
```
We follow that up with the charting, which is usually more conveniently used inside the ```print()``` funtion. 
```Javascript
print(ui.Chart.image.series(
evt,     // parameter for dataset to be charted
geometry, // parameter for the area or point of interest
ee.Reducer.first(), // parameter for reducer, in this case, the first value corresponding to point of interest
500, // scale parameter corresponding to the spatial resolution of dataset
'system:time_start', //properties parameter corresponding to the property charted along the x-axis
));
```
## Changing Chart elements

It is often desired that the default chart obtained from GEE is modified to make suit our preferences and needs. The ```.setChartStyle()``` allows for changing the type of chart - *LineChart, ScatterChart, ColumnChart,AreaChart* etc. ```.setOptions``` allows for more customizations like chart title, x- and y-axis labels, plot color, line-width among other chart elements.
```Javascript
print(ui.Chart.image.series(
evt,geometry, ee.Reducer.first(), 500,'system:time_start', )
.setChartType('LineChart')
.setOptions({
title: 'Evapotranspiration', // chart title
hAxis: {title:'Time Period'}, // x- axis label
vAxis: {title:'Evapotranspiration'}, // y-axis label
series: {0,{color:'red', lineWidth: 2, pointSize: 4}} // apply formatting to the first plot(0) in the chart
});
```
## Plotting multiple plots in a chart

It is also possible to have multiple plots on the same chart. For this purpose, we add draw two polygons on the map using the gui.
We then change the type of these polygons from *geometry* to *featurecollection* and proceed with the chart plotting as done previously. Also, the *ui.Chart.image.serisByRegion()* function is utilized as shown below:
```Javascript
print(ui.Chart.image.seriesByRegion(
evt,geometry, ee.Reducer.first(), 'modisEVT', 500,'system:time_start', 'system:index' )
.setChartType('LineChart')
.setOptions({
title: 'Evapotranspiration', // chart title
hAxis: {title:'Time Period'}, // x- axis label
vAxis: {title:'Evapotranspiration'}, // y-axis label
series: {0,{color:'red', lineWidth: 2, pointSize: 4},
         1,{color:'blue', lineWidth: 2, pointSize: 4}} // apply formatting to the first plot(0) and second plot(1)in the chart
});
```
