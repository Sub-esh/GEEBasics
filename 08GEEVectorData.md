# Working with Vector Data in GEE editor
So far in GEE, we have been working with images and image collections. In geospatial terminology, these fall under the raster datatype. While working on projects, we often require working with other kinds of data, called vector data. We have already used a few variations of the vector data like the points and polygons drawn on the map layer using the GEE UI. However, they are not useful in most practiacal applications. One solution is provided by GEE, as it provides a large range of vector datasets. In GEE dataset catalog, all the vector data are stored under *FeatureCollection*. Some of these *FeatureCollection* include the HydroSHEDS datasets, Global Administrative Unit Layers among others. 
```Javascript
var basins = ee.FeatureCollection('WWF/HydroSHEDS/v1/Basins/hybas_6');  
var countries = ee.FeatureCollection('WM/geoLab/geoBoundaries/600/ADM0'); 

var poi = ee.Geometry.Point([85.35209292087292,27.6936706603162]);

var aoiBasin = basins.filterBounds(poi);  // filter only the basin in the selected point of interest
var aoiCountry = countries.filterBounds(poi); //filter only the country in the selected point of interest

Map.addLayer(aoiCountry);
Map.addLayer(aoiBasin, {color: 'blue'});
```

# Importing a vector layer from external sources
The need often arises when certain vector data require to be imported into GEE.A shapefile is a very popular way to store vector data  A shapefile essentially contains a geometry(point, line or polygon) and consists of several files, with mainly four files having formats : .shp, .dbf, .dxf and .prj. If one is to import a shapefile into GEE, all the four associated files must be imported together. 
To import an external shapefile, go to *Assests* tab on the left hand window of the GEE editor. Select *New* and click on *SELECT* under source files. Then select the location where the shapefile is stored. Select all four exstensions for the shapefile and follow the prompts to import the shapefile. The shapefile will be imported, by default, under the name *table*. The following script a contunuation of the proceeding steps to operating on the land surface temperature from Modis dat in the imported shapefile area of interest:
```Javascript
Map.centerObject(table);
Map.addLayer(table,{color:'red'});

var aoi = table.map(function{               // this is to simplify the geometry so that the imported shapefile has lesser numner of vertices for more effecient processing
          return table.simplify(1000);
});

//import the modis data
var LST = ee.ImageCollection("MODIS/061/MOD11A2")
.select('LST_Day_1km')
.filterDate('2023','2024').mean().multiply(0.02).subtract(273.15); // x0.02 is scale factor and -273.15 is to convert temperature to degree Celsius from degree Kelvin

// clip(restrict) the modis data to our region of intereset
var clippedAOI = LST.clip(aoi);

//visualize the clipped aoi on the map with a suitable color pallette for low, medium and high values of temperatures respectively 
Map.addLayer(clippedAOI, {palette: ['blue', 'orange', 'red']},'Land Surface Temperature',false);
 //you might need to adjust the layer stretch in the layer properties for better visualization
```
