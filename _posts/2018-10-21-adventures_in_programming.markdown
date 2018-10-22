---
layout: post
title:      "ADVENTURES IN Ruby "
date:       2018-10-21 22:26:19 -0400
permalink:  adventures_in_programming
---

## (or Lessons Learned from the Creation of a CLI Gem)
### SCOPE AND CONCEPT
My original concept idea, with all my enthusiasm and naivety was to create a grandiose all purpose developer reference that included shortcuts for atom, ruby methods, cli commands, pry commands, and any other routinely used tool by web developers. While great in concept I spent weeks pouring over pages trying to find any workable “cheatsheets” that did not either come in pdf format or html tables.   Eventually I realized that working with pages and pages of tables was going to be tedious and nearly impossible to parse and make sense of.  I narrowed down my scope by the selecting the most agreeable table of all the tools and web pages I had selected, which turned out to be an atom shortcut reference page on GitHub. 

**Lesson learned:** MVP. Minimum Viable Product. Start small in scope and scale, expand after a thoroughly working and tested MVP has been achieved.  Build one piece and a time, get each piece working properly before jumping ahead. Be patient and disciplined about it. It’s much easier to find a bug in one block of code than in hundreds of lines of code later on. 


### SCRAPING AND DATA FORMATTING
Since the page I selected to scrap my object data from was in table format it did require a fair amount of parsing, tweaking, splitting, and hashing to create an array of hashed values for each object to be passed into the model classes.  I  thought I had it all worked out, and was some way into the project when I noticed I noticed odd capitalization, and missing table values with caused my hash objects to offset, reversing key value pairs.  I solved this by adding n/a values to the array to put attributes back in their correct order and placement.  In retrospect though I new a table would be challenging I may have chosen something else more reliable to work with and easier to drill down into using by using css selectors.  

**Lesson Learned:** When choosing website to scrape look for sites that have clearly formatted css selectors, and break down elements in a systematic and orderly fashion.

### MODEL ARCHITECTURE
![](https://i0.wp.com/www.brainpickings.org/wp-content/uploads/2013/01/chickenegg1.jpg?w=300&ssl=1)

This particular topic seem to bring up the most questions in my mind. Sometimes I puzzle over it till I find myself in an infinite loop, spinning in circles till I get find the end or the beginning. Which comes first the chicken or the egg? Is the egg a product of the chicken, or is the chicken a product of the egg.  Begin infinite loop. PANIC!  Using my novice understanding did have of the bleongs_to, has_many, has_many_through and object relationships, I sat down and wrote out three different alternatives for constructing my models and chose the one that felt most natural to what functionaltiy I was trying to achieve with my application.  I've included this document in my project file. 

### CLI APP FUNCTIONALITY 
The interface flow of my cli gem would begin the user with a brief introduction of the application followed by the main menu where the user would first select their operating system from the list. This would result in a method call to the Scraper, which would then scrape the website for all shortcuts, pass this array of strings into the Data Formatter class.  The DataFormatter class would create my object hashes that were passed into the Shortcut model for object creation and return a submenu to the user.  This new submenu (search menu) would include a LIST ALL SHORTCUTS option - that would display all atom shortcuts in a numbered list for the users selected operating system, a SEARCH BY KEY option - that would allow users to search shortcut for a specific key sequence ex. “ctrl-m”, and an option to SEARCH BY NAME - allowing users to search atom shortcuts by name ex. “Open File”.  If they chose to LIST ALL SHORTCUTS they would be given the option to select by a shortcut by number from the list, and the program would return a detailed view of their chosen shortcut.  The detailed view includes the shortcut name, key sequence, OS, and a brief description of the shortcut functionality.  Typing ‘X’ followed by enter, exits the user from the program from nearly anywhere within it.  

Once the objects were created and I had created the search and list functions I noticed that theta wasn’t really being displayed in any kind of order so I decide to create an alphabetize function to make it easier to visually digest.  

### OBJECT DUPLICATION
![](http://www.vikrambedi.com/wp-content/uploads/2017/10/duplicate-content-image.jpg)

While testing out my List All Shortcuts function I discovered an interesting bug that puzzled me.  While my first OS displayed all the correct objects and values as requested, the second operating system returned all the requested objects plus a duplicate copy, and the third operating system seemed to return a triplicate copy of shortcut objects. Yet other times it seemed the program worked flawlessly.  This inconsistency stumped my even further. At the time of writing this I did not know what was causing this bug and it is the final unremaining detail in completing my project. Lightening strikes, as I type these letters I’ve had my ahhhaaa moment.  

![](https://vignette.wikia.nocookie.net/buddyfight/images/d/d8/Homer-simpson-quotes-doh-i11.jpg/revision/latest/scale-to-width-down/304?cb=20141227165956)

What would seemingly cause a correct number of objects to be displayed for the first OS, duplicate for the second, and triplicate for the third OS.  D’oh!  Could it be tied to the fact that my scraper is called AFTER the OS is selected, which MEANS it is being called for EACH TIME a new OS is selected creating yet another copy of each object EVERY TIME the scraper class is called.  I resolved this by moving the method call to the Scraper class from it's awkward position in the Main Menu to the CLIInterface initialize mthod where is would only be called once per program execution, this immediately corrected the issue.

**LESSON LEARNED:** Be familiar with the lifecycle of objects and sequence of events in your programs.  Forcing you to articulate bugs, concepts, problems often helps determine in clarify in your own mind what these things are.  Often when I feel stuck on a concept or am the debugging process, the first thing I do is ask myself - What is the question? What exactly am I trying to accomplish? Sometimes by clarifying the question the answer because easy to locate, or assimilate.

This was yet another adventure in programming that tested my problem solving abilitites, while also creating an awareness of blindspots, and syntax errors. The construction of my own projects is always for me the most effective way to practice, learn, and retain concepts through hands on exploration and bar far the most rewarding.

A demo of this cli gem can be viewed here:
https://youtu.be/5GZ8Ck5W3us





