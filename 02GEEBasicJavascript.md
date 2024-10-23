# Basic Javascript Commands in GEE
GEE allows accessing and processing of datasets through a GEE IDE, which is based on javascript. So, those familiar with
javascript will be in familiar territory when using the GEE IDE.
Some basic commands are listed below:
## Storing and printing strings
```Javascript
//this is how comments are written
/*..or if you
want to have multi-line comments,
this is the way to do it
*/
var myString1 = "this is a string in double quotes";
var myString2 = 'string is single quotes are also allowed';
print('This command will print the contents within the quotes and the object(s) following the quote', myString1);
//remember to end each statement with a semicolon(;)
```
## Storing and printing numbers
```Javascript
var myNumber = 90;
print("The number in the variable myNumber is: ",90);
```
## Lists
```Javascript
//lists may contain numbers, strings or other objects enclosed in []
var numList = [2,4,6,8,10];
var strList = ['one','two','three'];
print('The number list is:',numList); 
print('The string list is:', strList);
//to access individual items of the list, use numerical indexing starting from 0
print('the first item of numList is: ', numList[0]);
```
## Dictionaries
```Javascript
//Dictionaries contain key-value pairs within {}
var thisDict = {
     Continent : "Asia",
     Country : "Nepal",
     Cities : ["Kathmandu","Pokhara", "Bhairahawa"]
};
// Dictionary values are accessed using keys
print('Accessing the continent and country:', thisDict['Continent']); // or thisDict.Continent
print('Accessing Kathmandu city from cities key in thisDict: ', thisDict.Cities[0]);


