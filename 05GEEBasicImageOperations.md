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
purpose, the map() function comes very handy. The map() funtion is utilitised by passing another function to it.
An illustration is given below (modification of previous coding block) to show how scale factor is applied to each of the 
12 bands of the hyperspectral images of sentinel data. The scale factor is provided in the metadata. The scale factor is applied so that this correction may be used in an expression later for deriving important results like the NDVI.
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
 var bands = img.select('B.*').multiply(0.001); //select and multiply by 0.001 to all bands starting with "B" 
//the formula for ndvi is (nir-red)/(nir+red) and may be applied in any of the following three ways in GEE
 var ndvi1 = bands.expression('(nir-red)/(nir+red)', {nir:bands.select('B8'),red:bands.select('B4')}).rename('ndvi1');
 var ndvi2 = (bands.select('B8').subtract(bands.select('B4'))).divide(bands.select('B8').add(bands.select('B4'))).rename('ndvi2');
 var ndvi3 = bands.normalizedDifference(['B8','B4']).rename('ndvi3');
 return ee.Image.cat([ndvi1,ndvi2,ndvi3])
 .copyProperties(img, img.propertyNames());

)};
print(sen2);
```
After executinig the above code block, if you now go to the console and check the properties of the output image collection, under the bands, instead of the original bands, there will be bands called 'ndvi1', 'ndvi2' and 'ndvi3', each calculated based on our expressions in the code block.
Expressions may also be used on single images as show in the code block below, which applies scale factor to each band of the image:
```Javascript
var sent2_img = ee.Image('COPERNICUS/S2_SR_HARMONIZED/20230102T045209_20230102T045209_T45RUM');
var sent2_bands = sent2_img.select('B.*').expression('img* 0.001',{img:sent2_img.select('B.*')});
var sent2_ndvi = sent2_bands.normalizedDifference(['B8','B4']).rename('ndvi');
print (sent2_ndvi);
```
