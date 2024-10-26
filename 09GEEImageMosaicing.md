# Mosaicing Images In GEE

Satellites caputre data by moving through a certain strip of land and cover the whole of the earth in a fixed time interval, which is called their temporal resolution. What we end up having are strips of images. It thus becomes necessary that for a certain area of our interest, to 'stitch' these strips of images together so that we can then have a complete image of the area, which can then be used for further processing. This 'stictching ' is called mosaicing. We will be using the Sentinel-2 data as we did previously in [Chapter 5](https://github.com/Sub-esh/GEEBasics/blob/main/05GEEBasicImageOperations.md).
```Javascript
//select point of interest
var poi = ee.Geometry.Point([85.36983728264772,27.702623556235);
//select area of interest around the point of interest
var aoi = ee.FeatureCollection('USDOS/LSIB_SIMPLE/2017')
          .filterBounds(poi);

// import the sentinel dataset
var sen2 = ee.ImageCollection('COPERNICUS/S2_SR_HARMONIZED')
           .select('B[2-4]','B8') // select only the visible bands (B2-B4) and near infrared band (B8)
           .filterDate('2020', '2021')
           .filterBounds(aoi) ;
```
After the importing and filtering of the dataset based on our area of interest, bands and time period is complete, we now we can proceed with the mosaicing. But first, we need to complete a few more tasks. We need to create a list of dates corresponding to the temporal resolution of our dataset, which, in the case of sentinel-2A dataset is 5 days. This task requires several steps. For ease of understanding, we will work through all the steps and then combine them together.
```Javascript
//define starting time and ending time for the dataset and find their difference(time interval between start and end dates)
var start_date = ee.Date('2020');
var end_date = ee.Date('2021');
var date_diff = end_date.difference(start_date,'day');
//create a sequence of numbers upto the time difference (365 in this case) with at an interval of 5 (temporal resolution of sentinel-2)
var num_list = ee.List.sequence(0,date_diff.subtract(1),5);  // starting from 0 hence stopping at 365-1 = 364
// create a sequence of dates from the sequence of numbers
var date_list = num_list.map(function(num){
                return start_date.advance(num,'days')
               });
//mosaic all the images in the sentinel dataset for the sequence of dates
var mosaic_sen2 = ee.ImageCollection(date_list.seq.map(function(date){
                  var st_date = ee.Date(date);
                  var en_date  = st_date.advance(5, 'days');
                  var img = sen2.filterDate(st_date, en_date).mosaic();
                  return img
                         .set('system:time_start', st_date.millis())
                         .set('system:time_end', en_date.millis());
}));

Map.addLayer(mosaic_sen2.select('B8').filter(ee.Filter.calendarRange(8,8,'month')).toBands().clip(aoi), [], 'Mosaic', false);
Map.addLayer(sen2.select('B8').filter(ee.Filter.calendarRange(8,8,'month')).toBands().clip(aoi), [], 'Original', false);
