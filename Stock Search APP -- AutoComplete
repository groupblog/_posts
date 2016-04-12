title: Stock Search App -- AutoComplete
categories:
    - Project
    - Web Develop
tags: [PHP, JSON, jQuery, AJAX]
date: 2016-04-11
---

![](https://farm2.staticflickr.com/1693/26393556095_c31c115dc0.jpg)

### Introduction
During developing the Stock Search App on the cloud server (AWS), in order to make out application be used conveniently the input will autocomplete based on the users’ input. This function can be divided into two parts, the back-end and front-end. Firstly, with the help of Ajax, the input of user will send to the server and server will fetch the information for user synchronously. This process is hidden to users. Secondly, when the front end get the data from the server, jQuery UI will show these data as a prompting list so that user can choose what they want to search. During the process, the user haven’t to press any button and the prompting message will change as the change of users’ inputs. Users can’t feel the process of data transmission between local and server. It is just like they run an application locally which is a great benefit of Ajax.
<!-- more -->
### jQuery UI realize the autocomplete front-end
Fortunately, jQuery provide a user interface to realize the autocomplete prompting list. In the [official website]( http://jqueryui.com/), it provides some [examples](https://jqueryui.com/autocomplete/#default). At the same time, we need to add following libraries, including CSS and javascript, to our application.
``` html
  <link rel="stylesheet" href="//code.jquery.com/ui/1.11.4/themes/smoothness/jquery-ui.css">
  <script src="//code.jquery.com/jquery-1.10.2.js"></script>
  <script src="//code.jquery.com/ui/1.11.4/jquery-ui.js"></script>
```
Firstly, we build an array in our application. We can get the input element based on the input textbox’s ID. When we add the jQuery-UI library, the input textbox will have the autocomplete method. In the method there is a source variable, and then we can set the source as the array what we just create before. Then the input will autocomplete based on the users’ input and the array we create. For example, when the user type j in the textbox, the autocomplete function will search the string star with ‘j’ from the array. When it get a list, then it will show this list for us.
``` javascript
<script>
  $(function() {
    var availableTags = [
      "ActionScript",
      "AppleScript",
      "Asp",
      "BASIC",
      "C",
      "C++",
      "Clojure",
      "COBOL",
      "ColdFusion",
      "Erlang",
      "Fortran",
      "Groovy",
      "Haskell",
      "Java",
      "JavaScript",
      "Lisp",
      "Perl",
      "PHP",
      "Python",
      "Ruby",
      "Scala",
      "Scheme"
    ];
    $("#tags" ).autocomplete({
      source: availableTags
    });
  });
  </script>
```
The code above is sample and clean and it satisfy our need of front end. However, it just can autocomplete the user’s input based on the array which we create before. Actually, it is impossible for us to create an array to autocomplete user’s input during our application. The amount of company is so large, so we need to fetch the useful data from the stock server instead create a static array in our application. In the following section, I will talks about how we can get information from the server.

### Autocomplete Based on Users’ Input
In our application, what we face is a huge amount of data so we cannot create a static array to realize automatically as before.
``` javascript
$("#search_query").autocomplete({
            source: function (request, response) {

                $.ajax({
                    url: "search.php",
                    data: {input: request.term},
                    type: "GET",
                    datatype: "json",
                    success: function (json) {
                        var jsonObject = jQuery.parseJSON(json);

                        if (jsonObject.length == 0) {
                            document.getElementById("checkValidate").innerHTML = "Select a valid entry";

                            response(null);

                        }
                        else {
                            document.getElementById("checkValidate").innerHTML = "";

                            var data = new Array();

                            for (var i = 0; i < jsonObject.length; i++) {
                                data[i] = {
                                    label: jsonObject[i].Symbol + " - " + jsonObject[i].Name + " ( " + jsonObject[i].Exchange + " ) ",
                                    value: jsonObject[i].Symbol
                                };
                            }
                            response(data);

                        }
                    }
                });
            }
        });
```
The search.php as followint
``` php
<?php
if(isset($_GET["input"])) {
    $autocomplete_check_search = "http://dev.markitondemand.com/MODApis/Api/v2/Lookup/json?input=".$_GET["input"];
    $json_content = file_get_contents($autocomplete_check_search);
    echo $json_content;
}
?>
```
### Conclusion
In this task, we use the Ajax to fetch data from server based on users’ inputs without the observation of users’. Then with the help of the jQuery UI, we can show the list for the user conveniently. With the help of jQuery UI our code is more clean and easy to manipulate. And with the help of Ajax, we can divide the web user development with the server function development. And we can keep our servers code save because the user cannot see server’s code.

-[github code]( https://github.com/spacime/Projects/tree/master/Search%20Stock%20Data%20--%20Ajax)
