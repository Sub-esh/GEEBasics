# Earth Engine Objects
The previous chapter was a discussion on pure Javascript datatypes which could be used in GEE. However, GEE optimizes the
storage, retrieval and processing of different datatypes using different GEE objects. These objects make processing large
amounts of data faster and more effiecient by communicating with the GEE servers through various algorithms at the GEE backend.
Earth engine objects are generally declared using GEE functions that start with "ee.".
Some examples are given below:

## GEE strings
```Javascript
//regular Javascript string
var myString = "A regular Javascript string";
//GEE string
var mygeeString = ee.String("An earth engine string"); // the eeString object is stored in the GEE server
```
## GEE numbers
```Javascript
//regular Javascript number
var myNum = 90;
// GEE number
var mygeeNum = ee.Number(90)
```
## GEE lists
```Javascript
//regular Javascript lists
var mylist = [1,3,5,7,9];
//GEE lists
var mygeeList1 = ee.List([1,3,5,7,9]);
//GEE lists using sequence function
var mygeeList2 = ee.List.sequence(1,9,2) ; // creates list [1,3,5,7,9]
//GEE lists are accessed using get function
print(mygeeList2.get(2)); // prints 5
```

## GEE dates
```Javascript
//Dates in GEE may be defined in the following ways
var myDate1 = ee.Date('2024-11-23');
var myDate2 = ee.Date.fromYMD(2024,11,23);
var myDate3 = ee.Date.fromYMD({
                          day: 23,
                          month: 11,
                          year: 2024
});
//operating on time objects
var yearDate1 = myDate1.advance(1, year); // 2025-11-23
var monthDate1 = myDate1.advance(1, month); // 2024-12-23
var dayDate1 = myDate1.advance(1, day); // 2024-12-24
```

## GEE dictionaries
```Javascript
//regular Javascript dictionary
var myDict = {
     Continent : "Asia",
     Country : "Nepal",
     Cities : ["Kathmandu","Pokhara", "Bhairahawa"]
};
//GEE dictionary
var mygeeDict = ee.Dictionary(myDict);
//access GEE dictionary items using keys in get function
print(mygeeDict.get('Continent') // Prints Asia
