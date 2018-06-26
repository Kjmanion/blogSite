---
layout: post
title: "Selecting by Location with ArcPy"
description: "Continuing the automated map creation with some selections by location"
date: 2018-06-26
tags: ['ArcPy', 'GIS', 'Python']
comments: true
share: true
---

# Continuing with ArcPy

This is a continuation of my revelatory (to myself) ability to automate map production with the mapping module of ArcPy. These are all basic functions in ArcMap, but one must start with the basics! This will be a post based on a problem that can be solved using ArcMap, but can be automated much quicker with ArcPy. A Select By Location is as basic as they come, but getting to automate it (for a problem that doesn't exist) still felt good.

## The Problem

I have a point layer of all child care facilities in the county. I want to make a map of each one in a layout I have created before hand. However, I also want to know which type of zoning each resides in first. (As I said before, this doesn't really solve a problem, but it does let us use a select by location query.) My solution, will be to iterate through each point, do a select by location of the zoning polygon layer to select the zone where that point resides, and then print a map with that data on it. Essentially, here is the code:

```python
POI = arcpy.MakeFeatureLayer_management("C:\\Users\\Kevin\\Desktop\\StateProjects\\arcgisProFiles\\AA_POI.shp", "POI",  "\"CATEGORY\" LIKE '%CHILD%'")
zoning = arcpy.MakeFeatureLayer_management("C:\\Users\\Kevin\\Desktop\\StateProjects\\arcgisProFiles\\aaco_zoning_adopted.shp", "zones")
cursor = arcpy.da.SearchCursor(POI, ["FID", "feature_na", "CITY", "SHAPE@"])
careMaps = []
for row in cursor:
    newData = []
    pt_lyr = arcpy.MakeFeatureLayer_management("C:\\Users\\Kevin\\Desktop\\StateProjects\\arcgisProFiles\\AA_POI.shp", "pt_lyr",  "\"FID\"={}".format(str(row[0])))
    arcpy.SelectLayerByLocation_management('zones', "CONTAINS", 'pt_lyr', "", "NEW_SELECTION")
    cursor2 = arcpy.da.SearchCursor('zones', ["FID", "TYPE"])
    for type in cursor2:
        newData.append(type[1])
    newData.append(row[1])
    newData.append(row[2])
    careMaps.append(newData)
```

The cursor the for loop is iterating through is the layer of child care centers. The key lines within the for loop is the creation of the individual point that the loop is iterating through, and the select by location query.  The pt_lyr creation is necessary to do a select by location on the one point, and not the entirety of the child care center dataset. This was something I picked off stack exchange. It may not be pretty, but it works. 

The Select by location is key to get the necessary data out of the layer. The first parameter, refering to the zones feature layer, is the layer that a selection is being made from. The third parameter, pt_lyr, refers to the single point layer I created in the line above. The 'CONTAINS' is the type of selection by location I am making, meaning that the zone that contains the point in pt_lyr will be selected. If I want to grab data from that, I have to assign a cursor value to the zones layer which will iterate through only the selected feature. In this example, I want the FID value and the TYPE, and append the TYPE to the list, newData, along with data from the initial pt_lyr to newData.

## Still learning

This is all an exercise in reviewing how I got to this point. It was not an easy process as I spent more than 5 hours this weekend getting the syntax right. I aim on continuing exploring ArcPy and hope it gets more familar as time goes by...