title: Stock Search Android APP – AsyncTask
categories:
    - Project
    - Android Develop
tags: [Android, JSON, AsyncTask]
date: 2016-05-27
---

### Introduction
Previously I have developed a Web application, and now I will realize the same functions on an Android application. This Android application can be used to search companies’ stock information and realize some useful tools. Many functions in this application are required to fetch data from the webserver. In order to simplify the application design, Android provides developer with AsyncTask class to divide the user interface with background process. Precisely, in this application the user interface’s task is showing lists or charts based on data and the background task is to fetch data from the server. In other words, the user interface will show up until the data arrives when the background task is finished and then show for the user.

![](https://c4.staticflickr.com/8/7397/26683716203_c5d7c8b5a7.jpg)

<!-- more -->

![](https://c5.staticflickr.com/8/7302/27187459276_16256d841c.jpg)


### The Structure of AsyncTask Class
AsyncTask is an abstract class provided by Android which helps us to use the UI thread properly. This class allows us to perform long/background operations and show its result on the UI thread without having to manipulate threads.  
*	doInBackground: Code performing long running operation goes in this method. When onClick method is executed on click of button, it calls execute method which accepts parameters and automatically calls doInBackground method with the parameters passed.
*	onPostExecute: This method is called after doInBackground method completes processing. Result from doInBackground is passed to this method.
*	onPreExecute: This method is called before doInBackground method is called.
*	onProgressUpdate: This method is invoked by calling publishProgress anytime
from doInBackground call this method.

![](https://c2.staticflickr.com/8/7187/26683719193_6e1f860c1a.jpg)

### Create the AsyncTask class
Specifically, here I will provide a sample function to make use of AsyncTask class so as to explain it clearly. The process is that the application fetch the data from server with a parameter, the company’s Stock Ticker Symbol and then show the details in a list for the user. The first step is to create a new class extend from AsyncTask class and then create an object based on this class when you need to show the data. In this application, I put the creation of object on onCreate function of the activity.
``` java
// Create an new object when needed
new StockDetailsFragmentFiller().execute(stockSymbol);
```
``` java
// Design a class extending from AsyncTask
class StockDetailsFragmentFiller extends AsyncTask<String, String, String> {
…
}
```
### doInBackground Period
The function doInBackground will be called when the object is created based on AsyncTask class. In This function, we get the data based on the url and the format of the data is JSON, which is a simple and easy to parse. After getting the JSON file, we parse it into a JSON for the future use.
```java
        @Override
        protected String doInBackground(String... key) {
            companySymbol = key[0];
            StringBuilder sb = new StringBuilder();
            String json_string = null;
            int val;

            try {
                /* ------------------ Loading string from server content ------------------------ */
                URL url = new URL("http://level-oxygen-127003.appspot.com/index.php?symbol=" + companySymbol);
                urlConnection = (HttpURLConnection) url.openConnection();
                InputStream in = new BufferedInputStream(urlConnection.getInputStream());

                BufferedReader reader = new BufferedReader(new InputStreamReader(in));

                String line;
                while ((line = reader.readLine()) != null) {
                    sb.append(line);
                }
                json_string = sb.toString();

                /*Log.d("Info", result);*/
                /* ------------- Finished. String fully loaded from server response ------------- */
                Log.d("Result", sb.toString());

                /* We receive a JSON object (not a JSON array), so we should create a JSONObject */
                JSONObject resultObject = new JSONObject(json_string);
	   ….
            } catch (Exception e) {
                Log.w("Error", e.getMessage());
            } finally {
                urlConnection.disconnect();
            }
            return null;
        }
```

### onPostExecute Period
The onPostExecute function will be called the doInBackground finish all its statements. In this function, we need to create an adapter based on the JSON object we created in the doInBackground function for ListView. After that we get a list by findViewById method and put the adapter into the list. Finally, all the information could be seen by the user. The customize adapter for the list will be discussed in another article.
```java
       @Override
        protected void onPostExecute(String result) {
//            adapter = new StockDetailAdapter(ResultActivityNew.this, entries);
            /* populate Stock Details ListView */
                    /* Build Adapter */
            adapter = new StockDetailAdapter(ResultActivityNew.this, entries);
            populateStockDetailsListView(stockSymbol);

        }
```
### Conclusion
AsyncTask class provides developers with a method to deal with the threads of user interface and background data process separately, which dramatically reduces the workload of programmers.

-[github code](https://github.com/spacime/Projects/tree/master/Stock%20Search%20Android%20APP)
