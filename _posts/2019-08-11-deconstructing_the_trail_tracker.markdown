---
layout: post
title:      "DECONSTRUCTING THE TRAIL TRACKER"
date:       2019-08-11 04:18:30 -0400
permalink:  deconstructing_the_trail_tracker
---


## Generating the Idea
Generally before I build an app I’ve had half a dozen ideas bouncing around my head for days or weeks before I begin.  The  application idea I select is usually determined by which idea appropriately meets the terms and scope of project requirements defined by my Flatiron instructor.  This time I tried a totally different approach and reached out to my sister Katie who had an upcoming birthday and asked her what problem she was experiencing that I might be able to solve digitially through creation of a web application.  She expressed how she would love to be able to share local trails with her daughter that were not listed on google or other mapping programs with adding them to a database as publicly available as google.

## What Is It? What Does it Do?
I brainstromed and mapped out the idea.  I envisioned a simple application with a visually appealing, intuitive user interface with basic functionality for users to record details of local trails, add, images and details about these trails, and record and share their hikes on them. Because this is a two part project I had to ensure that it would allow me expand applications with javascript features and that I could fill all the project requirements for both assignments with this concept

## The Easy Parts
After the gazzllion exercises I completed in both Ruby and Sinatra in the course curriculum, I felt fairly confident with my knowledge executing CRUD functionality through MVC architecture.  Though Rails is a mammoth framework, the basics of it are pretty straightforward, and the documentation is very clear, easy to navigate and well written and maintained .  That being said Im sure I will eat my words when someone later points out a rookie mistake I overseen in my rails application.  I kept waiting to get stuck, to hit a wall, or a challenge I couldnt overcomebut  for the most part I was able to sail through the implmentation of these features without ever really being stumped. Although this was my first Rails app, having previoulsy built a sinatra application from scratch the MVC architecture and seperation of concerns principles were the same, with the added inmplemention of RESTFUL routes.  This was also my first time using twitter boostrap, and while it was my first time working with it, and with my limited experience in front end development but extensive experience in graphic design, I found it refreshingly easy to use, and manipulate once I got the hang of it.


## And then Came the *Other* Parts… 
<br>
![](http://i.ebayimg.com/images/i/401066743191-0-1/s-l1000.jpg)
<br><br><br>

Pure grit. That was what it ended up taking me to finish this application.  Not because it was conceptually so challenging that I couldnt complete it.  All my hurdles came in suprise packages from outside the development environment.  First was my international move from the U.S. to an island in Mexico which tied up a huge chunk of my time and resources for a short period. Ok, no big deal.  That was a choice and  I had planned, researched and prepared for a graceful transition  (Or so I thought).  Then began the domino effect which started while wrapping up the tail end of the project, right as I was recording my demo video and writing my blog post, days before planning to schedule my assessment. <br><br>

The hurdles started with the amateur electrical wiring in our temporary accomodations that shocked me with 220V on a daily basis through open neutral circut and quickly rendered my just over one year old macbook air useless. Gah! Ok,  no problem. <Gritting teeth, while opening wallet>  So... even though I was on an island of 70,000 people in Mexico... I found a repaiman who replaced my motherboard within a day for very afforable price.  Lucky me!  What were the chances?  Yeay!  Next hurdle, the replacement motherboard worked beautifully but ...also revealed there was more damage than anticipated, and the faulty wiring  had also destroyed my built in speakers, microphone and audio jack. No audio input, meant no demo video, no online assessment.  <Gritting teeth, while opening wallet again>  Through a very complicated arrangement aka "Operation Cozumel", I was able to locate a secondhand Dell XPS 15 replacement laptop back in Austin, TX.  I wired money for it, hired someone to go check it out, purchase it and ship it to Oklahoma ,and then had someone else fly it from Oklahoma to Mexico. Okay.  That took a few weeks but problem solved. I also purchase a "No Break" uninterupted power supply with led voltage monitor to prevent anymore damaged electronics.  Now, working laptop in hand, crisis averted. I got this.  Sigh,  I moved  from electrified paradise into new permanent apartment, where the property manager promised fast wifi that had been set up for previous remote worker inhabitants.  And...nope. I can't even load google on my 2Mbps wifi connection. Doh!  <Gritting teeth, while opening wallet and peering in to see if there is anything left> Yeay! Superhero computer repair guy also turns out is actually a network guru in disguise (with a Masters in Network Engineering). He saves the day once again by appearing with a brand new wifi extender, which I regretfully discover after a day does very little through my concrete walls.  But, that's okay, because the next day my newly adopted superhero (let's call him Edgar) resells me my neighbors long range wifi radio antennna transmitting 400Mbps from a dedicated fiber connection on the other side of town.  (I think every programmer needs an 'Edgar' in their life. ) And so... that it how I was able to complete my blog post and demo, one paragraph at a time, and how I became the proud new owner of a Ubiquti NanoBean AC Gen 2 Power Beam and possibly the best internet connection on the island, with my own superhero.
<br><br><br><br>


![](http://4.bp.blogspot.com/-u1gwflnN2nU/UcT5TE2kGwI/AAAAAAAAAmQ/yna9tX5z78g/s1600/island_ipad_fullsize.jpg)
<br><br><br><br>

Aside from the logisitcal challenges I overcame there were few programmatical challenges along the way.
  	
I began my application some months ago before deciding to tackle the rest of the curriculum material first and return to my project, so there were several months in between beginning and finishing and so I had the experience of  having to reaquaint myself to my code, and returning to an application with a deprecated gem and a security vulnerablilty.  

Upgrading the deprecated gem in my application was an easy fix.  I  simply replaced the chromedriver-helper gem with the new webdrivers gem in my gemfile.  Chromedriver-helper was created to run system tests for the Chrome browser and was deprecated in favor of webdrivers with discontinued support as of 03-31-2019.  

Regarding security issues in my application, an alert appeared in my Github profile on May, 29, 2019 warning me of a security vulnerability of 'high severity".  The widely used OmniAuth gem that creates an alternative sign up and login method for new users by allowing them to sign in to popular accounts such as facebook, gmail, and github to authorize and authenticate their identity had been recognized as a security risk. Apparently the OmniAuth request phase is 'vulnerable to Cross Site Request Forgery when used as a part of the Rails framework'.  This vulnerbillity creates an opportunity for a secondary account to be able able to sign into a primary account without the primary users consent.  No patch was available at the time of writing this blog post but there is a suggested fix which instructs developers to add the omni auth-rails_csrf_protection gem to the application gemfile and ensure that any links that initiate an Oauth request phase and use an HTTP GET request is converted to an HTTP POST request form with an authenticity token value, most easily by replacing suspect instances of link_to with button_to.  

Other little bugs included, form validation with missing error messages,  flash messages appearing again later on the wrong pages, unexpected behaviors between associated objects, for example if deleting a trail that another user had saved an associated hike to cause all kinds of errors with missing attribute values.  Making the application fluid and responsive for dfferent browser window sizes to accomodate different devices also proved to be challenging and time consuming. One by one I worked through each to an appropriate solution.  Of the more complicated solutions I implemented was the use of polymorphic associations between models.

## Polymorphic Associations
When creating an architectural outline for my application, I realized that the image objects I was creating, did not belong to one other object model in my program, but rather could belong to almost any model in my project.  A profile image would belong to a user,  an image of a trail would belong to it’s trail, and a users images of a specific hike would belong to the hike.  I needed to create an image model that could morph its association to which ever model that instance of an image belonged to.  As per the Rails guide: “A slightly more advanced twist on associations is the polymorphic association. With polymorphic associations, a model can belong to more than one other model, on a single association.”

Polymorphic associations are a fairly straight foward concept but while I am practicing limiting my own scope, it is a concept with enough material to explore in a seperate blog post dedicated strictly to this topic where I would also like to explore polymorhpic route helpers so I will only cover it briefly cover here. <br><br>

To implement polymorphic associations:<br><br>

			Create your migration to build your model
			Add the [model] id and [model] type line to your migration file
			Run rake db:migrate
			Add the belongsto association to your polymorphic model
			Add the reciprocal associations to your has many models
			Create methods to create the appropriate restful routes
			
<br><br>
The unforseen complication of this type of association was that my rails path helpers no longer worked.  When I discovered this I made the amateur error orfreinventing the wheel without checking for an exisiting solution.  I ended up spending a whole lot of time building many neatly encapsulated helper methods to create my own paths for each polymorphic variation.   While refreshing my understanding of polymorphic associations for this blog post I stumbled across polymorphic route helpers that it did not even occur to would already be available.  Again, I would like to revisit this in depth in a future blog post.

<br><br>
![](https://proxy.duckduckgo.com/iu/?u=http%3A%2F%2Faturkewi.github.io%2Fimages%2Fpolymorphic.jpg&f=1)
<br><br><br>
##  Future Features
This is a two part project and I intend to extend my app by giving users the capabilty to share feedback with and interact with other users by adding JS comments to Trails.  I would also like to use google maps api to enable users to add location maps of trails with start and end markers, use geolocation to display user or device position in relation to these trails and add google directions service to map direction to trail heads.

## If I Had a *Do Over*
Projects are always the deepest and broadest scope in the learning curve.  If I were to do this project again, I would simplify.  I would focus on limiting scope and improving the quality and syntactical sugar of my code.  I would write the best little program instead of a mediocre larger more complex one.  I think there is eloquence, and efficiency in simplicity.  I would take time more time to brainstorm user scenarios and app behavior to define and understand the clear relationships between objects as the user interacts with them. For example - the Trails and Hikes relationship. If a user deletes a Trail what happens to the references to that Trail by associated Hikes on that Trail that other users have created.  I would clearly define these behaviors and what ifs at the beginning of the architectual process rather than being suprised by them as bugs while nearing completion and having to create methods as afterthoughts to correct them.  I would avoid the added complication of polymorphic associations, if practical.  I would refactor my code to reduce the number of databse inquiries.  I was torn between reducing potential bugs caused by storing old databse requests in instance variables and making frequent new requests to the database to prevent variables from holding on to stale data. I choose the latter, but I like to find a more efficient way to reduce the number of this requests and keep my variables current. So to sum, less db request, smaller scope, simplified code base, higher quality code, polymorphic route helpers instead of custom path helper methods.

## D-R-Y
In the spirit of not repreating myself, mistakes included, a couple of takeways that Im sure will be useful to retain for future projects would be the use of polymorphic associations on my object models if I needed to recreate simillar relationships.  Simple things like remembering to add regex for end of string in date and number range validations, and watching out for missing attribute references on forms, and form label helpers.  I learned about handling deprecated gems in my app, and correcting security vulnerabilities created by the use of popular gems.  With my unanticipated electrical, hardware and network issues, I got side lessons in hardware repair and replacement, open neutral electrical wiring, uninterupted power supplies, wifi extenders and long range wifi antennas, and a refresher on setting up linux development environments with my replacement laptop.  All in all I would say these challenges provided me with a pretty well rounded curriculum in all things web developement  All are lessons I am eager to retain.<br><br>


![](https://proxy.duckduckgo.com/iu/?u=https%3A%2F%2Ftse2.mm.bing.net%2Fth%3Fid%3DOIP.4tkMiuG-fQfllunKPicYSgHaEs%26pid%3DApi&f=1)
