title: Stock Search Android APP
categories:
    - Project
    - Android Develop
tags: [Android, JSON]
date: 2016-05-24

### Introduction
![](https://c1.staticflickr.com/8/7387/27187459336_584bde318b.jpg)

This project allows user to search stock information, such as details, trends and news. When user type in some words, this application will autocomplete and provide related companies for being chosen. Moreover, favorite list stores the companies which user is interested in and user can delete the company form favorite list by slide the item in the list. This project contains lots of basic skills of Android development. It is absolutely a good practice if you are interested.
<!-- more -->
### Automatically Complete
To realize the autocomplete function, the application need to connect with server http://dev.markitondemand.com/MODApis/Api/v2/Lookup/ to search related companies based on user’s input. It will use Android’s AsyncTask class to realize the search function background. After the data is fetched from the server, they would be shown as a list. In this list, we need to create an adapter for the list. The adapter’s XML file and its class should create for this application. The AsyncTask class and List’s adapter will be talked in another two blogs.

![](https://c5.staticflickr.com/8/7662/26614698444_5848b0d960.jpg)

### Stock Details
![](https://c5.staticflickr.com/8/7302/27187459276_16256d841c.jpg)

When user select one element from the autocomplete list and press the “GET QUOTE” button, it will create a new tabbed layout activity. The tabs contain stock’s current detail, history chart and news. It also fetch today’s trend picture on the detail tab. When user click on the picture, it will pop up a dialog which contains the today’s trend picture and user can zoom up and down the picture by multiple touch.

![](https://c4.staticflickr.com/8/7036/26617457123_36e8b40211.jpg)

### Highlight Chart
![](https://c1.staticflickr.com/8/7795/26614698304_b1b9073487.jpg)

This tab shows the trend of the company’s stock. This has to implemented using a WebView using HighCharts API. We need put the HTML file into the assets directory so as to be shown in WebView.
``` java
private void loadHistoricalChartWebView(String stockSymbol) {
        WebView browser = (WebView) findViewById(R.id.webView);
        /* set loading of images */
        browser.getSettings().setLoadsImagesAutomatically(true);
        /* enable JS */
        browser.getSettings().setJavaScriptEnabled(true);
        String url = null;
        try {
            url = "file:///android_asset/historicalChart.html?symbol=" +
                    URLEncoder.encode(stockSymbol, "utf-8");
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }
        browser.loadUrl(url);
    }
```

### News

![](https://c7.staticflickr.com/8/7163/27124658422_6ce91b4e4b.jpg)

In order to get the news about this company, this application also use AsyncTask class to fetch data from server and then show the data in a list when all data are fetched. Similarly, we also to create an adapter for the items in the list.

Share on Facebook
If you like, this application can share company’s stock on Facebook. We will use the Facebook SDK in this application. The detail steps are here: https://developers.facebook.com/docs/sharing/android.


### Favorite List
![](https://c3.staticflickr.com/8/7497/26616152914_c49698b6a4.jpg)

![](https://c5.staticflickr.com/8/7155/26616152964_98c403d068.jpg)

User can click the right corner’s star to add or remove from the favorite list. User also can delete the company from favorite at MainActivity by sliding the items. The slide deleting function is realize as following:
``` java
DynamicListView mDynamicListView = (DynamicListView) findViewById(R.id.favoritesListView);

        mDynamicListView.enableSwipeToDismiss(
                new OnDismissCallback() {
                    @Override
                    public void onDismiss(@NonNull final ViewGroup listView, @NonNull final int[] reverseSortedPositions) {

                        android.app.AlertDialog.Builder whetherDelete = new android.app.AlertDialog.Builder(MainActivity.this);
                        whetherDelete.setTitle("Want to delete");
                        String name = "";
                        for (int position : reverseSortedPositions) {
                            name = adapter.getItem(position).getFavoriteName();
                        }
                        whetherDelete.setTitle("Want to delete " + name + " from favorites?");
                        whetherDelete.setPositiveButton("OK", new DialogInterface.OnClickListener() {
                            @Override
                            public void onClick(DialogInterface dialog, int id) {
                                for (int position : reverseSortedPositions) {
                                    adapter.remove(adapter.getItem(position));
                                }
                            }
                        });
                        whetherDelete.setNegativeButton("CANCLE", new DialogInterface.OnClickListener() {
                            @Override
                            public void onClick(DialogInterface dialog, int which) {
                            }
                        });
                        whetherDelete.show();
                    }
                }
        );
```


### Conclusion
This project contains lots of characteristics of Android development. It contains creating new activity, Asyntask, ListView, REST and Facebook SDK. If you are the first time to touch Android development and want to become familiar with it, you can create a same application above.
-[github code](https://github.com/spacime/Projects/tree/master/Stock%20Search%20Android%20APP)
