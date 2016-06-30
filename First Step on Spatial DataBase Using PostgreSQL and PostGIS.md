title: First Step on Spatial DataBase Using PostgreSQL and PostGIS
categories:
    - Studying Notes
    - Database
tags: [Database, Spatial Database, PostgreSQL]
date: 2016-06-29
---

### Introduction
Spatial database is a database that is optimized to store and query data related to objects in space, including points, lines and polygons. Spatial Database is widely used in different areas, such as medicine and geography. Spatial database is built based on traditional database. Spatial database contains more types and operators. The indexing and storage are designed more easy to process and access from the client end.  In this article, I will find the nearest neighbors and convex hull of several points. In order to show the result clear, I make use of [Keyhole Markup Language]( https://en.wikipedia.org/wiki/Keyhole_Markup_Language) (kml) file to allow the result to be shown on the Google Earth. Traditional SQL and spatial operators are used to get the result. Here, I use the PostgreSQL and PostGIS which could be download from there official website.

![](https://c8.staticflickr.com/8/7099/27911367951_8b6ff25ae2.jpg)

<!-- more -->

### Spatial Database’s Structure
Spatial Database is combined by three parts: interface to application, core, interface to [DBMS]( https://en.wikipedia.org/wiki/Database). The following picture depict clearly the three layer architecture of special database. In this article we will use spatial operators which are provides by the core part. In this graph, we can see that lots of application are built based on spatial database. The details behind this spatial application are hidden from users.

![](https://c8.staticflickr.com/8/7756/27911368031_b0da88ab3c.jpg)

### Preparation for Example
In this article, I will get 9 locations, latitudes and longitudes, in and around USC. Secondly, I will write the latitudes and longitudes into the KML file and load the KML file into Google Earth. Finally, I will create a table which store the 9 locations for the following operators, finding the nearest neighbors form one point and calculating the convex hull of 9 locations.
``` sql
CREATE TABLE Coordinates
(tag CHAR(30) NOT NULL,
longitude DOUBLE PRECISION NOT NULL,
latitude DOUBLE PRECISION NOT NULL,
PRIMARY KEY (tag));

INSERT INTO Coordinates(tag, latitude, longitude)
VALUES
('Apartment', 34.0223055556, -118.294083333),
('Exposition/Vermont', 34.0183611111, -118.291472222),
('Vermont/Jefferson', 34.0253888889, -118.291444444),
('Jefferson/Figueroa', 34.0218888889, -118.280111111),
('Figueroa/Exposition', 34.0183888889, -118.282333333),
('Cromwell Track and Field', 34.0221944444, -118.287583333),
('Leavey Library', 34.0217222222, -118.282944444),
('Tommy Trojan', 34.0205555556, -118.28541666),
('Hoose Library of Philosophy', 34.0188055556, -118.286583333);
```

### Nearest Neighbors
Above I provide the basic information of spatial database and make preparation for our experiences. Right now it’s time for us to find three nearest neighbors from one specified point. The first step is finding the three nearest neighbors using SQL. Then we write the results into the KML file. Finally we local the KML file into Google Earth.
``` sql
SELECT *
FROM (
  SELECT * FROM Coordinates
  WHERE tag != 'Apartment'
) as PointsExpApt
ORDER BY ST_MakePoint(longitude, latitude) <-> (SELECT ST_MakePoint(longitude, latitude)
                                                FROM Coordinates WHERE tag = 'Apartment')
LIMIT 3;
```
In the above SQL, we sort the distances between the specified point and other points which is realize by ORDER and ‘<->’. Then we select the three points which own least distances from specified points. After the load the KML file into Google Earth, it will show like following.

![](https://c4.staticflickr.com/8/7510/27911368171_046b8c2724.jpg)


### Convex Hull for Locations
Like the previous experience, we also need use SQL to find the points which form the convex hull. Then we write the data into KML file and load the file into Google Earth.
``` sql
SELECT ST_AsText(
ST_ConvexHull(ST_collect(points.geom)))
FROM ( SELECT ST_MakePoint(longitude,latitude) as geom FROM Coordinates) as points;
```
The above SQL code use the PostGIS’s operation, ST_ConvexHull, to the get points which form the convex hull. The parameter is the set the points which are needed to be included and are select form the table. The final result shown in Google Earth is following.

![](https://c3.staticflickr.com/8/7354/27709266730_464ea6e868.jpg)

### Conclusion
There are lots of other functions are provided to deal with special operation, such as topological operators and distance operators. In order to facilitate to get the special items, spatial database also makes use of other indexing methods, such as [R trees](https://en.wikipedia.org/wiki/R-tree) and [K-D-B](https://en.wikipedia.org/wiki/K-D-B-tree) trees, which are more suitable to spatial items.

-[github code]( https://github.com/spacime/Projects/tree/master/Database/Spatial%20Database)
