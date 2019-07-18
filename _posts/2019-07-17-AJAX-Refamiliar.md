---
layout: post
title: "What is AJAX?"
description: "Refamiliarizing myself with the technology that is AJAX"
date: 2019-07-17
tags: ['JavaScript', 'AJAX', 'Web Development' ]
comments: false
share: true
---

# Getting back to Web Development via class!

These past few weeks have had me returning to Web Development projects on the side via the CS50 Web class. You can find the class on EdX, or by clicking (here)[https://courses.edx.org/courses/course-v1:HarvardX+CS50W+Web/course/]. The course is a strong introduction to the material if you have never worked with these technologies and also works as a way to come back to web development.  As an aside, Mozilla's documentation has been a great help in the projects.

# What is AJAX?

AJAX, or Ascynronchous Javascript and XML, is a technology found in Javascript that lets you do two things. The first is to transfer resources between the user's browser and the server it is communicating with. The second is the delivery of these new resources to the user's browser without reloading the whole page. The Asynchronous part of AJAX refers to this second feature.  The data sent between the two can be XML, HTML, text files, or JSON, which is the most popular format used.  HTTP requests make all this communication possible.

An example of an application using AJAX I made a few months was the tornado data viewer application.  The user fills out a form that sends a request (in a JSON format) to send the request. The Flask application receives that request and queries the database which then uses a flask feature, jsonify, to return the geometry and other data to the browser in JSON. The example below is a smaller, simpler example of the same thing.

## AJAX Example by way of changing a title

I have a small flask application that I wrote this morning as an example of the communication between the browser and a (fake) server.  The Python file contains 17 lines of code while the Javascript file has roughly 20. When you run the sample application in the command line, it will bring up this index.html that has a box for a user to input text and a submit button that will do something when the program runs. The heading of the page requests the user to input their name, which I will do here:

![Entering a name in](../../images/step1.png)

My name is now there and I will click the button which produces this page:


![What a greeting!](../../images/AfterButton.png)

I added the word, 'Hello' plus the input of my name and put it in the heading. The text input went to the server when it could have been changed by Javascript.  In this case, it will be a learning example. 

### Making a request

When the button was clicked, the contents of the input were saved to a variable called 'text' in the Javascript file. We also created a variable, request, as a new XMLHttpRequest object. This variable will contain the methods we need to send the text to the server, but there are a few things that need to be done first, as recommended by the docs over at Mozilla. 

We use a JSON method to wrap the text into a JSON format. When the data is sent to the flask application, the request library's get_json method will be able to decipher it into a Python dictionary.

Here are the three variables in our Javascript code mentioned above:

![3 Variables of Request!](../../images/AJAXEstablishing.png)

We now have to get that data from our browser to the server. This will be done by our request variable's send method. The data we are sending will make this a POST request as opposed to a GET request. When making a 'GET' request, the user is requesting something on the server with no parameters sent to the server, like going to a website.  A POST request involves the user sending data to the server to be processed. Request.open initializes the POST request. It also includes a parameter for the flask route, newTitle, where we will send the data to. 

When requests are sent to servers using HTTP, there is a head part and a body part. The body part contains the actual text that we typed, while the head contains metadata about it. The head part would contain the 'POST' mentioned above as well as content-type that was set in the setRequestHeader method. We're sending JSON so that is specified in the header. This method must be set after the open method has been set, but before send, which is how we did it here:



![Header Request!](../../images/requestHeaders.png)

The text we converted into a JSON has been sent to the flask application. Once the data is in the flask application, it will run the function within the newTitle flask route, which is named returnNewTitle. Here are the few lines of code where we are at now:

![Python Code!](../../images/appPythonCode.png)


The first thing to do is unpack the sent json with the request.get_json() method and save that as variable, userName. I have a couple print statements to show the type of data userName is and what is inside it. Here is what printed in my terminal when those lines of code are run:


![Python Code!](../../images/printStatements.png)


You can see that the unpacked JSON that was delivered is a dictionary in Python. It follows a similar key-value structure as a JSON. The next two lines will involve us making a little adjustment to the variable, by adding a, "Hello" to it.  We return the new data to the browser with the jsonify method of flask.


![Variables Returned!](../../images/variable-returnImg.png)

### Viewing it within the Network Tab

Our data is now coming back to the browser, having been altered by our Python script. I would like to point our attention to the network tab within Chrome Devtools, which, among other functions, displays all the files it downloads to load the websites you visit. When you first load the index.html file with the input empty and the heading asking the user to enter your name, the network tab lists these files: 

![Network tab before request goes through](../../images/netWorkTab_ChromeBefore.png)

Once we have typed a name and hit submit, the heading changes to now include your name plus a greeting. I put my name in, and it looks like this:

![New Title on site!](../../images/SiteNewHeading.png)

There it is returned with a new heading. If we return to Chrome's network tab, we will see our newTitle route within the list as well:

![Chrome Network after!](../../images/chromeNetworkAfter.png)

There is the route that we sent to the server. If we click it and then header, we can see the data that was sent. The JSON we created is there under the payload section.

![Network details!](../../images/networkNewTitleDetails.png)

The data was sent and we got new data. What happens when the server returns that new JSON? We return to our request variable in the script file, with the onload function. A note before we move on to this function. In my previous images, the onload function was written after .send, which was a mistake. It should have been written before the line where .send is run. 

The onload function, is a callback function. It runs when the request has successfully completed. In this case, we do something with the data that has been sent from the server after parsing the JSON into a data variable. 

### Recappin it!

1. I input my name into the text input box of the HTML.
2. I hit submit and the browser sends the data to the flask back end
using AJAX.
3. The function runs, makes changes to the variable that was input to the browser, and returns it in another form of JSON to the browser.
4. When the request has returned data to the browser, the onload function runs. It unpacks the JSON data and updates the header of the page. 


I hope to have more web development blog postings in the future and to include web maps in them as well. 
