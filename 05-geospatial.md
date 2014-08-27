# Geospacial Queries

## Background

* MongoDB has extensive support for Geospatial Queries
* Select geometries within a boundary $geoWithin
* Select geometries that interest $geoIntersects
* Select geometries in proximity to a point on a spehere $nearSphere

## Sample Geo Document

	"location" : {
        "type" : "Point",
        "coordinates" : [ 
            -89.658, 
            25.888
        ]
    },

This is the structure for storing a point in MongoDB. Notice the Longitude comes before the Latitude.

## Sample Index

	db.collection.ensureIndex( { "location" : "2dsphere" } )

If the location field is structured corrected, this will create a special 2dSphere index.

## Demo

### Perform a geospatial point query:

**Query**

	db.teacher.find(
	    {"location" : {
	        "$nearSphere": {
	            "$geometry": {
	                "type": "Point", "coordinates": [-84.5, 28.5]
	            },
	        "$maxDistance": 200000, /* 124.274238 miles */
	        "$uniqueDocs": 1
	        }
	    }
	})

**Result**

	error: {
		"$err" : "Unable to execute query: error processing query: ns=workshop.teacher limit=0 skip=0 Tree: GEONEAR  field=location maxdist=2000 isNearSphere=0 Sort: {} Proj: {} planner returned error: unable to find index for $geoNear query",
		"code" : 17007
	}

An error is returned because we don't have a proper geospatial index.

### Add geospatial index to the location field:

	db.teacher.ensureIndex( { "location" : "2dsphere" } )

### Now try the query again:

**Query**

	db.teacher.find(
	    {"location" : {
	        "$nearSphere": {
	            "$geometry": {
	                "type": "Point", "coordinates": [-84.5, 28.5]
	            },
	        "$maxDistance": 200000, /* 124.274238 miles */
	        "$uniqueDocs": 1
	        }
	    }
	})


**Result**

	3 results, this is the first one.

	{
	    "_id" : ObjectId("51e873dbb199b3cb9318d99d"),
	    "condition" : {
	        "latitude" : 28.5,
	        "longitude" : -84.51699829101562,
	        "date" : ISODate("2013-07-19T21:50:00.000Z"),
	        "wave_height_meter" : 1,
	        "air_temperature_fahrenheit" : 79.33999862670899,
	        "tide_water_level_meter" : null,
	        "wind_speed_milehour" : 17.89552,
	        "sea_level_presssure_hpa" : 1017.200012207031,
	        "station_visibility_nmi" : null,
	        "wind_speed_knots" : 15.55072,
	        "gust_wind_speed_knots" : 19.4384,
	        "tide_water_level_feet" : null,
	        "gust_wind_speed_metersec" : 10,
	        "wave_height_feet" : 3.28084,
	        "average_wave_period_sec" : 3.900000095367432,
	        "pressure_tendency_hpa" : -1,
	        "dewpoint_temperature_celsius" : 22.60000038146973,
	        "wind_direction_degnorth" : 190,
	        "wind_speed_metersec" : 8,
	        "dominant_wave_period_sec" : 5,
	        "dominant_wave_direction_degnorth" : 165,
	        "air_temperature_celsius" : 26.29999923706055,
	        "sea_surface_temperature_celsius" : 27.70000076293945,
	        "sea_surface_temperature_fahrenheit" : 81.86000137329101,
	        "gust_wind_speed_kilohour" : 36,
	        "dewpoint_temperature_fahrenheit" : 72.68000068664551,
	        "wind_speed_kilohour" : 28.8,
	        "gust_wind_speed_milehour" : 22.3694
	    },
	    "image" : "http://www.ndbc.noaa.gov/images/stations/3m.jpg",
	    "image_small" : "http://www.ndbc.noaa.gov/images/stations/3m_mini.jpg",
	    "location" : {
	        "type" : "Point",
	        "coordinates" : [ 
	            -84.517, 
	            28.5
	        ]
	    },
	    "location_desc" : "112 NM WNW of Tampa, FL",
	    "name" : "Station 42036 - West Tampa",
	    "owned" : "National Data Buoy Center",
	    "payload" : "ARES 4.4",
	    "region" : "Gulf Of Mexico",
	    "rss_data" : "http://www.ndbc.noaa.gov/data/latest_obs/42036.rss",
	    "station_id" : "42036",
	    "station_page" : "http://www.ndbc.noaa.gov/station_page.php?station=42036",
	    "station_type" : "3-Meter Discus Buoy",
	    "surface" : "ocean",
	    "txt_data_45" : "http://www.ndbc.noaa.gov/data/realtime2/42036.txt"
	}

Now that the index is is place, we get back documents.

### Look at the explaination

**Query**

	db.teacher.find(
	    {"location" : {
	        "$nearSphere": {
	            "$geometry": {
	                "type": "Point", "coordinates": [-84.5, 28.5]
	            },
	        "$maxDistance": 200000, /* 124.274238 miles */
	        "$uniqueDocs": 1
	        }
	    }
	}).explain()


**Result**

	{
	    "cursor" : "S2NearCursor",
	    "isMultiKey" : false,
	    "n" : 3,
	    "nscannedObjects" : 39,
	    "nscanned" : 39,
	    "nscannedObjectsAllPlans" : 39,
	    "nscannedAllPlans" : 39,
	    "scanAndOrder" : false,
	    "indexOnly" : false,
	    "nYields" : 0,
	    "nChunkSkips" : 0,
	    "millis" : 1,
	    "indexBounds" : {},
	    "server" : "h004520.mongolab.com:59519",
	    "filterSet" : false,
	    "stats" : {
	        "type" : "GEO_NEAR_2DSPHERE",
	        "works" : 39,
	        "yields" : 0,
	        "unyields" : 0,
	        "invalidates" : 0,
	        "advanced" : 3,
	        "needTime" : 0,
	        "needFetch" : 0,
	        "isEOF" : 1,
	        "children" : []
	    }
	}

We can see by explain, a GEO_NEAR_2DSPHERE is taking place and 39 documents were scanned.

## Exercises

**Part A** Add the geospatial point index to your student collection.

	db.student.ensureIndex( { "location" : "2dsphere" } )

**Part B** Perform a query to find documents based on a latitude and longitude.

	db.student.find(
	    {"location" : {
	        "$nearSphere": {
	            "$geometry": {
	                "type": "Point", "coordinates": [-84.5, 28.5]
	            },
	        "$maxDistance": 200000, /* 124.274238 miles */
	        "$uniqueDocs": 1
	        }
	    }
	}).explain()

**Part C** Apply a filter for only surfaces on the ocean to the query to reduce the number of document searched.

	db.student.find(
	    {"surface" : "ocean",
	    "location" : {
	        "$nearSphere": {
	            "$geometry": {
	                "type": "Point", "coordinates": [-84.5, 28.5]
	            },
	        "$maxDistance": 200000, /* 124.274238 miles */
	        "$uniqueDocs": 1
	        }
	    }
	}).explain()


### [Back to: Indexing](04-indexing.md) ###
