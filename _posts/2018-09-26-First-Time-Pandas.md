---
layout: post
title: "Exploring Pandas with Gameplay Statistics"
description: "Introducing myself to Pandas using my own generated game statistics"
date: 2018-09-28
tags: ['Data Science', 'Python', 'Pandas', 'Dota']
comments: true
share: true
---

# Discovering Pandas     

A few weeks ago I was listening to a podcast that mentioned the value of the youtube videos from the 2018 SciPy Conference. [Here](https://www.youtube.com/watch?v=y7zGnKzaKIw&list=PLYx7XA2nY5Gd-tNhm79CNMe_qvi35PgUR) is a playlist of those videos. Since I am a GIS person that works with data, this piqued my interest. I started out with the GeoPandas video originally, because of my background. Unfortunately, I had to stop midway as the library requires some knowledge of the original pandas library. I ended up discovering other ways to play with data within pandas to discover this new world. It lead me to my gameplay statistics from my playing days of Dota. 

## A Shameful Secret

Yes, dear reader, I play Dota. I don't play as much anymore, but I wondered if I could get access to my stats. There was a python library, dota2api, that made it easy to get them in a spreadsheet. I would say the number of games that it returned, but I'm too embarrassed. So without further ado, let's get into the data and libraries.

## The Toolkit

Pandas is a python library that analyzes data within python. Before I even played with Pandas, I wrote a python script to retrieve my dota stats using dota2api library which saved it to a csv called 'testing.csv'. Here is a picture of what that spreadsheet in excel looks like:

![Dota 2 spreadsheet in excel](../../images/dota_sheet.png)

It's just some a mess of data. It actually looked worse as I had to figure out how to put it in a presentable format for pandas using python and the dota2api library. I now understand why so much time can be used to clean data. From here on out, I'll be doing the coding in a jupyter notebook, which is another tool of the data scientist. There are individual cells that run and you can immediately see the results printed out underneath that cell. I'm using anaconda to manage my python environments and Jupyter notebooks. You can get anaconda [here](https://www.anaconda.com/download/)

Now that you know all of what I used to get pandas into a Jupyter notebook, here is our first 'cell', or what a snippet of code in a notebook is called.

```
import pandas as pd
games = pd.read_csv('testing.csv')
```

Click run in the notebook or Shift+Enter on the keyboard to execute the code. Nothing should be returned here. The testing.csv is saved to a variable called games. This variable's type is a pandas DataFrame. The other type of variable found in pandas is called a series. A series is a one dimension labeled list that contains any type of data, while the dataframe is two dimensional with rows and columns. Each column has it's own unique data type. The Pandas documentation says dataframes can be thought of as a, "dict-like container for series objects."

If you type only 'games' into the next Jupyter cell and run it, here is what you will see:

![Dota 2 spreadsheet in pandas](../../images/dota_pandas.png)

However, we have to do some data management as the API data doesn't have a wins or losses column. It only has a reference to a team win. It also tells me which team I was on for each game. We will create a new column if I was on the winning team or not, with these four lines of code:

```
games.loc[((games['player_slot'] >= 128) & (games['player_slot'] <= 132)) & (games['radiant_win'] == True), 'result'] = 'L'
games.loc[((games['player_slot'] >= 128) & (games['player_slot'] <= 132)) & (games['radiant_win'] == False), 'result'] = 'W'
games.loc[((games['player_slot'] >= 0) & (games['player_slot'] <= 5)) & (games['radiant_win'] == False), 'result'] = 'L'
games.loc[((games['player_slot'] >= 0) & (games['player_slot'] <= 5)) & (games['radiant_win'] == True), 'result'] = 'W'
games = games.drop_duplicates(subset='match_id')
```
There are a few things going on  above that I will point out, but first some background. In Dota, the two competing sides are called the radiant and the dire. The API only returns a true and false if the radiant side won. The player_slot column refers to which team I am on. If the player_slot number is over 128, I was on the dire, and if it was under 5, I was on the radiant. 

The '.loc' is a reference to the label of the column in the games dataframe. This will pull out the value of the column for each game or row in the DataFrame to decide the value in new column, 'result'. Pandas is using the bitwise operators above, so AND is notated by the '&'.

The last line in the code is myself noticing that due to my script, some matches may have been added twice to the csv. I can identify each row by the unique match_id and drop duplicates if they do exist so I will now have all unique values.

## Grouping 

I will next talk a little about the groupby method of pandas. But first, here is some more background on Dota. In the game, you play as a hero, and there are over 100 heroes in the game. It has a steep learning curve since many heroes are in the game along with items and such. I have a columns of basic outcomes from the games I've played including kills, deaths, and assists. What I want to know is the average of those for each hero on the list I've played. There is a column named, 'hero_name', and this will we create another variable, gr on. Like this:

```
gr = games.groupby('hero_name')
# if I want the average of deaths on a hero, I would do this:
gr.deaths.mean().sort_values(ascending=False)
```

This would return a list looking like this in the jupyter notebook:

![List of death averages by heroes](../../images/groupby.png)

I am creating a variable, gr, that is a grouping from the games dataframe of the column, 'hero__name.' The grouping is created by the .groupby method. This will allow me to get statistics by each hero I've played. You can see in image above that is the average of how many deaths I have on each hero. Some heroes I've played more than others, which may be the reason of some of those stats, especially the top hero, as I may have played Techies once or twice. 

## Fun in Stats 

Statistics was never a strength of mine, but it's fun when I'm using my own data to explore my
gameplay statistics. Even I don't play much anymore.

