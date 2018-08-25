---
layout: post
title: "Mapping Tweets by Hashtag with Python"
description: "Introduction to Geoprocessing with Python and the Twitter API"
date: 2017-09-26
tags: ['GIS', 'Python', 'Twitter', 'GeoJSON', 'Tutorial']
comments: true
share: true
---

## It has been a while

 It is not my intention to let this space sink into oblivion. I had hit a bit of a rough patch over the past few weeks. This is what happens whenever I don't have active project on hand to finish or a (reasonable) goal to achieve. However, I finally decided on a project! This happened after thinking back on a CS50 Twitter project and how to apply any mapping to it. The goal for this project was to have a map of tweets that would show the user name and the text that was tweeted out. It was altered to creating a GeoJSON of this data, using just Python (and a library or two), and putting it on Github. If viewed on Github, it automatically maps the points using Mapbox. Like this:

![Imgur](https://i.imgur.com/fVyRWox.png "Imgur"){: .center-image}
Here is a link to the map if you wish to see more, [map](https://github.com/Kjmanion/GeoprocessingPython/blob/master/ch3/tweets.geojson)

This is a GeoJSON file that I pushed to a Github repository. Github will automatically turn any GeoJSON file in a repo into an interactive map. The above map contains tweets that were searched for using the #Terps hashtag. It was after the University of Maryland's football team win over Texas, which was a shock for the football fans among us. Here is what you see when you click one of the pins:

![Imgur2](https://i.imgur.com/NgltaTc.png "Imgur2"){: .center-image}

Of course, you can make a map with searches that include any hashtag. I was only in a festive mood celebrating a rare Terps win over a ranked opponent.  

## Things you will need and Overview
Creating this map requires a few things listed here:

1. A Twitter Application OAuth access token
2. The Tweepy library for Python - to retrieve the tweets
3. The GDAL/OGR Libraries for Python- to create the GeoJSON file.


I broke this project into a few pieces.

1. Gain access to the Twitter API
2. Preparing Tweepy to search for tweets
3. Prep the GeoJSON with whatever information we want to include in addition to where each tweet is
4. Loop through the tweets information and create the GeoJSON with all data included

### Getting Twitter API Credentials

You need to sign up for an OAuth access token to be able to make calls to the Twitter API.

1. While you are signed into Twitter, go to this [Twitter Apps](https://apps.twitter.com/) page and click "Create New App"
2. Fill out the details required by the application and create the application.
3. You will then come to your app's details page. You will want to go to the Keys and Access Tokens tab and make sure you have a
* Consumer Key (API Key)
* Consumer Secret (API Secret)
* Access Token (If not, click on 'create my access token')
* Access Token Secret

You will need these 4 items for our next step

### Preparing to search for tweets

We will be using Python library, Tweepy, to retrieve our tweets. If you do not have Tweepy and have pip installed, type into your terminal
```python
pip install tweepy
```
If you do not have pip installed, go to the [Tweepy site](https://github.com/tweepy/tweepy) to learn how to install around that.

We will now be able to access tweets via python/Tweepy. Be aware that the Twitter API does have a rate limit, 15 requests every 15 minutes. 

Remember those keys that we got from the Twitter site? You will input them in the following places replacing consumer_key with your consumer key and consumer_secret with etc.

```python
auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_secret)

api=tweepy.API(auth)
```

The last line there will allow you to use the Tweepy library features via the api variable. Now we will focus on the search query.

First, we need to know where to search in the world. We can find this value with Tweepy:

```python
places = api.geo_search(query="USA", granularity="country")
print (places[0].id)
```
What you should see printed to the console is "place:96683cc9126741d1". This will be key for our search query to keep our tweets within the USA. You are welcome to input a different country, but make sure you retrieve the unique place value. The search query variable will end up looking like this:

```python
searchQuery = "place:96683cc9126741d1 #terps OR #Terps"
maxTweets = 100
```

Replace '#terps OR #Terps' with whatever hashtag you wish to search for. Now you have all the variables needed to retrieve our tweets in place. The thing we have to do now is prepare a place to put all that data.

### Preparing the GeoJSON to input tweet data to

The OGR/GDAL Python library is something I'm still in the early phase of learning, but already see the potential. GDAL stands for Geospatial Data Abstraction Library. It lets us work with vector and raster data formats within python or other programming languages. Let's create a new GeoJSON file to input some data. We will need access to the OGR library to work with vector data. Here are how to define some of these terms

* Driver- A way of telling the OGR library how to read and write data to that particular format.
* Data Source- A file, such as a shapefile, GeoJSON, or GeoDatabase
* Layer- The data inside the data source. Some formats can contain more than one layer, while others such as shapefiles can not.

Here is how we will create this GeoJSON

1. Create a variable called driver and set it equal to ogr.GetDriverByName('GeoJSON')
2. Create a data source with that driver by setting a variable, ds, to equal driver.CreateDataSource(r'filename.geojson').
3. Create a layer by setting a new variable, lry, to = ds.CreateLayer('layer')

By now, this part of your program should look like this:

```python
driver = ogr.GetDriverByName("GeoJSON")
ds = driver.CreateDataSource(r"filename.geojson")
lyr = ds.CreateLayer("layer")
``` 

We now have a data source with a layer to input data to. Now is the time to create the attribute or fields where that data will go, along with the location data. My example only retrieved the username and the tweet itself, but that is only the tip of the iceberg for data you can get from Tweepy/Twitter. Here is a link to the tweet location metadata [reference](https://developer.twitter.com/en/docs/tweets/data-dictionary/overview/geo-objects.html). 


When we create these new attributes, we will need to know what type of data they are. The latitude and longitude that we get will be Real numbers, while the data for the username and tweet are strings. The syntax for each is listed below.

|Category Name  | Data Type   |   OGR Syntax |
|:---------|:-----------:|:------------:|
|Longitude | Real Number | ogr.OFTReal  |
|Latitude  | Real Number | ogr.OFTReal  |
|UserName  |    String   | ogr.OFTString|
|Tweet     |    String   | ogr.OFTString|

Here is how we will go about creating this table for us:

1. Create a new variable called coord_fld and set it to ogr.FieldDefn('X', ogr.OFTReal)
2. That field will be where you input the data, but we need to set the width(how many characters) and precision (for real numbers). We will set the width of this coord_fld with the method .setWidth(12) and precision with the method .setPrecision(7). Please note that these are generalizations of places and are not meant to be super specific. There is a difference between 36.44368 and 36.4436763. I settled on this limit for simplicity. 
3. The layer variable can now create a new field with the method .CreateField. Use that method like this:
lyr.CreateField(coord_fld). It is now added to the layer.
4. We can keep the same attributes of the 'X' or longitude field and just change the name to, 'Y', with the method .SetName. So we will use it like this, coord_fld.SetName("Y"), and just add it to the lyr with lyr.CreateField(coord_fld). 
5. I created a new variable for the tweets, tweet_fld. tweet_fld = ogr.FieldDefn('tweet', ogr.OFTString)
6. Set the width for the tweet to be 200, only because links and names are not counted as part of the 140 characters anymore. tweet_fld.SetWidth(200). Then add that to the layer with lyr.CreateField(tweet_fld)
7. Do the same as above for the username field, user_name = ogr.FieldDefn('User', ogr.OFTString). user_name.SetWidth(25).

From creating the coord_fld to the user_name variables, your code will look something like this:

```python
coord_fld = ogr.FieldDefn('X', ogr.OFTReal)
coord_fld.SetWidth(12)
coord_fld.SetPrecision(7)
lyr.CreateField(coord_fld)
coord_fld.SetName("Y")
lyr.CreateField(coord_fld)
tweet_fld = ogr.FieldDefn('Tweet', ogr.OFTString)
tweet_fld.SetWidth(200)
lyr.CreateField(tweet_fld)
usr = ogr.FieldDefn('User', ogr.OFTString)
usr.SetWidth(25)
lyr.CreateField(usr)
```

The input is now prepared. Let's go back to the tweets to figure out how to place them.

### Looping through Tweets to input to your GeoJSON

This is the grand finale to most of preparation we have been going through! There are a few more things we need to prepare, but it will be quick.


1. These next variables will be ones I mentioned above in the overview of Tweepy. Variable searchQuery will be which hashtag to search and where. We decided on the U.S. and got the ID from the method above and you will prefix your hashtag search with place:"USAID" or 'place:96683cc9126741d1'. maxTweets and tweetsPerQry are for how many you wish to get from the cursor search. 
2. You can Replace #Terps with whatever hashtag you wish and I kept the number to search at 100.
3. The bottom three lines are preparing variables for the geo part of the program. We're getting the layer definition to be able to output new features that are points in the variable out_feat (output feature.)

```python
searchQuery = 'place:96683cc9126741d1 #Terps OR #terps'
maxTweets = 100
tweetsPerQry = 100

featureDefn = lyr.GetLayerDefn()
out_feat = ogr.Feature(featureDefn)
point = ogr.Geometry(ogr.wkbPoint)
```

We are now ready to interate through tweets and start the search with the cursor object of tweepy as listed on it's [reference documents](http://docs.tweepy.org/en/v3.5.0/cursor_tutorial.html). 

1. Start the loop with tweepy.Cursor and the right parameters that we defined above.
2. We will check to see if each tweet has place metadata to gather the bounding box coordinates from. I created a function to get the averages of latidue and longitude from the 4 points of the bounding box. This equation wouldn't work if it was any other shape, but the box will work.
3. If coordinates exist, it will get the average and place them in a list returned by the function, avgCoordinates, that I created. If you wish to see the function, I will put a link to the script, with omitted OAuth tokens of course.
4. We then use the out_feat variable we created to set fields of new information from the tweet including the coordinates we got from the average function, the actual tweet text, and the user name that tweeted it.
5. We then add a point with the coordinates from the newCoords list.
6. The new feature from this specific tweet will need to be connected to that point and that's what out_feat.SetGeometry(point) does.
7. The last part of the loop is creating a feature from the CreateFeature method of lyr with the input of our new feature as the parameter.


```python
for tweet in tweepy.Cursor(api.search, q=searchQuery).items(maxTweets):
    if tweet.place is not None:
        newCoords = avgCoordinates(tweet.place.bounding_box.coordinates[0])
        myList.append(tweet)
        out_feat.SetField("X", newCoords[1])
        out_feat.SetField("Y", newCoords[0])
        out_feat.SetField("Tweet",  tweet.text)
        out_feat.SetField("User", tweet.user.name)
        point.AddPoint(newCoords[1], newCoords[0])
        out_feat.SetGeometry(point)
        lyr.CreateFeature(out_feat)



```

And that completes our script. You should have an outputted GeoJSON file where your data source was created. 

Here is a link to the repo where the map and script are. [Link](https://github.com/Kjmanion/GeoprocessingPython/tree/master/ch3)

This has been an interesting experience for myself to actually write this tutorial, which is more of a review of what I have been learning in the past few weeks. I hope to have some more things to add in the near future.

### Sources & Further reading

This blog, Dealing Data, has a nice writeup of great detail on the Twitter API and Tweepy python library. These two posts are the ones that helped me the most:

* [Twitter API](http://www.dealingdata.net/2016/07/21/PoGo-Series-TwitterAPI/)
* [Tweepy](http://www.dealingdata.net/2016/07/23/PoGo-Series-Tweepy/)


Currently, I am reading a book on working with Geospatial data in Python. It's called [Geoprocessing with Python](https://www.amazon.com/s/ref=nb_sb_noss?url=search-alias%3Daps&field-keywords=geoprocessing+with+python) and I have found it be quite helpful in automating such tasks as creation of the data. I've only just
