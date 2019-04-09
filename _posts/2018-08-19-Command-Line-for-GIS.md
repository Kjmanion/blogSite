---
layout: post
title: "Learning the Command Line as a GIS Professional"
description: "The value of learning to use the CLI as a GIS person"
date: 2018-08-19
tags: ['Command Line', 'GIS', 'General Technology']
comments: true
share: true
---

# Why the Command Line for GIS?

I had been looking for a general topic to write about for some time. This period coincided with being asked to give a presentation to employees at work who have little Python or any programming experience. I told them to join the club as one who doesn't have much experience in Python (but am changing that.) However, it was that this point that I thought of a developer developing without experience in using the command line. There are so many tools in the CLI that it would be strange to meet a developer who doesn't use the CLI.

You're likely at this point saying,"But Kevin, GIS professionals are not developers and there is no need for them to use the command line." My response for this would be that the command line can be another tool in the bat-utility belt of GIS professionals. It wasn't until recently that I became aware of such tools that are created for GIS. One more reason is that the, 'IS' in, 'GIS' usually stands for Information Systems, so we should be expected to be into technology, if not the general area of expertise we use GIS for. I have to credit the blog post [here](http://blog.geomusings.com/2013/01/30/yes-you-need-to-code/), for expressing that second reason better than I can. It's a bit dated, it still applies, probably more so now than then.

### What is the Command Line?

The command line is a way to interact with files without the use of the GUI that your operating system provides. It's essentially a text-based windows explorer, which, like text-based games, can feel a bit confusing the first few times you use it. Those of us who are used to pointing and clicking can especially feel lost. Well friend, I am here to alleviate those worries. I used to be someone who shunned anything have to do with development, command line, etc, but have gotten used to use the command line every day in my learnings. 

### Why use the Command Line?

The main reason for this are the tools that can be used in the CLI, or command line interface. These are functions that you may not have use for everyday, but come in handy in the right situation. I'll give some examples both recent and not so recent. When I was learning Web Development, I quickly got tired of refreshing a html page everytime I changed the html, css, or javascript. I found a command line tool to udr called browser-sync. It automatically refreshed the page in the browser everytime I made a change to the script. It was very convenient and it was run through the command line. 

A few weeks ago I had a bunch of photos I wanted to be made into a point layer. Their placement would be based on their coordinates, as they were geotagged on the phone they were taken on. These coordinates were located in the properties of the file. It looked like a python script was in my future, but I found a command line tool that works with images called exiftool. I naviagated to the folder of photos then ran the tool to export all attributes for every photo in the folder to a CSV. A task that would have taken sometime to copy and paste the data from propeties or write a script was only one line in the CLI.

### GIS Tools: ogrinfo

If you quickly want information from a shapefile or any other geo-type file, one can use ogrinfo. Ogrinfo will even give you the coordinates of every vertex that is part of a polygon. I usually turn off the geometry if I am listing all features in the layer. Here is an example of a command input to a shapefile to gather all metadata about the shapefile, HS_Districts.shp. I am currently in the directory where this shapefile is. I ran this command like this:
```
ogrinfo -so HS_Districts.shp HS_Districts
```
![parameters put out by above command](../../images/ogrinfo_CLI.png)

The '-so' is a flag for the tool that says to the tool to give a summary only of the file. It will give you things like the projection the file is in, coordinates of extents, and other information. If I wanted to list all features, I would use the flag, '-al'. To avoid my terminal becoming overrun with geometry information, I also input the flag, '-GEOM=NO'. Putting this command in with the -al and GEOM=NO gives me:
```
ogrinfo -al -GEOM=NO HS_Districts.shp HS_Districts
```
or like this:

![listings of the features](../../images/ogrinfo_AL.png)

There are many ways to explore your data and it helps to be aware of what ogrinfo can do for a GIS user. I only recently became aware of these tools so I'm still learning them as well.

### GIS Tools: ogr2ogr

ogr2ogr is another helpful tool if you ever wish to change the type of file a layer is to another. Let's say I am making a webmap of sports fields in Anne Arundel county. The data is in a shapefile format, but instead of going into Arc or QGIS, I can use ogr2ogr to convert the shapefile to a GeoJSON like so:

```
ogr2ogr -f 'GeoJSON' sport_fields.geojson sport_fields.shp
```
or like so:

![ogr2ogr conversion command](../../images/ogr2ogr_geoJSon.png)

Very nice to only need write a simple line into the tool for what would be a few clicks in a GUI program.



### Wrapping it up

This just goes back to a tool is a tool and it's up to you on how you use it. I am still new to these tools but am learning that the CLI is an invaluable tool. If you're wondering on how to learn the command line, there are plenty of tutorials on the web, some of which I'll link below.

### Resources to learn Command Line

#### General Command Line

- [Learn Command Line the Hard Way (my first tutorial into CLI)](https://learnpythonthehardway.org/book/appendixa.html)

#### GIS Tools
- [The ogrinfo site on gdal.org](https://www.gdal.org/ogrinfo.html)
- [ogr2ogr cheat sheet on bostongis.com](https://www.bostongis.com/PrinterFriendly.aspx?content_name=ogr_cheatsheet) 

