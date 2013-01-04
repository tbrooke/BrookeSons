---
title: "Learning Oauth 2"
author: 'Tom Brooke'
layout: blog
tags:  [ Oauth ]
---
# Learning Oauth2
## Part I  of Creating an app using the Clio API

The http specification and the REST specification (Representational State Transfer) form the backbone of the web. Through a restful interface browsers can communicate with web applications and web applications can commnicate with each other. By providing a public API the owner of a web app can allow others to communicate with his app and allow for the exchange of data between his app and the world. Clio has recently published their RESTful API and this is a great boon to develpers wanting to integrate with the Clio platform. 

Clio is a cloud based law practice management tool developed in Ruby on Rails. The API documentation is located at: [Clio API Docs](http://api-docs.goclio.com/v1/index.html). Since Clio is a legal applciation designed for lawyers one of the primary considerations is the security of the data. By providing a public API you do not want to allow people to muck around with your data without authority and even then you want to keep the access limited. To do this you untilize a modern authentication system such as Oauth2.


 The authentication used by Clio is the Oauth2 method and the first step in creating an application that communcates through the API is to get properly authenticated and set up a secure session. Clio provides sample ruby code but it would be helpful to get familiar with the workings of Oauth2 before diving in. Oauth2 is a protocal adopted by Google and several others to establish a web authentication protocal. The specification is set forth at [Oauth](http://oauth.net/2/)

The analogy set out in an introduction to Oauth2 is that of giving a valet key to a parking lot attendant. It grants linmited acces to your car but not full access to the trunk or whatever. It is the same with Oauth2 authentication - you can grant someone, or in most cases another web site, limited access to the resources on your web site without giving them a password or full access. Clio uses what is know as a *Grant type authorization* based on the obtaining of an authorization code to allow access. 


Some times it helps to manually walk through a procedure and one of the easiest ways to start working with Oauth2 is to check out Google's [Outh2 Playground](https://developers.google.com/oauthplayground/). We will use the playground to demonstrate exactly how Oauth2 works.


To get started you have to set up the Clio side of things by creating a new app in your Clio account. The Clio documentation describes this process and it is fairly easy to get started. When setting up your application set up *https://developers.google.com/oauthplayground* as the url for your application since this is the callback url needed to connect Clio and the Google playground together. The Clio side is the server side of your application. When you have this set up you can go to the Google playground and and set up the client side of the equation. 

To set this up for Clio you must use a custom configuration which you can set up by clicking on the little sprocket on the top right hand side of the screen. THis will produce a drop down window entitled OAuth2 configuration. You are creating a Server side configuration so indicate that in the selection window and for OAuth endpoints: you are using custom instead of Google. After you complete those fill in:

 Authorization endpoint: https://app.goclio.com/oauth/authorize/

 Token endpoint: https://app.goclio.com/oauth/token

Access Token location: Authorization header w/ Bearer prefix

![](blog/assets/setup.png)

Then put in the Oauth client ID and Client Secret that you received from Clio  


You are now setup and ready to go so go to Step 1 and set up and authorize the API. The way the authorization works is that you present your credentials, which we have just set up with the client ID and Client Secret, to Clio and if everything is in order they issue an access token. This is sent back and by using that token you have access to Clio. The token is limited in that it can only be used by your Web Application to log into Clio and it is only good for 10 minutes. 

Lets go over to the left hand side of the screnn and beside the Authorize APIs button put in:

https://app.goclio.com/oauth/approval



then click on the Authorize APIs button and viola you're at Clio

![](blog/assets/ClioConnect.png)

Click OK and you will be taken back to Google with the acess Token filled in for you:

![](blog/assets/connected.png)

Now you are ready to go and we can move on to step 3 which allows us  to configure a request to the API 

under HTTP Method Add Headers (in the little box above the request space) put in Host and for the header value put in:  app.goclio.com

Now we are ready to put in our RESTful request - We are performing a simple GET request which we can select in the selection window and let's start with something simple like Who am I 

https://app.goclio.com//api/v1/users/who_am_i


If we have done everything right we get back:

{"account":{"id":"8DTsvjEvjIfpufuz/vefRw","owner":{"id":343991381,"name":"Tom Brooke","url":"/api/v1/users/343991381"},"name":"Law Office of Tom Brooke","maildrop_address":"643fedb36@maildrop.goclio.com","created_at":"2011-03-14T20:15:50+00:00","updated_at":"2012-11-15T00:07:53+00:00"},"user":{"id":343991381,"email":"tom.brooke@gmail.com","first_name":"Tom","last_name":"Brooke","time_zone":"Eastern Time (US & Canada)","enabled":true,"subscription_plan":"Attorney","created_at":"2011-03-14T20:15:51+00:00","updated_at":"2011-10-06T04:53:43+00:00"}}

Now let's look up our matters which is only one at this point:

then enter your request as a full address ie: https://app.goclio.com//api/v1/matters 

And viola we get our response:

 {"matters":[{"id":1017058013,"client":{"id":863215887,"name":"Sam May","url":"/api/v1/contacts/863215887"},"display_number":"00001-May","description":"New Matter","status":"Open","open_date":"2013-01-04","close_date":null,"pending_date":null,"location":"","client_reference":"","responsible_attorney":{"id":343991381,"name":"Tom Brooke","url":"/api/v1/users/343991381"},"billable":true,"maildrop_address":"643fedb36+matter1017058013@maildrop.goclio.com","created_at":"2013-01-04T19:02:26+00:00","updated_at":"2013-01-04T19:02:26+00:00"}],"records":1,"limit":1000,"next_offset":1017058013}

That's it. Of course we will be performing all of this automatically as in the Ruby code provided by Clio or we cna write our own code in Ruby, Clojure or whatever. Once we get in our program will parse the json responses and do our magic with them 

Feel free to leave comments or questions and consider joining out Google Group at: *https://groups.google.com/forum/?fromgroups=#!forum/clio-developers* where we discuss thest things. In the next post we'll put together a Sinatra app that uses the API and authentication we have covered here.

 
