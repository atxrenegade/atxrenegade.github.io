---
layout: post
title:      "BUILDING CASTLES WITH SINATRA"
date:       2018-10-22 02:10:13 +0000
permalink:  building_castles_with_sinatra
---


## THE MAKER

Im a maker at heart and every time I get an opportunity to build something I have a tendency to get carried away.  I call it  “Building castles where only bungalows are required” syndrome. Learning about constructing MVP’s (Minimum Viable Product) has been both enlightening and beneficial for this tendency towards elborate prodcution.  Needless to say this project was my first opportunity really make something out of thin air and I was going to have fun with it.  Even if it meant putting an extra day or two (week or two) to build something I was proud of.  

## THE APP

My vision was an implementation of something close to my heart and something I have been for some time strongly encouraging those around my to participate in.  I call my application Morning and Evening Pages, or AM/PM pages for short.  Morning and Evening pages is created to assist users in recording and making a habit of a morning and evening ritual that includes intention setting, gratitude and reflection.  

I designed it to encourage and implement a daily practice of conscious focus and repetition to create new neural pathways leading to conscious living and away from subconscious reactivity.  Throughout our hasty lifestyles our attention and focus is constantly being directed away from our goals and intentions as we are bombarded by the latest media driven fear and the continuous perceived threat to our survival. While technology has enhanced our lives in many ways, I believe that it can be more beneficially used to create and develop mindfulness and healthier perspectives of our world.


![](https://github.com/atxrenegade/AM_PM_pages/blob/master/public/site_design_mockups/Screen%20Shot%202018-10-14%20at%2010.04.56%20AM.png?raw=true)



## THE PROCESS

So more about the app. One of my favorite parts of the process, was beginning from absolute scratch. Ground Zero meant I knew exactly where I was beginning from and allowed me to create a fluid systemic step-by-step approach to building from the ground up (hah, at least in my head). This allowed my to create every file and write every line of code, aside from a small skeleton framework I customized for css styling and a few lines from a README.md template.  I was able to become intimately familiar with every piece of my application.  Since this was a simple Sinatra application using Model View Controller architecture to implement CRUD (CREATE READ UPDATE DELETE) functionality, I felt fairly comfortable with the process, since we had been required to complete similar projects multiple times throughout the Sinatra section.  Following a couple of minor bumps in the road I began to take for granted the ease of this process I hit my first roadblock/challenge. 

Reflected in the image below is the basic strcuture of MVC architecure.


![](https://smist08.files.wordpress.com/2013/11/mvc3.png)


## SPEED BUMPS AND ROADBLOCKS

#### Pluralization 

I hit my first minor speedbump early on, which revealed itself as a question about active record pluralization that eventually had to be corrected in both my database and my class names.  The question in my mind was "Does Active Record recognize grammatically correct pluralization?"  I leaned towards a less intelligent system and wrote my initial user the assumption that it would not accommodate proper pluralization of class names and database table names.

If my database table name was AM ENTRY would ActiveRecord recognize:

AMEntrys   OR    AMEntries ?
PMEntrys    OR    PMEntries ?


As it turns out ActiveRecord does recognize grammatically correct pluralization, and since I erred on the side of caution, I had to update all my class names and tables names. Easy fix. In general naming conventions ahs been a sore spot for me so  for my own reference I created a table to remind myself since I seem to be so painful unable to retain them.

![](https://github.com/atxrenegade/AM_PM_pages/blob/master/public/images/ActiveRecordMvcNamingConventions.png?raw=true)


#### Loading Controllers in Alphabetical Order

While ActiveRecord is intelligent enough to recognize pluralization, Sinatra does not know to automatically load application_controller.rb before all others. Instead it loads each controller in alphabetical order and since A-P-P is most often going to be the first file loaded, in my case it did not come before A-M-E (AMEntriesController).  I spent hours puzzled by this phenomenon before throwing out a life line to Section Lead Howard DeVeenish who helped me identify and correct the issue by requiring the Application Controller at the top of my  AM Entries controller.

#### Model Architecture 
When it came to model architecture I was not entirely sure about the most efficient way to design my models. I had several attributes for my am_entry and pm_entry classes and many of those attributes were multiples. For example, In both AM and PM entries I had an attribute for gratitude. The are five lines to input and store five gratitude items.  Ideally I would have made each attribute a model itself containing these multiple variables for easy retrieval and interaction of each individual instance of an attribute - now object. However for the number of models/tables I would have had to create, this would have expanded this project well beyond the scope of the exercise and I was already struggling to restrict to myself to something small enough to graduate before retirement age.  This was a case where I was able to restrict myself to building an apartment complex sized project, instead of castle when only a bungalow was required.  

When constructing my models, I decided, to be ultra efficient I would list each attribute by number on the view pages and then store them as an array in the database under their attributes/column titles.  For example the view page would collect gratitude[gratitude_1], gratitude[gratitude_2], gratitude[gratitude_3, gratitude[gratitude_4], gratitude[gratitude_5], and I would compress these values and store them in the database as an array.  This resulted in strange unrecognized strings with extraneous escape characters that I could not extract when retrieving the data. It also resulted in a complicating the process or retrieivng and updating these values.  I learned/was reminded that SQL does not recognize or store arrays and eventually had to resolve myself to the fact that EACH iteration of EACH attribute would have to be stored independently.  For example, my table instead of reflecting a column named “gratitude” containing a gratitude array, of five gratitude items, now held a COLUMN for each gratitude item named “gratitude_1”, “gratitude_2”, “gratitude_3”, “gratitude_4”, gratitude_5”. While it didn’t feel like the most was the most elegant execution it did create the functionality I was seeking.  One hundred migrations and revisions later my models, views and revised column names reflected my new format.

#### Recording, Storing and Formating Date and Time 
Time.now - Timestamps, datelines, time formatting, time updated. This turned out to be my biggest headache.  My intention was to record and display a simple Time and Date heading at the top of each entry and organize the entry index by this data.  Instead what I got using Time.now in my table was a hardwired date in my schema labelling every entry the same date and time.  Additionally if I edited/updated an entry it would  now display the correct date and hour of the entry change but the minutes would always display 10 minutes after the hour.  For example if it was 5:22 pm it would record 5:10 p.m., and if it was 3:56 a.m. I would get a time of 3:10a.m.  This was another lifeline issue for me and after a couple debugging sessions with a technical coach and section lead we were able to correct it.  The created_at bug with the hardwaired date and time was resolved by dropping the database, and changing the initial migration to remove the default value from the created_at column of the AM and PM entry tables.  The 10 minute after the hour bug literally turned out to be the result of one letter that was capitalized when it should have been lowercase.  In my time and date formatting method I had referred to the minutes variable as %M  instead of %m.  Because %M is actually representative of the MONTH,  NOT the MINUTE, and the month was October the10th month of the year, it was the cause on my 10 minute after the hour anomaly.  One tiny typo wreaking havoc!  

## THE EXTRAS

#### The About Page  
Here I took an opportunity to explain the use and purpose of my application along with an opportunity for flagarant self promotion which I leveraged to the fullest.  My intention for this application is to actually have it up and running on a server for people to use for their own benefit.  It is my way of learning while simultaneously contributing and creating value…and promotion.

#### Styling and User Interface 
I am a visual/logical person and I believe in creating things that are both pleasant to look at, as well as intuitive and functional to use. I tried to reflect this as best I could through the interface by including multiple easy to navigate buttons on every page, with a fluid transition between CRUD functions. Regarding styling I was able to locate a skeleton css template, literally called “Skeleton Template”.  I customized and modified 80% of it to suit my design vision, and cut out some of the unnecessary content. I sourced images and fonts online from open sources.


## FUTURE FEATURES and POTENTIAL IMPROVEMENTS

#### Improved stylesheets css and inline style  elements  
Since this was not a requirement of this exercise and I am not fully comfortable with modifying stylesheets at this time.  I did not spend what I could have to implement better coding practices in my css style sheets and view pages.  To correct this I would spend the time to remove redundant inline style elements and use specific css selectors to target areas of the page I was trying to format. In concept this is a great idea but when I tried to execute it, I made a mess of my view pages and decided that this was not a part of my MVP and that I couldn’t afford to spend anymore time on it for now.  I may correct this at a later date.  I would also like to see that my view page displayed correctly on various device sizes and add appropriate queries for a consistent viewing experience across all platforms.

#### Live and Online 
My end goal is to create a portfolio project I am proud of but also to create something this is usable, live and online.  I will add the live application here address here when I do.

#### Password Recovery 
I did find steps to create a password recovery process for Rails application.  I do not know if it would work with Sinatra but it is something I would like to try to add as feature to this application. Im going explore this further.

#### Background Randomizer 
I did try to implement this with two different sets of images.  One set of stunning HD nighttime images to be randomly viewed as backgrounds to PM pages and another of striking Daytime scenes for randomized backgrounds to Morning Pages. This was an interesting concept because while essentially creating a branch in my project and testing an implantation from stack overflow I had worked out in my head multiple variations and had concluded that simplest way was to select images by id number depending on what the entry id number was.  For example if the entry created was an AM Entry with an id number of 22 this would result in a background image number 2 from the AM Images folder. I did find an alternative implementation on stack overflow that I was unable to successfully incorporate on a brach of this project at the time of this post but will continue to work on it.

#### Desktop and Mobile Ready 
As mentioned above I would like to add media queries to make this application mobile and desktop ready. 


![](http://webizrada.org/wp-content/uploads/2015/11/mobile-ready.jpg)



## AND SO I’VE CONCLUDED…

I enjoyed the opportunity to create using all my new skills and knowledge in one application. It really brought to light areas that I hadn’t fully grasped yet, syntax I had remembered incorrectly and solidified concepts that prior to I may not have been confident about.  I am a really hands on learner and after this project I truly believe that it is through the creation and completion of a programmers own passion projects that one will learn and benefit from most.






 
