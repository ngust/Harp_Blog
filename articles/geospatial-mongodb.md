## Working with Geospatial Data in MongoDB
<!-- more -->
MongoDB is NoSQL database that is rapidly gaining popularity.  All data is stored in JSON which works awesome on Node.js.  MongoDB is capable of geospatial queries when set up properly.
<!-- more -->

![large](/img/MongoMaps/map-coffee.jpg)

A few weeks ago I started a web mapping project with a partner.  We are building a web app that displays government mining information on a cool map.  This project is being built with Node.js and Mongodb.

Our data comes from the government of British Columbia and comes in the form of a shapefile.  Unless you have experience with GIS you probably haven't worked much with shapefiles.  They are a data format developed by ESRI, the makers of ArcGIS.  

Shapefiles are a vector file format meaning that they contain information that describes points, lines or polygons.  As opposed to raster data such as aerial photos, or terrain.They also contain a database.

![large](/img/MongoMaps/tablet-GIS.jpg)

Naturally mongo does not recognize a shapefile directly.  We need to convert it into GeoJSON, which is a form of JSON for GIS data.  There is a linux utility that can achieve this via the command line called ogr2ogr.

    ogr2ogr -f GeoJSON -t_srs "EPSG:4326" -geomfield geom  -nlt POLYGON -dim 2 2dclaims_satPM.geojson *.shp

There is a lot going on with this command.  This tool is capable of translating several different formats, we need to tell it exactly what to do.

The `-f GeoJSON` option tells ogr to output the data in the GeoJSON format.

The next one is a little more complicated.  Digital map data has several properties that are needed to use map data correctly.  The projection is the way that the 3D earth is displayed on a 2D map.  The earth is approximately a sphere, to put that into a map you essentially peel the surface like an orange and lay it flat.  The projection is the orange peel.  

![large](/img/MongoMaps/peel.png)

Different map projections can retain certain features of the true 3D earth, but none can preserve all.  Those features are Conformality (shapes), Distance, Area, and Direction.  Our source data has the projection "NAD_1983_BC_Environment_Albers" which uses a metric coordinate system.

A shapefile actually consists of four files with different extensions.  They are .shx, .shp, .prj and .dbf.  THe .prj file contains the projection information.  Here's the .prj file from my data:

    PROJCS["NAD_1983_BC_Environment_Albers",
    GEOGCS["GCS_North_American_1983",  
    DATUM["D_North_American_1983",
    SPHEROID["GRS_1980",6378137  .0,298.257222101]],PRIMEM["Greenwich",0.0],UNIT["Degree",  0.0174532925199433]],
    PROJECTION["Albers"],  
    PARAMETER["False_Easting",1000000.0], PARAMETER["False_Northing",0.0], PARAMETER["Central_Meridian",-126.0],  PARAMETER["Standard_Parallel_1",50.0],  PARAMETER["Standard_Parallel_2",58.5],  PARAMETER["Latitude_Of_Origin",45.0],
    UNIT["Meter",1.0],  
    AUTHORITY["EPSG",3005"]]

The last part `AUTHORITY["EPSG",3005"]]` give us the SRID which is a 4-digit code that defines the map parameters.  The standard for web maps is "EPSG:4326" which uses Lat/Lon coordinates and the WGS84 datum.  In our org2ogr command above this option defines the SRID, `-t_srs "EPSG:4326"`.

`-geomfield geom ` tells org which column in the database holds the geometry data.  `-nlt POLYGON` sets the data format to polygon.

`-dim 2` sets the data to 2D.  That took me a while to figure out, MongoDB does not support 3D data.  This options will remove the Z-axis from your data.

The next two parameters define the output file name and input file, `2dclaims_satPM.geojson` and `*.shp` respectively.

One you have created your GeoJSON file it can be imported into MongoDB.  To do that you run the following command, from the terminal (not from the mongo console):

    mongoimport --db claims --collection docs --file 2dclaims_satPM.geojson

That will create the collection "docs" within the database named "claims".  You need to create the database first.  You can create it by running this from the mongo console:

    use claims

Great, so now the data is loaded into mongo.  We can query the database and return a result including the geometry.  In our case the file has 41,000 records.  Here is an example of what one looks like in the database:

    {
      "_id" : ObjectId("56d2a20a1c601e547e112e0b"),
      "type" : "Feature",
      "properties" : {
        "OWNER_NAME" : "GUST, NICHOLAS",
        "OBJECTID" : 39472,
        "CLAIM_NAME" : "AU GROUP",
        "CLIENTNUM" : 278107,
        "ISSUE_DATE" : "20141002233529",
      },
      "geometry" : {
        "type" : "Polygon",
        "coordinates" : [
          [[-120.74498469014414,49.42069474093248],
              [-120.74498449094528,49.41652804563313],
              [-120.75123475545085,49.41652783718137],
            [-120.75123493680732,49.420694540352734],
            [-120.74498469014414,49.42069474093248]
          ]]
      }
    }

At this point we have a functional geospatial database that can be used in a mapping application.  Here is a shot of MongoDB in action in our Node.js app.

![large](/img/sites/MCM2.png)

There are a couple more steps to reach Mongo's full potential with map data.  Mongo needs an index to allow searches based on location.  This is a special geospatial index.  The command for that is run from mongo's console.  From the command line you just run `mongo`.

    $ mongo
    MongoDB shell version: 2.6.10
    connecting to: test
    > use claims
    switched to db claims
    > 

To create the 2dsphere index run this command:

    db.docs.ensureIndex( { "coordinates" : "2dsphere" } );

You should get an output that looks like this.

    {
      "createdCollectionAutomatically" : true,
      "numIndexesBefore" : 1,
      "numIndexesAfter" : 2,
      "ok" : 1
    }

If "ok" is not 1, something is wrong.  In our case it's all good.  Now we have a fully set up geospatial MongoDB database ready for anything.

I'll give you an example of geospatial query in mongo.

    db.docs4.find({$and: [{'properties.CLIENTNUM': 278107}, {
     "geometry": {
       $geoWithin: {
          $geometry: {
             type : "Polygon" ,
             coordinates: [[
             [-121.57814025878906, 49.651181832527826 ], 
             [ -121.57814025878906, 49.80652967700498 ], 
             [ 121.28734588623047, 49.80652967700498 ], 
             [ -121.28734588623047, 49.651181832527826],
             [ -121.57814025878906, 49.651181832527826 ]
              ]]
             }
           }
          }
         }
       ]
      }
    )

$geoWithin will find anything that matches the search criteria inside the defined polygon.  In this case we are looking for records where CLIENTNUM = 278107.  You have to define the polygon for the search.  It is important that the first and last coordinate pairs are the same.  That closes the polygon.  You can create these searches dynamically in javascript but that is anouther topic.

Check out the MongoDB docs for more info on geopspatial queries, [MongoDB Docs](https://docs.mongodb.org/manual/reference/operator/query-geospatial/).
