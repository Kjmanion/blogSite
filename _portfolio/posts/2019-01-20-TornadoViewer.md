---
layout: post
title: "Tornado Data Viewer with Flask, PostgreSQL, and PostGIS"
description: "An exercise in learning these technologies"
date: 2019-01-20
tags: ['PostGIS', 'GIS', 'PostgreSQL', 'Flask', 'Python', 'Databases', 'AWS']
comments: false
share: true
---


## What it does

~~If you go to the app's [page](), it looks something like this:~~

![Tornado Data Viewer Site](../../../images/appPic.JPG)

This application allows a viewer to set where and when they want to view tornado paths. There are two ways of querying the data with hopes to add more. The first is to select the state where tornado paths cross and set the the time frame (in years) of when the tornados occured. For example, you could choose to look at tornados in Kansas from 1960 to 1980.  The second way is to type in a city name and select the state. After doing that, the application will return all tornados within the dataset that crossed within 10 miles of that city. If you want to try a new query with different years for a state, you can use the clear map button. This will remove all the data off the map so new queries can be clearly seen.


## The Genesis of this project

My experience with building web applications is limited to my experience with CS50 and their flask projects. These projects were great for dipping one's toes into the Python web waters. However, it had been so long since I had worked on these projects that I felt I needed to start again from the beginning. Another consideration was that it must include geographical data. If I want to learn PostGIS, then it can't be avoided. I eventually found a tornado dataset on NOAA'a website, which can be found [here](https://www.spc.noaa.gov/gis/svrgis/). (Note: The application is not running right now, hence why the link above has been removed) I used shp2pgsql to get the data into a PG database and put that into a AWS RDS. The flask application itself is on a EC 2 instance, built by AWS Elastic beanstalk, where I uploaded a zip of the application. The zip also included a text file of libraries required to run the application for the EC 2 instance.

I took a slow and steady approach to this project. The work started in October. This was a very generalist project, as I needed to learn how to create a Flask application again, write basic PostGIS queries, and Javascript. It can be thought of as another ToDo app.


## Things to Improve 

1/20/19

Design is not something I have experience in, but can already tell that the interface is not the most intuitive. There are two submit buttons, one for each path described above, when one would suffice.  The 'clear map' button should probably be more distinctive and closer to the map.

## Links referring to this project:

I listed it above, but the project can be found [here](http://flasktornado-env.mkvky3wwhm.us-east-1.elasticbeanstalk.com/)

The github repository can be found [here](https://github.com/Kjmanion/retire)
Try not to notice the repository name.