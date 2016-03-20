title: Fetch Stock Data
categories:
    - project
    - Web Develop
tags: [PHP, XML, JSON, FORM]
date: 2016-03-11
---
![](https://farm2.staticflickr.com/1501/25827795981_835ee7fb4b_z.jpg)

# Introduction
In this project, I create a webpage used to search stock information with the help of Markit on Demand APIs, and the search results will be displayed in tabular format. After getting the search list, users can click the more information link to get more details stock information about the company.  In this project, I used the PHP programming language to deal with the POST method to realize the search function, then I use the GET method to get the detail information. In both method I get XML file through Markit on Demand. I also use the formation function of PHP to show the data readable.
<!-- more -->
----

# Get Search Data from Markit on Demand
I build form including a search input and submit button. When user input their search query and click the submit button, it will apply the POST method and get the input value as the search query which is stored in a variable $company.

``` php
<input type="search" name="textbox" id="textbox" required pattern=".*\S{1}.*" value=
    <?php
    if ($_SERVER["REQUEST_METHOD"] == "POST") {
        echo $_POST["textbox"];
    }
    if ($_SERVER["REQUEST_METHOD"] == "GET" and isset($_GET["textbox"])) {
        echo $_GET["textbox"];
    }?>>
<button type="submit" name="Submit" value="submit" onclick="check_validation()">Submit</button>
```
After we put the search input query in the variable $company, we create a url which include “http://dev.markitondemand.com/MODApis/Api/v2/Lookup/xml?input=” and $company. Then we can use the function file_get_contents(url)  to get the XML file. Then we can use the PHP’s XML parse to get the data and put the data into a table as shown in the figure above.

``` php
<?php  
function getresult($company) {
    $company = "";
    if ($_SERVER["REQUEST_METHOD"] == "POST") {
        $company = $_POST["textbox"];

        if ($company != "") {
            getresult($company);
        }
    }
    $url = "http://dev.markitondemand.com/MODApis/Api/v2/Lookup/xml?input=" . $company;
    $result = file_get_contents($url);
    $xml_doc = simplexml_load_string($result);
}
?>
```
# Get Detailed Information from Markit on Demand

After the search of a query, we get a result table which include more information link at the end of each row. We can click it to get more information where I use the GET method the submit it. In this times we use the http://dev.markitondemand.com/MODApis/Api/v2/Quote/json?symbol= and $symbol to get the JSON file. After this we can use the PHP’s JSON parse to get the stock information into the table. Because the information in JSON is not readable for person, we should use some information to transfer the JSON data. For example, we can transfer JSON’s timestamp, Fri Mar 18 00:00:00 UTC-04:00 2016,into a readable data, 2016-03-18 7:59 PM UTC,  by `date("Y-m-d g:i A T", strtotime($more_info->{'Timestamp'}))`.

```php
<?php
$url_moreinfo = "http://dev.markitondemand.com/MODApis/Api/v2/Quote/json?symbol=" . $symbol;
$content = file_get_contents($url_moreinfo);
$more_info = json_decode($content);
?>
```

Then we will get the following detail stock information as following. The source code could be found in the following github link.
![](https://farm2.staticflickr.com/1514/25896841566_eefdfacac6.jpg)
- [github code link](https://github.com/spacime/Projects/tree/master/Fetch%20Stock%20Data)
