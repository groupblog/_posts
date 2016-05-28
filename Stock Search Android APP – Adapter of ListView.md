title: Stock Search Android APP – Adapter of ListView
categories:
    - Project
    - Android Develop
tags: [Android, JSON, ListView, Adapter]
date: 2016-05-28
---

### Introduction
In the stock search application, a lot of information, such as stock details, news and favorite list, are shown in a ListView class. Android SDK provides user a wise mechanism for developers to customize their own list view with litter efforts. Android SDK is good at dividing a tool into reasonable parts and SDK provide a default configuration to combine various parts into a tool. What the developers need to do to only to change parts of it and make use of others to realize their design. In this article, I will show you how to design a news ListView of a company. In the end, I also gives you an example how to configure the ListView itself to implement some interesting characters into the ListView.

![](https://c3.staticflickr.com/8/7655/27217925842_9344290bb1.jpg)


<!-- more -->

####  Mechanism of Adapter
With the help of adapter’s mechanism, the designs of user interface and data managements can be done separately. The following is my design steps:
-	Design a data entity class, and then create an ArrayList to store the entities. The entity is one of the items in the ListView.
-	Create an XML file. This XML file is what on item in the ListView looks like. At the same the data in the XML is same as the data in the entity class.
-	Create an adapter class with input of the XML file and entity class.
-	Find the Listview and make use of the adapter class as input. Then we can see the ListView in our application.
Entity Class
Each entity contains some information. For example, the entity in the news ListView contains the title, basic information, posted date and publisher. The class is as following:
``` java
public class NewsEntry {
    private final String title;
    private final String content;
    private final String publisher;
    private final String date;
private final String url;

    public NewsEntry(final String title, final String content,
                     final String publisher, final String date, final String url) {
        this.title = title;
        this.content = content;
        this.publisher = publisher;
        this.date = date;
        this.url = url;
    }
    public String getNewsTitle() { return title; }

    public String getNewsContent() { return content; }

    public String getNewsPublisher() { return publisher; }

    public String getNewsDate() { return date; }

    public String getNewsUrl() { return url; }
}
```

Then we need to create an ArrayList of entity class. In this application, the ArrayList get data when the method, doInBackground, is called in the Asyntask class (detail information in another article). After this all the data are fetched and stored in the ArrayList.

``` java
private List<NewsEntry> newsListEntries = new ArrayList<NewsEntry>();
JSONObject row = array.getJSONObject(i);

newsListEntries.add(new NewsEntry(row.getString("Title"),
                                  row.getString("Description"),
                                  "Publisher: " + row.getString("Source"),
                                  "Date: " + row.getString("Date"),
                                  row.getString("Url")));
```

### XML Design for User Interface

In order to create an adapter object we need two input parameters, ArrayList and XML file. Before we create our own adapter, we need to design how the entity looks like in the ListView.

``` XML
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:text="Large Text"
        android:id="@+id/newsTitleText"
        android:textStyle="bold"
        android:autoLink="web"
        android:linksClickable="true"
        android:textColor="#000000"
        android:layout_marginTop="5dp" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:text="Medium Text"
        android:id="@+id/newsContentText" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceSmall"
        android:text="Small Text"
        android:id="@+id/newsPublisherText"
        android:layout_marginTop="5dp" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceSmall"
        android:text="Small Text"
        android:id="@+id/newsDateText"
        android:layout_marginTop="5dp" />
</LinearLayout>
```

### Adapter Class
After we get the needed input XML file and the ArrayList of data, we can combine these two parameters into an adapter. The adapter class’s task is to put the entity’s data into the position of XML file. The first step is to get the element from XML file and then put the data into this element. To be specify, some information formats are needed to transfer if your application are required. For example, this application need change the time stamp into readable format.
``` java
public class NewsEntryAdapter extends ArrayAdapter<NewsEntry>{
    public NewsEntryAdapter(Context context, List<NewsEntry> items) {
        super(context, R.layout.custom_news_list_item, items);
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        LayoutInflater inflater = LayoutInflater.from(getContext());
        View customView = inflater.inflate(R.layout.custom_news_list_item, parent, false);

        NewsEntry singleItem = getItem(position);
        TextView newsTitleText = (TextView) customView.findViewById(R.id.newsTitleText);
        TextView newsContentText = (TextView) customView.findViewById(R.id.newsContentText);
        TextView newsPublisherText = (TextView) customView.findViewById(R.id.newsPublisherText);
        TextView newsDateText = (TextView) customView.findViewById(R.id.newsDateText);

        newsTitleText.setText(Html.fromHtml("<a href=\" " + singleItem.getNewsUrl() + " \">" +
                singleItem.getNewsTitle() + "</a>"));
        newsTitleText.setAutoLinkMask(Linkify.WEB_URLS); /* linkify web urls inside TextView */
        newsTitleText.setLinkTextColor(Color.parseColor("#000000")); /*set link color to black*/

        newsContentText.setText(singleItem.getNewsContent());
        newsPublisherText.setText(singleItem.getNewsPublisher());
        String dataString = singleItem.getNewsDate();
        dataString = dataString.substring(5, dataString.length());
        dataString = dataString.trim();
        SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss'Z'");

        ...

        return customView;
    }
}
```

### Implement of Adapter Class
Completing the above tasks, putting the data into entity’s ArrayList, designing the view of entity and the design of adapter class, we need to create an object based on the class.

``` java
private ArrayAdapter<NewsEntry> adapter2;
ListView list = (ListView) findViewById(R.id.newsListView);
list.setAdapter(adapter2);
```
After finish running the above codes, the list can be seen by users.

![](https://c7.staticflickr.com/8/7163/27124658422_6ce91b4e4b.jpg)

### Customize ListView
Every class could be rewrite to realize some characteristics on the ListView. In this application, we will realize that when we slide the entity in the ListView we can delete the entity.
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
                    adapter.remove(adapter.getItem(position)) ;
                }
            }
        });
		...

        }
});
```

The DynamicListView is designed by [Niek Haarman]( https://github.com/nhaarman), at the repository is at [ListViewAnimations]( https://github.com/nhaarman/ListViewAnimations). We also can design our own ListView basic on the given ListView class. Niek gives us a great example to show how to extent.

``` java
public class DynamicListView extends ListView {

    @NonNull
    private final MyOnScrollListener mMyOnScrollListener;

    /**
     * The {@link com.nhaarman.listviewanimations.itemmanipulation.dragdrop.DragAndDropHandler}
     * that will handle drag and drop functionality, if set.
     */
    @Nullable
    private DragAndDropHandler mDragAndDropHandler;

    /**
     * The {@link com.nhaarman.listviewanimations.itemmanipulation.swipedismiss.SwipeTouchListener}
     * that will handle swipe movement functionality, if set.
     */
    @Nullable
    private SwipeTouchListener mSwipeTouchListener;

    /**
     * The {@link com.nhaarman.listviewanimations.itemmanipulation.TouchEventHandler}
     * that is currently actively consuming {@code MotionEvent}s.
     */
    @Nullable
    private TouchEventHandler mCurrentHandlingTouchEventHandler;

    /**
     * The {@link com.nhaarman.listviewanimations.itemmanipulation.animateaddition.AnimateAdditionAdapter}
     * that is possibly set to animate insertions.
     */
    @Nullable
    private AnimateAdditionAdapter<Object> mAnimateAdditionAdapter;

    @Nullable
    private SwipeUndoAdapter mSwipeUndoAdapter;

    public DynamicListView(@NonNull final Context context) {
        this(context, null);
    }

    public DynamicListView(@NonNull final Context context, @Nullable final AttributeSet attrs) {
        //noinspection HardCodedStringLiteral
        this(context, attrs, Resources.getSystem().getIdentifier("listViewStyle", "attr", "android"));
    }
…
}
```

### Conclusion
All in all, Android SDK allows developers to customize their ListView with freedom. We not only could configure the entity, but also could revise the ListView itself. For instance, as described in the second to last section, we can realize some animation to the ListView. Moreover, the design philosophy of Android SDk allows developers to customize other tools too. It is just like building blocks, where you can create lots of things in your mind and it is a funny experience.

-[github code](https://github.com/spacime/Projects/tree/master/Stock%20Search%20Android%20APP)
