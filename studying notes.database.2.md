title: JSON Data
categories: 
    - Studying Notes
    - Database
tags: [JSON]
date: 2016-06-23
---
<img src="https://c7.staticflickr.com/8/7392/27571792310_2e06922f54.jpg" width="500" height="232">
<!--more-->
----

JSON(javascript object notation):
* standard for "serializing" data objects, usually in files
* human-readable, useful for data interchange
* also useful for representing & storing semistructured data
* no longer tied to javascript, parsers for many languages

basic constructs(recursive):
* base values: number, string, boolean...
* objects{}: sets of label-value pairs
* arrays[]: lists of values

Relational Model versus JSON:

|<font size=2>  (none) </font> |<font size=2>  Relational  </font>|<font size=2>  JSON  </font>|
| :| |
|<font size=2>  structure </font> |<font size=2>  tables  </font>|<font size=2>  nested sets/arrays  </font>|
|<font size=2>  schema </font> |<font size=2>  fixed in advance  </font>|<font size=2>  "self-describing" flexible  </font>|
|<font size=2>  queries </font> |<font size=2>  simple expressive languages  </font>|<font size=2>  widely used  </font>|
|<font size=2>  ordering </font> |<font size=2>  none  </font>|<font size=2>  arrays  </font>|
|<font size=2>  implementation </font> |<font size=2>  native systems  </font>|<font size=2>  coupled with programming languages, NoSQL systems  </font>|

XML versus JSON:

|<font size=2>  (none) </font> |<font size=2>  XML  </font>|<font size=2>  JSON  </font>|
| :| |
|<font size=2>  verbosity </font> |<font size=2>  more  </font>|<font size=2>  less  </font>|
|<font size=2>  complexity </font> |<font size=2>  more  </font>|<font size=2>  less  </font>|
|<font size=2>  validity </font> |<font size=2>  DTDs/XSDs widely used  </font>|<font size=2>  JSON schema not widely used  </font>|
|<font size=2>  prog. Interface </font> |<font size=2>  clunky "impedence mismatch"  </font>|<font size=2>  more direct  </font>|
|<font size=2>  querying </font> |<font size=2>  xpath, xquery, XSLT  </font>|<font size=2>  JSON path, JSON query, JAQL  </font>|

semantically valid JSON:
* adheres to basic structural requirements
* conforms to specified schema

<img src="https://c5.staticflickr.com/8/7263/27237284084_9ebeee9575.jpg" width="500" height="173">

Demo of JSON:
```
{ "Books":
  [
    { "ISBN":"ISBN-0-13-713526-2",
      "Price":85,
      "Edition":3,
      "Title":"A First Course in Database Systems",
      "Authors":[ {"First_Name":"Jeffrey", "Last_Name":"Ullman"},
                  {"First_Name":"Jennifer", "Last_Name":"Widom"} ] }
    ,
    { "ISBN":"ISBN-0-13-815504-6",
      "Price":100,
      "Remark":"Buy this book bundled with 'A First Course' - a great deal!",
      "Title":"Database Systems:The Complete Book",
      "Authors":[ {"First_Name":"Hector", "Last_Name":"Garcia-Molina"},
                  {"First_Name":"Jeffrey", "Last_Name":"Ullman"},
                  {"First_Name":"Jennifer", "Last_Name":"Widom"} ] }
  ],
  "Magazines":
  [
    { "Title":"National Geographic",
      "Month":"January",
      "Year":2009 }
    ,
    { "Title":"Newsweek",
      "Month":"February",
      "Year":2009 }
  ]
}
```

Demo of JSON schema:
```
{ "type":"object",
  "properties": {
     "Books": {
        "type":"array",
        "items": {
           "type":"object",
           "properties": {
              "ISBN": { "type":"string", "pattern":"ISBN*" },
              "Price": { "type":"integer",
                         "minimum":0, "maximum":200 },
              "Edition": { "type":"integer", "optional": true },
              "Remark": { "type":"string", "optional": true },
              "Title": { "type":"string" },
              "Authors": { 
                 "type":"array",
                 "minItems":1,
                 "maxItems":10,
                 "items": {
                    "type":"object",
                    "properties": {
                       "First_Name": { "type":"string" },
                       "Last_Name": { "type":"string" }}}}}}},
     "Magazines": {
        "type":"array",
        "items": {
           "type":"object",
           "properties": {
              "Title": { "type":"string" },
              "Month": { "type":"string",
                         "enum":["January","February"] },
              "Year": { "type":"integer" }}}}
}}
```
If type is object, it has properties which contains many key-value pairs; if type is array, it has items describing every kind of type inside array.