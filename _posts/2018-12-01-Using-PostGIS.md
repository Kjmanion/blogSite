---
layout: post
title: "Learning PostgreSQL and PostGIS"
description: "A waltz into the PostGIS world"
date: 2018-11-27
tags: ['PostGIS', 'GIS', 'PostgreSQL']
comments: true
share: true
---

# What was SQL?

I remember sending out resumes after college with SQL listed as a skill. This was probably due to the false sense of acquired knowledge that the use of ArcMap Select by Attribute calculator actually builds up in oneself. It wasn't until later that I learned that the section above the textbox where it says SELECT * FROM 'layer' was also SQL. This was a whole new world that I didn't know exist.

![A 'SQL command' in ArcMAP ](../../images/select.png)

Following that revelation, here is my short history with databases and how I am exercising SQL skills with PostGIS.

## My first introduction to databases and SQL

Harvard's CS50 class, provided by EdX, was really a great introduction to SQL and databases (in addition to programming.) An assigned project was creating a site that sold stocks. A user could create their account, log in, and buy and sell stocks with their funny money of 10,000 that each user was given. Queries were made from multiple tables to return information for a website on a flask project. While I had only put my toes in the water, it was in the adult pool.

## Time passes on

A post on Medium entitled, "Why you should care about PostGIS?" by Topi Tjukanov appeared a few months ago. [Here)](https://medium.com/@tjukanov/why-should-you-care-about-postgis-a-gentle-introduction-to-spatial-databases-9eccd26bc42b) is a link to it. It revealed, to myself, the power of a free and open source tool called PostGIS, that Topi uses to make amazing work. The key thing for me was the Spatial SQL, or part two of his post. One is basically able to combine select by attribute and select by location into one SQL query with PostGIS and PostgreSQL. I got to the, "How to get started section" and was using DB Manager in QGIS, which I had never used before.

The one thing about learning these technologies is that it requires daily practice, much like how one has to practice the piano daily to gain proficiency in it. Unlike how my teacher would assign me exercises or pieces, you have to come up with your own ways of querying data in addition to learning by the book. I follow Topi on twitter and think of something he recently tweeted out where you find the data, but have no idea if you can make something of it until you are playing around with it. 

NOAA has some datasets on their site as shapefiles including hail and tornado reports. I took the tornado reports and thought it would be silly and fun to find only the ones that cross state lines. I popped the data along with a US States shapefile where I found that if I put the two together looked like this:

![Tornadoes in USA ](../../images/statesbefore.png)

This past week consisted of ways to break down the lines to a startpoint and endpoint and fine where they are located. I actually ended up creating a few point tables consising of those points. However, this morning I spent some time seeing if I could actually get the query down to one execution to create a table from the tornadoes consisting of those tornadoes that crossed state lines. Now, I will also add that it took ~15 minutes to run and that is something for myself to work on, but I'm glad I got it down to one query. The table that was produced by the query, when placed in QGIS over the states, looks like this:

![Tornadoes in USA ](../../images/statesafter.png)