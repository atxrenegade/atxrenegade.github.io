---
layout: post
title:      "Word Wrap Kata - Node.js & Ruby on Rails"
date:       2021-04-12 23:30:19 +0000
permalink:  word_wrap_kata_-_node_js_and_ruby_on_rails
---

----------------------------------------

January 20 2021

7 min read  
#nodejs #rails #wordwrap #codekata #webdev  
  

The Challenge
-------------

  

I was recently asked to complete a code challenge for a word wrap kata in Rails and/or Node.js. I think of myself as builder and designer first and foremost, code is my medium and any excuse to build something and I'll take it. To me a coder is someone who pumps out large volumes in assembly line fashion code. As a builder/maker/fullstack developer I'm interested in the whole product, and all the little moving pieces that go into it from the client side experience to the database design. I enjoy being able to move between back and frontend. It keeps me challenged, interested and engaged. It also ensures my continued progress, since if I reach a point that I feel stagnant or need a break from a particular problem, I can still make progress in another area of the application using a different part of my brain, and return fresh to the original problem. My favorite projects, are the projects I get to do both and this micro assignment gave a chance to do that.

The request was to build out a Word Wrap Kata. Word wrap similar to what you would expect in a word processor. The algorithm needed to take two user inputs: the specified column width, and a string to break. Now here was the catch, the instructions did NOT ask for Ruby and/or JS. They specifically mentioned RAILS and NODE.js, and a single class and method, indicating to me that the algorithm was to be included in some form of micro-application using these frameworks (if you can arguably call Node a framework without offending people). Obviously if this was an active work or client request, with deadlines I would have followed up to clarify, but since it was a code challenge I decided to tackle it as is. In the description the words "A class", followed by "a SINGLE method", reinforced in my mind for some reason that we were limited to use of A SINGLE class AND A SINGLE METHOD, in retrospect I think that weight on A CLASS was added by my interpretation and not the writer's intention, which really would have opened up a lot of other options for me, but at the same time limited scope. So it could have gone either way. Maybe because it was written in the context of a puzzle that I was seeing and expecting extra layers of complexity. Below were the full specs I was provided:

  

![Word Wrap, literally](img/blog/blog_post_7/word-wrap.jpg)

Word Wrap...literally.

  

Word Wrap - Full Specifications
-------------------------------

  

Write the following in Rails and/or Node:

*   ⦿ Write a class called Wrapper
*   ⦿ Wrapper has a single static function named wrap
*   ⦿ Wrap takes two arguments - a string, and a column number
*   ⦿ Wrap returns a string with line breaks inserted at just the right places  
    to make sure that no line is longer than the column number
*   ⦿ You try to break lines at word boundaries
*   ⦿ Break the line by replacing the last space in a line with a newline

  

![Word Wrap, literally](/img/blog/blog_post_7/christmas_word_wrap.jpg)

Word Wrap...again.

  
  

Version 1 - Node.js
-------------------

  

Since I have been writing a lot in Java and JS lately I decided to write the Node version first. I took the project specifications quite literally. Build a word wrap in Node.js, one class, one method. I couldn't rationalize using a database to persist wrapped strings in a single purpose micro app so I decided to implement it as a CLI, I guess I could have engineered it as an API, but my mind associates API with retrieving stored data as opposed to just manipulating it. Either way no DB meant, no server requests, no server side validations, kept it pretty basic. In keeping with the one class, one method theme, I used loops in the index file to prevent passing invalid data to my function. Without the use of conventional Node functionality it really just became a Javascript Node flavored command line application.

  

Version 2 - Ruby on Rails
-------------------------

  

Round two. Still stuck on the idea that these input strings are being parsed and processed but not persisted through a single class and method. Rails without a DB means Rails without Active Record. To me that's like icing without a cake, a lace without a shoe, an MVC without a DB. How does that even work? Build a Rails app with one class and one method. What would that even look like? How do I keep with CRUD and restful route conventions with no records? What does my model class inherit from if not active record? I mulled over it a bit, and decided to break it down as user input entered through my single NEW erb view page, passing user input to the controller to my class for processing, returning the result back through my controller and rendering the processed string on my show page without having to persist it. A single static method, meant I couldn't initialize instances of a model and not being able to inherit from active record which created some challenges I did not foresee. More on that later.

  
  

![](img/blog/blog_post_7/MVC.png)

Building an MVC without the DB

  
  

The Algorithm - first attempt
-----------------------------

  

So after a rough overview plan, I started on the program logic. I am not a hard math person. In college I was an AP English and Social Sciences student, majored in Psych then transferred into Business and Marketing. I DID start writing my first computer programs when I was 8, but I am definitely by no stretch a math whizz. I do like logic puzzles, I love strategizing, and I can spot patterns pretty easily, but the point is algorithmic solutions DO NOT jump off the page at me. I have to work at, and if sit with it long enough and beat my head against the wall a few times, I get there eventually. When I start a new project business logic or models is usually where I start, after a general overview, and build out from there. Sometimes I will draw it out with pen and paper. Sketch out my desired finished product then work out what models and logic I need to get there. I hard code an example, work on it in Repl.it until my base test case is working, then I try corner cases, and modify if necessary. I then remove hard coded input and params and create the dynamic version, and continue building. I wrote a first iteration of the algorithm in JS, then translated it to Ruby. Once I had a rough working version I looked at alternatives via google, some using regex, various forms of head and tails recursion. I even tested a few, but the solutions I tried did not handle corner cases well, or were maybe more elegant but not any shorter. I decided I needed to stop looking at it for awhile, and use what I had for now, and come back with fresh eyes.

  

![](/img/blog/blog_post_7/my_process.jpg)

Cursing is an essential part of the creative process

  
  

#### Challenges and Insight into Building in Node.js version

  

A couple of the questions and issue arose while constructing the cli app. Deciding on a convention for the file structure was a bit hazy, since there seems to be a lot of varying opinions regarding the standard node program structure and nothing in regards to a cli at all, that does not require many of the files an directories of a full scale node app. Next question was how to prevent invalid user data from passing to my function without adding another method or class. I mentioned before that I worked around this by creating loops in the index file until valid user input is entered, and imported the readline-sync module to capture user data from the command line. Overall, it was quick and dirty because there wasn't much to the application.

  

#### Challenges and Insight into Building in Rails

  

This was the fun part. Rails without ActiveRecord threw all kinds of loops, beginning with - what does your model class inherit from that will not require a database connection? Rumor has it that Rails 6 has a solution using inheritance from ActiveRecord Model, but I wasn't able to successfully implement this suggestion, and ended up using a bare class with no inheritance but that contained my static method and worked for this use case. Since I was limited to a static method and without class initialization, I could not initialize an instance to wrap my form\_for tags and had to resort to using standard html5 form tags. It also meant that I didn't have access to ActiveRecord data validations when persisting data. I had to come up with an alternate way to validate user input. I didn't want to clutter my controller with logic, or break the one class, one method rule I was still hanging on to, so I resorted to client-side validations with HTML5 to limit strings less than 10 chars and a regex pattern only allowing positive integers greater than zero. I went back and forth with this for a bit between JS and HTML5 solutions, and created a JS file to append errors messages to the DOM and eventually settled on the combination for the best of both worlds. I made sure to include strong params, and sanitize the processed user string returned by the controller by permitting only line breaks elements.

  

By far the time most time consuming and frustrating part of both exercises was configuring the asset pipeline. Every time I updated a dependency, added a new one, or edited a js file my app would break, or display pre-compiled file versions instead of current,and failed to update even after restarting the server. I reconfigured numerous assets and files half a dozen times before realizing that some of the directions I was following were Rails5 config, and others were for Rails6 config which were dramatically different, and often did not specify which rails version they were referring to. Rails6 asset pipeline, and the changes to include webpacker and sprockets since Rails5 is really worthy of it's own blog post. I'm going to have to sit with it, and go over my working solution again to fully comprehend what modifications worked. At one point I did discover I had overlooked missing quotations loading webpacker, which solved at least half the my issues. Rails has some great documentation about the asset pipeline to further explore for anyone interested. I was eventually able to get all my assets and dependencies playing nicely together and as intended. For more documentation about the asset pipeline: [https://guides.rubyonrails.org/asset\_pipeline.html](https://guides.rubyonrails.org/asset_pipeline.html)

  

![](/img/blog/blog_post_7/sprockets.jpg)

How I view the Asset Pipeline - Rails6 - Sprockets & Webpacker

  
  

Reworking the Algorithm - Node.js
---------------------------------

  

Once I built out my micro-apps, I returned to reworking my algorithm. After playing with it for a bit using various test cases, and being unsatisfied with the redundancy of my nearly identical first and final if statements nagging me, I knew it needed a re-work. I sat down and worked through it piece by piece, starting with parsing and breaking up large words of double and triple the column value, and continuing one till I had the desired result for even single character words. Then added the opening if statement to prevent duplication of line breaks. I used a column index to track the and iterate through the column characters, and array index to track the index in the word array. I was pretty happy with my working solution that handled all corner cases I could come up with and was written without any external input or influence.

  

The commented version of this can be viewed in the repo but with the use of a single color in code box below made it difficult to read so I have removed comments from this version to ironically improve readability.  
https://github.com/atxrenegade/kata_word_wrap_node  

  

  

    class Wrapper {
        static wrap(userStr, colNum) {
            var wordArr = userStr.split(" ");
            var newStr = "";
            var colIdx = 0;
            var arrIdx = 0;
            
            function breakWord(colNum, colIdx, arrIdx, word = null) {
                word = word || wordArr\[arrIdx\];

                if (word != null) var wordLen = word.length;
                
                if (colIdx > colNum || (colIdx + wordLen) > colNum) {
                    var testChar = newStr.slice(-1);
                    if (testChar != '\\n') newStr += '\\n';
                    colIdx = 0;
                }
            
                if (wordLen > colNum) {
                    newStr += \`${word.substring(0, colNum - 1)}-\\n\`;
                    colIdx = word.substring(colNum - 1).length;
                    word = word.substring(colNum - 1);
                } else if (wordLen <= colNum && word !=undefined) { 
                    newStr +=\`${word} \`; 
                    colIdx +=wordLen + 1; 
                    arrIdx++; word=wordArr\[arrIdx\]; 
                } else { 
                    return;
            }  
            if (arrIdx < wordArr.length) breakWord(colNum, colIdx, arrIdx, word){  
                function breakWord(colNum, colIdx, arrIdx); 
            } 
    } 
    module.exports = Wrapper;

                                

  
  

Reworking the Algorithm - Ruby
------------------------------

  

To create my ruby algorithm I used my Javascript solution as my base with the expectation that I could translate most of my solution without any major modifications. I then realized I would not be able to nested methods for a recursive version. To solve this I iterated through a word array using .each() enclosing a while loop nested inside of a an if statement to parse and break up words larger than the column value and passed the remaining piece to the following if statement to parse the remaining word segment to add the appropriate line breaks and white spaces.

Again comments have been removed and can be viewed in the repo.  
https://github.com/atxrenegade/kata\_word\_wrap\_rails  

  

    class Wrapper
        def self.wrap(col\_num, user\_str)
            word\_arr = user\_str.split(" ")
            new\_str = ""
            col\_idx = 0

            word\_arr.each do |word|
                if (col\_idx > col\_num || (col\_idx + word.length) > col\_num)
                    test\_char = new\_str.slice(-1)
                    if (test\_char != "\\n") then new\_str.concat("\\n") end
                    col\_idx = 0
                end

                if (word.length > col\_num)
                    while (word.length > col\_num) do
                        new\_str = new\_str + "#{word\[0...col\_num - 1\]}-\\n"
                        col\_index = word\[0...col\_num - 1\].length
                        word = word.slice(col\_num -1, word.length)
                    end
                end

                if (word.length <= col\_num) 
                    new\_str +="#{word} " 
                    col\_idx +=word.length + 1 
                end 
            end 
            puts(new\_str) 
        end 
    end

                                

  

What I Learned From This Code Challenge
---------------------------------------

  

Since throughout the past year I've been pretty immersed in learning Java, while working primarily with JS frontend and Rails as an API, it gave me a chance to review using Rails views again. The challenge of creating backend and full stack micro applications without the use of a database challenged me to think outside the box to explore alternate solutions rather than depending on familiar libraries. The exercise (my typo - really!) gave me an opportunity to take explore more about Rails6 webpacker vs Rails5 use of sprockets for asset loading and pre-compiling. It familiarized me with HTML validations which I had previously relied on JS and ActiveRecord for. In this case I think my over complication of a simple problem, limited my scope and forced me outside of my comfort zone to explore other options and reanalyze my knowledge of certain libraries and concepts that I usually take for granted.

I think that Kata's are great practice in logical thinking and algorithm implementation that every developer can benefit from, but I also think that nothing beats the experience of building out actual applications. Thousands of code katas will not teach you all the idiosyncrasies and real world issues that arise when it comes to deployment, dependency conflicts, asset pipelines, and the million other finicky details that aren't part of Hackerrank, Codewars, LeetCode, bootcamps or a CS program for that matter. This turned out to be a good practice for both.

  
  

![](img/blog/blog_post_7/coding_kata.png)

Coding Ninja Warrior

  
  

The repo for the Node.js CLI can be found [here!](https://github.com/atxrenegade/kata_word_wrap_node)  
The repo for the Rails MVC version can be found [here!](https://github.com/atxrenegade/kata_word_wrap_rails)

[atxrenegade/Harleigh Abel](https://github.com/atxrenegade)
