---
layout: post
title:      "Understanding JIT Comp, Scopes, Closures..."
date:       2021-04-14 01:22:02 +0000
permalink:  understanding_jit_comp_scopes_closures
---

...Hoisting, Shadowing, THIS & the Module Pattern   

#### Harleigh Abel

![Coding in Javascript](https://www.harleighabel.com/img/blog/javascript.jpg)

Software Development

JS Under the Hood - The Fuzzy Parts
-----------------------------------

June 2 2020

Understanding JIT Comp, Scopes, Closures, Hoisting, Shadowing, THIS & the Module Pattern
----------------------------------------------------------------------------------------

  
32 min read  
#javascript #html5 #SPA #responsive #webdev  
  

#### Some of the topics explored in this post:

  
*   ⚫  Understanding Just-In-Time Compilation
*   ⚫  Exploring Scopes, Closures, Hoisting, and Shadowing
*   ⚫  Comparing Variable Types and Function Types and Behavior
*   ⚫  Implicit vs Explicit - Rules of Precedence for determining THIS
*   ⚫  The Principle of Least Exposure (POLE)
*   ⚫  Review of the Classic Module Pattern and the ES Module Format

  

Correcting Distorted Mental Models
----------------------------------

  

After discovering some misguided mental models in my understanding of some of the more opaque idiosyncrasies of JavaScript, I was directed towards Kyle Simpson's infamous and invaluable series "You Don't Know JS" (specifically the 'Scope and Closures' volume). I highly recommend a thorough consumption of each of these books to any developer who plans to time working with the rapidly transforming language of JavaScript, for a solid grasp of some of these often misunderstood and miscommunicated concepts. I hope I can do it some justice in clarifying and communicating some of those concepts here.

I am a full picture person, I retain details best when an aerial view, a deep exploration of low level concepts. But this is the real world and in development we often don't get time for contemplation and review, so I savor it when I can. Here's my Reader Digest version of mulling over Kyle's book(3 x for good measure), and re-clarifying thorough Stack Overflow posts, YouTube videos, Wikipedia, Medium, Dev.to, GitHub repos, and a dozen of other sources. Some of my specific misconceptions I was looking to clarify were hoisting, the use vs banishment of Var, closures, and the mystical JIT compilation process. Much of what I discovered contradicted what I had originally been taught, but these new arguments were backed with compelling logic from experienced developers and convincing code samples that altered the way I view and use JS now. So let's dive in.

  
  

Compiled vs Interpreted
-----------------------

  

For machines to understand high level languages like JavaScript, the code must be translated or complied into to machine code. Compilation is a process in which the compiler converts higher level source code into a machine language executable file in a build step before runtime. Interpreted languages are translated at runtime line by line while the source code is converted to machine code during execution. There are arguments for and against each format. Compiled code is faster to execute because the build step occurs prior to runtime, the entire program has already been checked for errors and the work of converting to machine code is already complete. Interpreted code is more flexible to correct, it runs line by line, often displaying errors as they occur, and takes less memory.

But why do we care? Isn't JavaScript an interpreted language? It was...in the beginning, but modern day JavaScript engines use a refined combination of compilation and interpretation commonly referred to as just-in-time compilation (aka JIT compilation).Understanding the way our code is processed during compilation and execution will help us get a clearer understanding of scopes, variables and their behaviors, and of when these objects are determined and available for use in our code. We can use this knowledge to replace memorization of misleading, incomplete, and outdated mental models. Remember these are high level summaries of complex processes simplified for basic comprehension.

  
  

JS Engine and the Runtime Environment
-------------------------------------

  

There are two important components involved at runtime, the JavaScript engine, and the runtime environment that the engine operates within. Each browser uses it's own engine as they compete for speed and efficiency. You've probably heard of the popular ones, V8 for Google Chrome, Karma for Internet Explorer, Nitro for Safari and Firefox(Developer Edition - my personal preference) uses Spider Monkey. While JS can still be executed with an interpreter, it is more commonly processed by these browsers and their fine tuned engines using complex processes for asynchronously compiling, optimizing and executing source code through multiple threads within the runtime environment.

  

![Web Browser Logos](https://www.harleighabel.com/img/blog/js_browsers.png)

Web Browser Logos

  

So what is the runtime environment? The runtime environment, is the environment that your code is executed in. It is generally the collection of libraries, tools, and support systems available within the runtime space. Some of these tools and services include the server environment, your desktop environment, the browser, the JS engine, the core library, etc.. This also where the event loop is implemented.

  
  

Three Stage Compilation
-----------------------

  

Now that we understand that JS uses a combination of both compilation and interpretation known as "just-in-time compiling", we are going to take a closer look at the compilation phase to better understand scope and variable behavior. Again, compilation is preprocessing of source code into machine code. It is during this phase that the JS engine creates a map of scopes and identifiers that determine the availability of our functions and variables. In JavaScript, the compilation of our source code occurs literal nanoseconds before the engines executes the translated machine code, so in order to facilitate this process it is broken down into three stages:

*   Stage One: Tokenizing/Lexing
*   Stage Two: Parsing
*   Stage three: Code Generation

  

### Tokenizing/Lexing - Compilation - Stage 1

  

In the first stage of compilation, lexical analysis and tokenizing refers to the process in which the engine analyzes our syntax and breaks it down into 'tokens'. Tokens are just strings with assigned meanings that we recognize as identifiers, keywords, operators, literals, comments, etc.

JS engines separate the registration of variables and functions to their scope, from the value assignment of those objects into two distinctly different operations. During the lexing phase when our JS engine reaches an identifier in our code it determines whether the variable identifier qualifies as a TARGET or a SOURCE. A TARGET identifier points to variable. A TARGET identifier is like the sign pointing to the container storing our value, while the SOURCE is the bucket holding the actual value. Another analogy for it is the left hand side vs right hand side of an equation. The right hand side of an expression would be the TARGET, and the left hand side would refer to the SOURCE. When the engine reaches a TARGET during the lexing phase it will register the identifier to its scope, but if the identifier is determined to be a SOURCE it will defer value assignment until the execution phase. While scope is mapped out during compilation, it is not CREATED until runtime when a new instance of our scope is called or invoked.

Once a variable scope is set during compilation it cannot be altered at runtime. That being said, in programming there seem to be corner cases for everything. Some exceptions here include cases that a scope may not be 'set' during compilation when the identifier references a JS object that is defined in an external file or module and not available for 'lookup' until after these files are loaded at runtime. Another deviation from convention is that scope MAY be altered at runtime through the use of the WITH keyword or the eval() function in non-strict mode. These are both exceptions to the rules and the later is not good coding practice and should be avoided.

  

### Parsing and Code Generation - Compilation - Stages 2 & 3

  

Once the source code has been tokenized, the JS engine parses our token array to create an abstract syntax tree representation of our program(AST). The AST is a data structure that is an intermediate representation of our program prior to code generation. It tells our compiler what the structural details of our program will be. The final stage of compilation is the code generation stage when the AST is converted into machine instructions to be executed by the JS engine at runtime.

  

### The Execution Phase and the Event Loop

  

Once code has been compiled, it is ready to be executed. JavaScript is described as asynchronous, single threaded and non-blocking. So what does that mean exactly? While JS engines and environments use multi-threaded processes, the actual execution of our program is single threaded. This means that when our compiled code is executed each process is carried out to completion before moving on to the next line of code, so only one thread(process) is executed at a time. But isn't that blocking/synchronous in nature? Yes, single threaded processes are blocking and synchronous, and the JS language itself matches both of these characteristics. JavaScript has one memory heap (just as it sounds a heap of memory), and a single call stack (think of call stack as a stack of tasks that have to be executed in the order of first in, last out). The non blocking nature of our programs is a feature provided by the Runtime Environment.

There is recent evidence to suggest that the attention span of human beings has dropped to below that of a goldfish (so congrats if you've made it this far in this blog post). While goldfish have exhibited a measured attention span of 8 seconds, human beings have been clocking in at only 7 seconds. Imagine how synchronous blocking tasks would affect our audience and their user experience if every time they loaded a webpage, or used a feature, they had to wait for each task to fully execute before moving on. If you remember the days of dial-up this won't be a stretch for your imagination but in this day and age of user expectations it is unlikely they would stick around for long lags in our program execution.

I prefer to think of synchronous JS in terms of a Pizza Party. Imagine everyone was having a great time until it was time to order food. One friend is allocated the task of ordering and picking up the pizza for everyone at the local pizza joint. Designated friend decides they don't want to miss any of the fun, and insists that everybody and in the party stops what they are doing and waits exactly as they, are until the friend returns. Forty-five minutes later, friend returns with a stack of hot cheesy pizzas to an empty house. When the party goers got tired they left. You'd likely have the same experience with your users without the event loop to manage blocking tasks in your code.

The event loop is a model we use to describe how the environment manages events at runtime. When the JS engine is executing our code, the environment event loop separates tasks into two categories, those to be immediately executed and callback events. As our program is executed line-by-line non blocking scripts are sent to the execution stack and executed immediately in consecutive order, and when the js engine reaches a line of code containing a callback event, this event is placed in a separate event handler queue in the order that they these events occur. Once the JS engine has executed everything in the execution stack, it will begin processing the tasks waiting in the event queue. This allows for asynchronous event handling in our programs by allowing non blocking scripts to be executed before processing callback events. Again, this is high-level overview for a basic knowledge of these concepts and I recommend a deeper exploration into the JavaScript event loop for a better understanding.

  

![The Javascript Event Loop Model](https://www.harleighabel.com/img/blog/js_event_loop.gif)

The Javascript Event Loop, Image Credit: Sagore, Raul. Understanding Event Loop, Call Stack, Event & Job Queue in Javascript, 2 Jan. 2019, medium.com/@Rahulx1 understanding-event-loop-call-stack-event-job-queue-in-javascript-63dcd2c71ecd. Accessed 2 June 2020.

  
  

Lexical Scope and Scope Chain
-----------------------------

  

### What is scope?

  

Scope is like a container that holds our functions and variables. It has a defined boundary and we can nest one scope "container" within another. The scope "container" is the area of our program that a function or variable is visible within, it is the part of the program that these js objects can be referenced in. A variable or function's scope cannot leak into another scope, it is completely enclosed and each scope is fully nested within it's parent scope.

  

### What is lexical scoping?

  

Lexical scoping is a reference to scope that is determined during the lexing phase of compilation. As we learned when exploring how our JavaScript engine processes our code, during the compilation phase the engine uses the location of variable and function declarators to determine scope in relation to other JS objects and parts of our program. Scope is not generally modifiable at runtime, and references to our functions and variables are legal as long as they are used within the same scope that a declarator was made or within an enclosing parent scope.

  

### What is scope chain?

  

The scope chain is the relationship scopes have each other. Scopes are nested one within another fully enclosed forming a chain of scopes similar to the layers of an onion, or a set of russian nesting dolls. This scope chain created at compile time is how the interpreter connects an identifier with the declaration that informs the interpreter of what that identifier's scope will be. As we learned in the compilation section, the compiler determines scope during the lexing phase while the interpreter handles assignment during execution.

When an interpreter reaches an identifier, it searches the scope level it is in for a declaration matching that identifier. If no identifier is found within the current scope, it moves up and outward to the parent scopes to search for a match. The scope chain is one directional, and ALWAYS moves only in an outward and upward direction, from innermost to outermost scope. As the interpreter continues its search for a matching declaration, it continues through each scope level until it reaches the global scope. The interpreter cannot assign a value to an undeclared variable or function, and will throw an error, or create a an accidental variable depending on the mode the function is running in. If our function or program is operating in strict mode and the interpreter does not find a match, a global variable is created. If we are running in strict mode, a reference error will be thrown.

  

![Russian Nesting Dolls](https://www.harleighabel.com/img/blog/russian-nesting-dolls.jpg)

Russian dolls nested one within another as a visual representation of JavaScript scopes

  

### Levels of scope

  

Prior to ECMAScript 2015 we were limited to two levels of scope within our programs: the Global scope, and the Function scope. Block scoping did not become available until Let and Const were introduced in ES6.

  

### Global Scope

  

Conceptually global scope is the outermost scope in a JavaScript program. It is where JS exposes built-ins, the DOM, the console, and environment features like setTimeout(). Prior to ES modules, the global scope was often the glue between separate JS files, and created a space for them to cooperate. The global scope varies in definition and behavior depending on the JavaScript environment it is run in, and the actual outermost scope may not always be the global scope we are expecting. It's important to familiarize yourself with what the outermost scope in for the environment you are working in to avoid being tripped up by assumptions. Functions and variables defined within the global scope are accessible throughout the program. Even though defining global objects allows for easy access to our functions and variables it is bad practice to crowd our namespace with global variables, and we will better understand why narrower scopes are recommended, when we review the Principle of Least Exposure.

  

### Local Scopes - Function, Block, and Implied Scope

  

Within the global scope our local scopes will be nested, the most common forms of local scope are Function Scopes, and Block Scopes.

Local scope is a reference to local accessibility to an object or variable. Local scopes are nested within the global scope and can be of either function scope or a block scope type. Variables and functions declared within our local scope will only be visible within this local container. Function scopes refer specifically to objects that are only accessible from the function level inwards and to the nested children of the current function scope.

Block scopes were introduced in 2015 with the creation of Let and Const variable types. Though syntactically there are many cases that we use curly braces in our JS programs, these code blocks do not qualify as block scopes until a variable to be contained is declared within them. Object literals, function declarations, try...catch statements, and classes are a few examples of blocks that do not qualify as block scopes. It's important to remember never to define functions within blocks, because they will create unpredictable behavior that will vary across JS environments. Block scoping is useful for increasing code readability, narrowing our variable scopes, and reducing the potential for Temporal Dead Zone errors which we will explore shortly.

  

    // levels of scope

    var globalVariable = "This is a Global variable, declared outside of all blocks and functions, in the global
    namespace."

    function printMyScopeMessages(){
        var functionScoped = "I am function scoped. I'm local to the function scope."
        if (typeof functionScoped === 'string') {
            let blockScoped = "I am blocked scoped, and I'm a mutable local variable."
            const anotherBlockScope = "I'm block scoped, and I'm a fixed local variable."
        }
        console.log(blockScoped);
        console.log(anotherBlockScope);
    }
        console.log(functionScoped);
        console.log(globalVariable);
    }

    printMyScopeMessages();

    $=>
    I am blocked scoped, and Im a mutable local variable.
    I block scoped, and Im a fixed local variable.
    I am function scoped. Im local to the function scope.
    This is a Global variable, declared outside of all blocks and functions, in the global namespace.
    
                            

  

Less commonly familiar are implied scopes. Scopes that have been implicitly created that may cause strange bugs and unexpected side effects if we are not aware of their creation. One implied scope to watch out for is the parameter scope created when when we use non simple parameters as function arguments. Since default arguments classify as non-simple arguments we can unintentionally create a parameter scope nested between a function's parent scope and the function's inner scope which could lead to unexpected variable shadowing. Another potential implied scope is the nested scope created between by the name identifier of a function expression nested between function's inner scope and the expression's outer enclosing scope. It is important to be aware of how these implied scopes may be created to control any side effects they may have in our programs.

Now that have a clear understanding of when JavaScript determines scope, how to use different types of scope, and how different variable types behave, we can start exploring these concepts will help us construct better programs.

  
  

Principle of Least Exposure
---------------------------

  

The Principle of Least Exposure(POLE) is a variation(or extension?)of the POLP principle (The principle of Least Privilege). It argues that function and variable access should be restricted to only to the parts of the program for which it is an absolute necessity to complete the work that that part, was designed to execute. Using encapsulation we can limit exposure creating better code organization, more secure programs, isolation of vulnerabilities, and easier updates. We implement the POLE principle by nesting our variables and functions in the smallest scope available. We use functions, blocks and IIFE's (Immediately Invoked Function Expressions) to narrow scope resulting in greater program stability, reducing the chance of naming collisions, unintended dependency, or unexpected side effects.

Since our quick journey into how the JS engine compiles and interprets our program, we know that scope is determined during the lexing phase of the compilation stage. We've reviewed what types of variables that can be created, and what levels of scope are available. Now lets look at how our variable types determine what scope the compiler will create for our variables.

  
  

Scope and Variable Types
------------------------

  

### Reintroducing Var

  

As a new JS developer I was also steered away from the use of the Var variable type, and really didn't understand it myself. I "knew" that it was labelled legacy code, the black sheep in the JS family that insisted on creating unpredictable bugs through a behavior called "hoisting". Beyond that I didn't really understand it, and was instructed to stick with Const for all variable instances with the occasional use of let. I personally, found Const to be unpredictable and deceiving, claiming to be immutable yet still alterable, so steering clear of the trickery and deceit of both Const and Var, I defaulted to seeming safe solution of the neutral Let variable type for nearly every variable I declared. Gaining a better understand of how these variable types function has completely changed how and what variable declarators I use but before we get into the behavioral differences, let's review the basics.

Let me re-introduce you to Var perhaps you also got off on the wrong foot. Var was the original variable declarator, reliable and time tested, and up until ES6 was the only flavor of JS variable declarators came in. Var variables attach to the nearest function scope to create function scoped variables. Var is a visible differentiator from Let and Const in programs helping us visually separating function and block scoped variables from each other at a glance. Re-declaration of Var within the same scope has zero effect, making it a useful variable as a semantic reminder when there is a distance between our original Var declaration and the variable assignment. Var is also the preferred declarator for global variables, and for declaring a variable inside a loop that also also needs to be accessed externally without creating a separation between declaration and use. In short, we like Var. Var rocks. Var is not broken or dysfunctional. Var is specifically useful for declaring function-scoped variables.

  

### Reviewing Let

  

The let keyword acts very similarly to var. Let is also used to declare mutable variables and attaches to the nearest block scope. Let was introduced with Const in ES6 prior to that block scoping in JS did not exist. On to Const.

  

### Downgrading Const

  

Const is reserved for declaring static variables and cannot be reassigned. Due to Const's inflexibility it is best used for variables like fixed strings, or numbers. Because const cannot be reassigned, it cannot be declared without assignment, attempting to do so will result in a Syntax Error. Like Let, Const is also a block scoped variable.

  
  

Hoisting and the Dead Zone
--------------------------

  

### What is hoisting?

  

HOISTING, another JS concept shrouded with mystery and confusion. The non programming definition of "hoisting" is simply the raising or elevating of one thing over another. In the JavaScript context, "hoisting" refers to the fact that all variable declarations are visible at the top of the scope they are declared in. So how does that reflect in our programs? Well, it depends on the variable type and this is where we observe behavioral differences between Var, Let and Const.

There are three steps in the creation of a function or variable, these are declaration, initialization and value assignment. Since our identifiers are registered in the compilation phase our scopes have been predetermined and at runtime the engine already has already processed what our scope will be but still has not necessarily executed the lines of code determining these object values.

Hoisting refers the to registration of a variable identifier at the top of the scope it is declared in. If we look back at how and when our js engines determine variable and function scope we will remember that the registration of variables occurs during compilation, and the value assignment occurs during the execution phase. After a JS object has been declared, it must be initialized before it can be used and assigned a value, otherwise it will result in error, or return a different value that we are expecting.

Variables declared with the keyword Var will be registered, and immediately auto-initialized and assigned a default value of 'undefined'. This means they are available for immediate use in our programs, albeit they will not reflect our intended value until the engine reaches the line of code that contains our variable value assignment.

If the variable is declared using Let or Const, the variable is registered, but NOT auto-initialized and NOT assigned a value until the value assignment line of code is reached. Even though the engine has mapped the scope for these variables attempting to use them before value assignment will throw an error. Unknowingly you have crossed into the DEAD ZONE! (♫♪♪ dah, dah, dah - morbid organ music playing ominously ♫♪♪).

The TEMPORAL DEAD ZONE! (cackles and screams in the distance)

  

![Dead Zone Logo as representation of The Temporal Dead Zone](https://www.harleighabel.com/img/blog/deadzonetitle.jpg)

Stephen King's infamous novel The Dead Zone as a reminder of the Temporal Dead Zone(TDZ)

  

### The TEMPORAL DEAD ZONE

  

What is the heck is the Temporal Dead Zone?  
In simplest terms the TDZ is the time period between a variable or function declaration and the when it is auto-initialized and available for use. To prevent Temporal Dead Zone errors we declare all variables at the top of their scope this prevents any observable time gap between variable declaration, auto-initialization, value assignment and use.

  

    // The effect of VARIABLE HOISTING when attempting use
    // an uninitialized variable

    console.log(varVariable);
    var varVariable = 'Im a var!';

    $=>
    undefined
    

    console.log(letVariable);
    let letVariable = 'Im a let!';

    $=>
    Uncaught ReferenceError: can't access lexical declaration 'letVariable' before initialization
    

    console.log(constVariable);
    const constVariable = 'Im a const';

    $=>
    Uncaught ReferenceError: can't access lexical declaration 'constVariable' before initialization
    

                            

  

This fresh perspective on Var, and a clear understanding of hoisting has definitely opened my eyes to the logic and reasoning behind it's intended use and Var has become quickly become my preferred function scoped variable declarator.

  

### Variable Shadowing

  

Variable shadowing is the legal process of eclipsing the value of a variable declared within an enclosing scope, by declaring a variable of the same name in an inner scope. It is the legal action of maintaining two variables with the same name with different values in different scopes. Due to lexical scoping the inner variable blocks the visibility of the outer variable. Because the compiler will search for the first declarator matching the variable identifier, it will locate and match the inner scope declaration and terminate the search once the closest declarator has been found. This conceals the original outer variable value from that point in scope inward and blocks access to it. (Side Note: This is with the exception of Var variables and function declarations made within the global scope. They will still be accessible through the automatic property global.variableName, but it is NOT recommended and can introduce bugs and create confusion in our code.)

Var cannot legally shadow a Let variable, this would require a scope boundary crossing that JS won't allow. However Let as the inner scope can ALWAYS shadow Var as the outer scope.

  

    // legal variable shadowing

    var shadowedVariable = "I'm the original unshadowed value!";

    function shadowOuterScopeVariable(someParameters){
        var shadowedVariable = "Now I'm Shadowed!";
        console.log('Here are some parameters: ' + someParameters);
        console.log(shadowedVariable);
    }

    console.log(shadowedVariable);
    shadowOuterScopeVariable('abcdefg');

    $=>
    I'm the original unshadowed value!
    Here are some parameters: abcdefg
    Now I'm Shadowed!
    

                            

  

I can't think of many reasons why you would want to intentionally use variable shadowing. It could be helpful during testing, making changes to legacy code or for concealing data from a public interface, but in general, its important to recognize to prevent unintentional shadowing. It makes far more sense to create a new variable with a different name if you are allocating a different value, name sharing is a bad practice unless you have a good reason for it.

  

![Panorama of an eclipse representing variable shadowing](https://www.harleighabel.com/img/blog/eclipse-panaromic.jpg)

Panorama of eclipse as a visual representation of variable shadowing

  
  

Function Types and Behaviors
----------------------------

  

Functions are named reusable code blocks that allow us to perform specific tasks multiple times throughout a program by invoking these code blocks by name. They allow us to break down our code by separation of concerns into manageable modular pieces, and encapsulate and conceal the data within them. There are several ways to declare and define functions, and different declaration types have subtle behavioral differences.

  

### Function Declarations

  

Function declarations are the conventional method of declaring and defining a regular named function. They are constructed of the function keyword, a name for future reference and invocation, parentheses for any arguments to be passed in, and a set of curly braces to contain the code block. Similarly to Var variables functions declared with function declarations are immediately auto-initialized and assigned a value. Instead of being assigned a value of 'undefined' like in the case of Var declarations, the function is initialized with it's full function definition and available for immediate use at the top of the scope it is declared in, even before the engine reaches the function declaration within our code. Just like variable identifiers, function identifiers are also registered during compilation. ALL OTHER function types will have it's identifier registered as well, BUT will be auto-initialized to 'undefined' and WILL throw a typeError if we attempt to use them before the line of code that they have been declared and defined on.

  

### Function Expressions

  

Function expressions are another common declaration form with several variations. Examples include: named expressions, unnamed expression and IIFEs. A function expression consists of a (usually) anonymous function definition assigned to a variable. The primary behavioral difference between a function expression and a function declaration is that the definition of these functions is not auto-initialized and they will act much like a Let or Const variable if referenced before the line in our code that includes the function definition. Function expressions can be explicitly named or the name can be inferred. Named function expressions are just function expressions that include a name identifier for the anonymous portion of the expression. Function expressions that rely on name inference often show up in stack traces as anonymous. Function expressions are invoked by calling the variable name.

IIFEs, Immediately Invoked Function Expressions are exactly as they sound. They are (named or unnamed) function expressions that are immediately self invoked. IIFE's were used for data encapsulation and as part of the classic module format before built in modules were created. They can create a scope to hide variables and functions and be used encapsulate data. IIFE's are not recommended for use with scopes that contain BREAK, RETURN, CONTINUE or THIS keywords, because their use may alter function boundaries and meanings.

  

### Anonymous Functions

  

Anonymous functions are any function declared without an identifier, arrow functions classify as anonymous functions as do unnamed function expressions. Without storing these functions in a variable we have no way to reference them, they do not show up in our stack traces with an identifiable name, and being nameless are incapable of self reference. Best practices steer us away from the use of anonymous functions, though they may save keystrokes in the short term, in the long run they can be vague and create confusion while debugging.

  

### Arrow Functions

  

Arrow functions were the most popular feature introduced in ES6. My personal opinion, if you've ever noticed my function and variable names, is that I prefer clarity over shortcuts. When I first used arrow functions throughout my programs I did not like the lack of visible separation it created between variables and functions in my code. When I look at my programs I want it to be clear at GLANCE what type of JS object I am looking at and what task that line of code performs. Staring at a sea of commingled variables and arrow functions, in my opinion creates unnecessary visual noise.

Arrow functions are always anonymous and inherit arguments from their parent scope. Many developers believe they were created as a shorthand for function declarations, but the true value of arrow functions is that they do not create their own THIS execution context and their binding will be inherited from the enclosing scope. They are an invaluable tool for replacing the use of _var self = this_, or _.bind(this)._ They cannot override lexical bindings, EVEN when the NEW constructor is used. And lastly arrow functions can even serve as a great shorthand alternative for simple, inline functions like callbacks, timers, and event handlers.

The syntax for arrow functions depends on the number of parameters and the length of your function statement. With a single function statement we can leave off the curly braces and the return value becomes implied. If we use only one argument in our function we can simplify our code even further by excluding the parentheses used in a standard function. Multi-line statements in arrow functions require the use of curly braces, and any time the curly braces are included the return value needs to be explicitly stated. If we use any quantity of OTHER than one (including zero) parentheses are needed for our argument parameters. Below are examples of quirky syntax variations for arrow functions.

  

    // arrow function syntax

    // no parameters simple single statement
    // implicit return
    var returnTotalNoParams = () => 'Your total is $3.67';
    returnTotalNoParams();
    $=>
    'Your total is $3.67'
    

    // on parameter simple single statement
    // implicit return
    var returnTotalOneParam = price => \`Your total is $${price}\`;
    returnTotalOneParam(4.23);
    $=>
    'Your total is $4.23'
    

    // two parameters simple single statement
    // implicit return
    var returnTotalTwoParams = (price, name) => \`${name}, your total is $${price} \`;
    returnTotalTwoParams(5.87, 'Harleigh');
    $=>
    'Harleigh, your total is $5.87'
    

    // two parameters with curly braces
    // explicit return
    var returnTotalExpReturn = (price, name) => { return ('\`${name}, your total is $${price} \`;') };
    returnTotalExpReturn(6.23, 'Harleigh')
    $=>
    'Your total is $6.23'
    

                                

  

### Function Return Values - Implicit vs Explicit

  

The only time a JavaScript function implicitly returns a value is when the object created is returned using the NEW keyword constructor, or with the use of a single statement arrow function without curly braces. In all other cases return values MUST be explicitly expressed in our functions to return a value.

  

### Function Hoisting

  

While using variables before their declarations in not a recommended practice, with functions that is not necessarily the case. Moving our executable code to the top of our scopes (below variable declarations) can create visual clarity and a visible separation between function calls and function definitions. We can quickly and easily identify what functions are being invoked and look for the definitions below if we need more clarification. Of course this only works for with functions that have been declared with a traditional function declarations. As we discussed the function definitions will be undefined for other function types before their definitions and will throw an error.

Below I have included the same function refactored a number of times to display the syntax for each of the above described function formats.

  

    // FUNCTION TYPES

    // function declaration

    function manageTableTotals() {
        var numsArray = filterListForSelected(collectCheckedBoxes());
        updateDOMTotalPrice(totalPrice(numsArray));
        updateDOMItemCount(countItems(numsArray));
        toggleTaxes(CACHE);
    }

    // function expression(named)

    var manageTableTotals = function getPriceAndCount(){
        var numsArray = filterListForSelected(collectCheckedBoxes());
        updateDOMTotalPrice(totalPrice(numsArray));
        updateDOMItemCount(countItems(numsArray));
        toggleTaxes(CACHE);
    }

    // function expression(unnamed)

    var manageTableTotals = function(){
        var numsArray = filterListForSelected(collectCheckedBoxes());
        updateDOMTotalPrice(totalPrice(numsArray));
        updateDOMItemCount(countItems(numsArray));
        toggleTaxes(CACHE);
    }

    // anonymous function
    function(){
        var numsArray = filterListForSelected(collectCheckedBoxes());
        updateDOMTotalPrice(totalPrice(numsArray));
        updateDOMItemCount(countItems(numsArray));
        toggleTaxes(CACHE);
    }

    // arrow function
    var manageTableTotals = () => {
        var numsArray = filterListForSelected(collectCheckedBoxes());
        updateDOMTotalPrice(totalPrice(numsArray));
        updateDOMItemCount(countItems(numsArray));
        toggleTaxes(CACHE);
    }

    // immediately invoked function expression
    var addButtonListeners = (function() {
        var numsArray = filterListForSelected(collectCheckedBoxes());
        updateDOMTotalPrice(totalPrice(numsArray));
        updateDOMItemCount(countItems(numsArray));
        toggleTaxes(CACHE);
    }());

                            

  
  

This, This, This or This
------------------------

  

The use of the keyword THIS is a concept that often trips up and mystifies new developers. The common misconception is that THIS is a reference to the function itself, OR to the lexical scope of the function being called on the THIS object. But THIS is actually a runtime binding determined by the call site that creates an execution context for the life of that THIS instance. In other words THIS is kind of like a post-it note we stick to a JS object to let our program know what THIS is referring to. In order to determine what THIS is referring to, we need to examine the point in our code where our THIS was attached to our object, aka the call site. There are multiple ways to configure our THIS binding, and since more that one determining variation can appear at the same call site, we need to recognize and understand the hierarchy that applies to the THIS binding rules. In order from highest precedence to lowest precedence, the four rules for determining what our THIS binding context is are:

*   ① The NEW constructor keyword
*   ② explicit binding
*   ③ implicit binding
*   ④ default binding

Let's explore how each of these execution contexts are determined and the syntax used to implement them.

  

### THIS by Default

  

Starting from the bottom up, the default rule is in effect when none of the other cases apply to our call site. If our THIS instance is not using implicit or explicit binding, or the NEW constructor keyword, then the default THIS behavior will be determined by whether or not the CONTENTS of the function are running in strict mode. If strict mode has not been applied to our function then our THIS binding will refer to the global object. If our function IS running in strict mode then the global object becomes unavailable and the value of THIS is set to 'undefined'.

  

    // default THIS

    console.log(this);
    $=>
    Window
    

    'use strict';
    console.log(this);
    $=>
    undefined
    

                            

  

### Implicitly THIS

  

Implicit execution contexts are determined the same way the dot operator function call operates. When we call a function using the dot operator we look to the left of the dot to determine what object we are invoking the function on. In the case of implicit binding to the THIS keyword, we determine ownership of the execution context by investigating the call-site in the same way. In more complex cases where multiple properties are chained together only the last level, the final property, is the determinant.

Word of caution with the use of implicit bindings. This binding type easily falls back to the default object and you may find it unintentionally resets when it loses it implicit binding. We can prevent this by creating hard bindings explicity setting the execution context with the bind() function.

  

    // implicit THIS

    // Create a function to demonstrate our IMPLICIT THIS context
    function printSalesMessage(transaction){
        var message1 = \`Hello ${this.name}, that will be $${transaction.price} ${this.currency}.\\n\`;
        var message2 = \`Will you be paying for your ${transaction.item} with ${this.paymentForm}?\`;
        console.log(message1 + message2);
    }

    // Define parameters to pass in to function
    var transaction9585 = { price: 2.34, item: 'dive mask' };

    // Define the object we will use for our THIS context
    var user1 = { name: 'Anita', currency: 'gbp', paymentForm: 'cash on delivery' };

    // Call our function on our IMPLICIT THIS object
    // Look to the left of the function for the subject
    // of THIS context
    user1.printSalesMessage(transaction9585);

    $=>
    Hello Anita, that will be $2.34 gbp.
    Will you be paying for your dive mask with cash on delivery?
    

                            

  

### Explicitly THIS - Bind, Call and Apply

  

THIS can be explicitly set to the object of our choice by using one of three popular methods: bind(), call(), or apply(). Each of these important tools work similarly with slight variations in parameter expectations and return values. The first parameter passed into each of these functions will be the object we intend to explicitly set as our execution context. If you decide to pass in a primitive value (such a string or integer), the value will be auto-boxed to create the object parameter the function expects. Auto-boxing is when we pass a primitive data type like an integer or a string into a method that is expecting and object and it automatically 'wraps' our argument in an object for us.

These functions are useful when we want to create methods that can be used on different objects, they give us the ability to specify what context we are binding the original method to, the initial arguments passed in upon invocation, and the option to create a brand new method bound to our object through hard bindings without mutating the original object or function.

The call() method takes two or more parameters as an argument list. The first parameter is the THIS context we are explicitly binding to the call method, and the remaining parameters are the arguments we would like to pass into the call() method. It then immediately invokes the function being called with these arguments.

The apply() method is virtually identical to the call() method and is also used to explicitly bind our execution context to our object, and to pass arguments to a function being executing on the specified object. The ONLY difference between call() and apply(), is that instead of accepting multiple parameters for additional arguments, the apply() method takes this additional arguments as a single array; The return values for call and apply are the result of the method called on our bound object with the specified parameters.

The bind() method is a another close relative of call() and apply(). It is also used to explicitly bind our THIS context and the first parameter is again our intended object for execution context, and the following parameters are again, a list of arguments we are passing to the function being invoked. However instead of being invoked immediately as with call() and apply(), a new function is created and returned with the provided parameters hard coded into the newly created function(aka as 'hard binding'). The parameter argument list is passed into the new function before any new arguments are passed in at execution time.

  

    /\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\* apply() \*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

    // object + array .apply()

    // First we define our function that we would like to
    // bind our apply() object to

    function printSalesMessage(price, item){
        var message1 = \`Hello ${this.name}, that will be $${price} ${this.currency}.\\n\`;
        var message2 = \`Will you be paying for you ${item} with ${this.paymentForm}?\`;
        console.log(message1 + message2);
    }

    // Create the object that will be your THIS context
    // for the bind function apply()
    var user2 = { name: 'Josaia', currency: 'cad', paymentForm: 'PayPal'}


    // lets nest our argument array in a function so we know our .apply()
    // isn't just using lexical scope to reference these array values
    function returnParams(){
        var price = 2.34;
        var item = 'dive mask';
        return transactionDetails = \[ price, item \];
    }
    var transactionDetails = returnParams();

    // Let's see that array
    console.log(transactionDetails);
    $=>
    \[ 2.34, 'dive mask' \]
    

    // Now we will invoke apply() with our user object to bind as
    // our THIS context to as first parameter
    // and pass in remaining arguments array as our second parameter
    printSalesMessage.apply(user2, transactionDetails);

    $=>
    Hello Josaia, that will be $2.34 cad.
    Will you be paying for you dive mask with PayPal?
    

    /\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\* call() \*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

    // object + object .call()

    // First we define our function that we would like to
    // bind our call() object to

    function printSalesMessage(){
        var message1 = \`Hello ${this.name}, that will be $${transaction.price} ${this.currency}.\\n\`;
        var message2 = \`Will you be paying for your ${item.name} with ${this.paymentForm}?\`;
        console.log(message1 + message2);
    }

    // create the object we are binding to for our THIS context
    var user1 = { name: 'Harleigh', currency: 'mxd', paymentForm: 'credit card'}

    // define multiple parameters
    var transaction = { price: 2.34 }
    var item = { name: 'dive mask' };

    // invoke the call() with our user object to bind to as first parameter
    // pass in remaining arguments
    printSalesMessage.call(user1, transaction, item); //call

    Hello Harleigh, that will be $2.34 mxd.
    Will you be paying for your dive mask with credit card?
    


    /\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\* bind() \*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

    // Here is our original function we will bind our user object to
    // and pass in transaction object as a parameter

    function printSalesMessage(transaction){
        var message1 = \`Hello ${this.name}, that will be $${transaction.price} ${this.currency}.\\n\`;
        var message2 = \`Will you be paying for your ${transaction.item} with ${this.paymentForm}?\`;
        console.log(message1 + message2);
    }

    // Create the object that will be your THIS context
    // for the bind function call()
    var user3 = { name: 'Katie', currency: 'usd', paymentForm: 'debit' };

    // Create the argument parameters you are passing with your bound object
    var transaction = { price: 2.34, item: 'dive mask' };

    // create a variable to store our new function
    // pass in object to bind to and function parameters

    var katieSaleMsg = printSalesMessage.bind(user3, transaction);

    // execute the newly created function

    katieSaleMsg();
    $=>
    Hello Katie, that will be $2.34 usd.
    Will you be paying for you dive mask with debit?
    
                            

  

### A brand NEW THIS

  

The easiest rule to identify is the use of the NEW constructor. The NEW keyword creates a new javascript object that THIS binds to for the duration of the constructor call. These calls will return our newly created objects automatically unless another return value has been specified.

  

    // using the new constructor with the THIS keyword
    // to specify context
    function User(name, currency, paymentForm) {
        this.name = name;
        this.currency = currency;
        this.paymentForm = paymentForm;

        this.msg = function printSalesMessage(transaction){
            var message1 = \`Hello ${this.name}, that will be $${transaction.price} ${this.currency}.\\n\`;
            var message2 = \`Will you be paying for your ${transaction.item} with ${this.paymentForm}?\`;
            console.log(message1 + message2);
        }
    }

    // create our object to bind THIS to using the new constructor
    var user1 = new User('Harleigh', 'mxd', 'credit card')

    // call a method on our new object bound to THIS
    var transaction1228 = {price: 2.34, item: 'dive mask'};
    user1.msg(transaction1228)

                            

  

To review the order of rules determining precedence for our THIS binding from highest to lowest:

*   ① The NEW constructor keyword
*   ② EXPLICIT binding - using bind(), call(), or apply() to set our THIS context
*   ③ IMPLICIT binding - look to the left to determine ownership
*   ④ DEFAULT binding - global object or undefined (strict vs non-strict mode)

  

Closures
--------

  

Closures are another foggy concept for many new developers, and can be a bit to wrap your head around at first. They seem to evade our traditional concepts of variable scope and lifecycle. Simply put closures are a way to maintain state and access variables from an external scope after the initial function call has completed. They can be used to separate methods and functions into private and public interfaces through encapsulation. Using closures narrows our function scope, creates code that is easier to read, and increases efficiency by allowing us to maintain state of a variable instead of having to recompute it for every instance, or alternatively having to keep track of, preserve and pass in a particular piece of data.

Closures are a feature only available to functions. They are created by nesting one function within another, then by passing the nested function a variable and exposing this preserved data to an external scope in a return statement.

Closures can only truly be defined as closures if they can be observed, which means that the variable maintaining state must be accessed from an external scope in order to create the intended and observable effect. Closures provide direct access to manipulate the variable itself, as they provide us access by reference not by value as commonly misinterpreted.

Kyle Simpson's mental model in "You Dont Know JS: Scopes and Closure" most accurately describes closures as "a function instance and its scope environment preserved in-place while any references or variables to it are passed around and invoked from other scopes."

  

    // example of closures

    var firstNum = 1;
    var secondNum = 2;
    var thirdNum = 3;
    var fourthNum = 4;

    function outerFunction(someArgs, someNum) {
        function innerFunction(x){
            console.log(someArgs);
            return x + someNum;
        }
        return innerFunction;
    }

    var variableEqualsFOUR = outerFunction('Hello, Gorgeous!', firstNum)

    var variableEqualsSIX = outerFunction('Hey, Smarty Pants!', secondNum)

    console.log(\`This variable will hold the accumulated value: \` + variableEqualsFOUR(thirdNum));

    console.log(\`This variable will hold the accumulated value: \` + variableEqualsSIX(fourthNum));

    $=>
    Hello, Gorgeous!
    This variable will hold the accumulated value: 4
    Hey, Smarty Pants!
    This variable will hold the accumulated value: 6
    

                            

  

Quick review: outer function, nested inner function, variable passed from outer function to inner function to maintain state, inner function exposes variable through return statement, scope external to outer function accesses inner data and variables.

So now that we've explored closures let's move on to modules.

  
  

![cartoon of programming cat](https://www.harleighabel.com/img/blog/kitty_programming.png)

Image Credit: Artist Unknown. Cartoon Cat Programming at Night, 248006.selcdn.ru/main/iblock/910/910ad0a8b7cacf41f92ab846a684edf5/efa84859bb8636e96d68f51add2f96c4.png. Accessed 2 June 2020.

  

ES Modules and the Module Pattern
---------------------------------

  

In early days of web programming, when the internet still consisted of primarily static web pages, JS code was limited to single lines embedded in HTML to assemble web components. As it has slowly taken on a new role and expanded functionality to both front and back-end development, and non-browser applications, javascript programs have increased in size and complexity, and with it the demand for a consistent load pattern for the use of multiple js files and resources within the same program. As our JS files expand in size breaking code down into modules allows us to organize our code into smaller modular pieces, making programs easier to read, scale, debug, reuse components, implement a separation of concerns, and honor the privilege of least exposure, by encapsulating variables and methods into public and private interfaces and smaller manageable pieces.

  

### The Classic Module Pattern

  

Prior to ES6, Javascript did not have built-in modules, and solutions such as the use of an IIFE, UMDs(Universal Module Definition), AMDs(Asynchronous Module Definition), the Common JS pattern, and module bundlers evolved out of necessity. Before the creation of native JS modules we had to rely on enclosing functions and closures using the classic module pattern to group similar functions, retain state with within them, and to control access to private user interfaces.

In order a for module to fit the characteristics of the classic module pattern Kyle Simpson(You Don't Know JS) reminds us that it display the following characteristics:

*   ①"An outer scope from that runs at least once,
*   ② An inner scope that has at least one piece of hidden information
*   ③ Must return a reference to at least one function that has closure over a hidden module state"

Below is a syntax example I have created of the classic module pattern from my experimental app for scopes, modules and closures. It includes the use of a IIFE that returns a public interface through its inner function by using closure to enable the return object keys store stateful data.

  
  

    const CACHE = (function setCACHE(data) {
        var cache = {};
        function cacheData(data, cacheName) {
            if (data) { cache\[cacheName\] = data };
            return cache\[cacheName\];
        };

        return {
            totalPrice: function (data) { return cacheData(data, 'totalPrice') },
            taxRate: function (data) { return cacheData(data, 'taxRate') },
            element: {
                price: document.getElementById('cost-num')
            }
        }
    })();

    CACHE.totalPrice(2.33);

                            

  
  

### ES Modules

  

Since ES built-in modules were introduced in ES6 they have slowly found support across the browser landscape as they become the standardized native format for javascript development. As of 2020 the only browsers still lagging in adopting ES modules are: Opera, Internet Explorer, and a few less commonly known and used outliers.

ESMs are fairly simple and include three lines of code to implement: an export statement in the file containing the module to be exported, an import statement in the top level js file that we are importing to, and a module script statement to be included in the HTML file. ES Modules do not require Immediately Invoked Function Expressions, or any fancy or mystical syntax to export and import our code. Modules are singletons, no matter how many times they are used only one instance is created at the top scope level, declarations are scoped to modules and not visible globally. Individual ES modules are loaded into the JS environment, they then share imports/exports without the need for for a mutual outer scope. A modules top level scope is nested within the global scope, and the creation of ES modules empowers us to minimize our use and dependency on global variables and the global scope. Because modules are automatically created in strict and not available globally, this may limit accessibility to module features in the console and restrict debugging capabilities for these objects.

There are several ways to export/import these modules in our code, each with minor variations in syntax and functionality. Named exports are the most straightforward use of ES modules. This ES module syntax is useful for exporting/importing multiple objects from one file to the top level js file of a program using the example shorthand syntax below. Don't forget, we need to include the following three steps in any module variation: load the module file with a script tag in our HTML file, export the module using one of the ES module variations, import the module into our top level file.

  
  

    // Named Exports:

    // modules/localStorage.js'
    export { createNewList, buildSavedList, returnSavedList, manageGroceryList };

    // js/main.js
    import { createNewList, buildSavedList, returnSavedList, manageGroceryList } from './modules/localStorage.js';

    // index.html
    

                            

  

### Default Exports

  

Another variation of ES Modules is to use default exports. Default exports are often created to cooperate with UMD and Common JS modules. This syntax will only export ONE object per file:

  

    // Default Exports:

    // js/modules/localStorage.js'
    export default newList;

    // js/main.js
    import newList from './modules/localStorage.js';
                            

  

### Renaming Exports

  

When we need to avoid naming collisions, we may have to rename our modules upon export:

  

    // Renaming Module Exports

    // js/modules/localStorage.js'
    export {
        createNewList as newList,
        buildSavedList as savedList
    };

    // js/main.js
    import { newList, savedList } from './modules/localStorage.js';

                            

  

### Renaming Imports

  

ES6 has also provided us an option to alternatively rename modules upon import if we prefer:

  

    // Renaming Module Imports

    // js/modules/localStorage.js'
    export { createNewList, buildSavedList };

    // js/main.js
    import {
        createNewList as newList,
        buildSavedList as savedList
    } from './modules/localStorage.js';

                            

  

### Module Objects

  

The final module variation we will explore is creating module objects. Using module objects we include our named exports in our export file, and import everything in the file, and reference our exported objects as properties of the imported module:

  

    // Creating Module Objects

    // js/modules/localStorage.js
    export { createNewList, buildSavedList, returnSavedList, manageGroceryList };

    // js/main.js
    import \* as Storage from './modules/localStorage.js';
    Storage.manageGroceryList('addItem', 'banana', 3);

                            

  

Deeper Exploration
------------------

  

Whew. That was a lot to cover. If you are still here and want to explore these topics further I have two personal favorites I would like to share here, one specifically for in-depth exploration and another for the full breadth of web development and javascript topics. The first resource as I have already mentioned above is the "You Don't Know JS" series by Kyle Simpson, now into it's second edition. These books can be purchased in hardcover on Amazon.com, or can be accessed electronically from the github repo: https://github.com/getify/You-Dont-Know-JS. While the majority of the material covered in this blog post was based on "Scopes and Closure", other titles include: "Async and Performance", "Es6 & Beyond", "This and Object Prototypes", "Types and Grammar", and more. Alternatively, if video instruction is more your learning style Kyle has some notable intermediate and advanced JS classes at https://frontendmasters.com. The second resource I would like to mention is a github repo titled ['33 Concepts Every Developer Should Know'.](https://github.com/leonardomso/33-js-concepts)It is a thorough list of articles and video resources for exploring everything from callstack to closures. There also many great resources I used to research this blog post that will be included in the references below.

Good luck on your journey. I hope I was able to clarify some of these topics for you. If you found this blog post helpful please like it, or share it with other developers you think could benefit, and follow me on Dev.to or check out my online portfolio at [http://harleighabel.com](http://www.harleighabel.com)

Some of the code samples that were included in this blogpost were extracted from my practice app, which can be found at this repo from can be found [here!](https://github.com/atxrenegade/grocery_list) A live demo of that application can be found [here!](https://www.joesgroceries.com)  
  
[atxrenegade/Harleigh Abel](https://github.com/atxrenegade)

  

### References:

All Things Javascript. “Function Declarations VS Function Expressions in JavaScript.” YouTube, 14 Oct. 2016, www.youtube.com/watch?v=gjLn95skIKE.

Code With Mosh. “JavaScript This Keyword.” YouTube, 15 May 2018, www.youtube.com/watch?v=gvicrj31JOM. Accessed 25 May 2020.

ECMA INTERNATIONAL. “ECMAScript Language Specification - ECMA-262 Edition 5.1.” Ecma-International.Org, 2011, ecma-international.org/ecma-262/5.1. Accessed 2 June 2020.

Eygi, Cem. “JavaScript Callback Functions - What Are Callbacks in JS and How to Use Them.” Free Code Camp, 17 Mar. 2020, www.freecodecamp.org/news/javascript-callback-functions-what-are-callbacks-in-js-and-how-to-use-them/. Accessed 2 June 2020.

Griffith, Steve. “JS Function Methods Call( ), Apply( ), and Bind( ).” YouTube, 10 Oct. 2017, www.youtube.com/watch?v=uBdH0iB1VDM.

“JavaScript Modules.” MDN Web Docs, developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules. Accessed 25 May 2020.

“JavaScript Runtime.” MDN Web Docs, developer.mozilla.org/en-US/docs/Mozilla/Projects/Rhino/JavaScript\_runtime. Accessed 25 May 2020.

Jain, Siddarth. “Understanding the Event Loop in Javascript ( Js ).” Imaginea, 9 Dec. 2019, www.imaginea.com/the-javascript-event-loop-micro-tasks-and-macro-tasks/. Accessed 2 June 2020.

Johansson, Mattias Petter. “JS Function Methods Call( ), Apply( ), and Bind( ).” YouTube, 10 Oct. 2017, www.youtube.com/watch?v=uBdH0iB1VDM. Accessed 25 May 2020.

“Language Agnostic - What Is a Callback Function?” Stack Overflow, stackoverflow.com/questions/824234/what-is-a-callback-function#7549753.

Maldonado, Leonardo. “Leonardomso/33-Js-Concepts.” GitHub, 24 May 2020, github.com/leonardomso/33-js-concepts. Accessed 25 May 2020.

McGinnis, Tyler. “JavaScript Modules: From IIFEs to CommonJS to ES6 Modules.” YouTube, 15 Jan. 2019, www.youtube.com/watch?time\_continue=72&v=qJWALEoGge4&feature=emb\_logo. Accessed 2 June 2020.

Orendorff, Jason. “ES6 In Depth: Modules – Mozilla Hacks - the Web Developer Blog.” Mozilla Hacks – the Web Developer Blog, 14 Aug. 2015, hacks.mozilla.org/2015/08/es6-in-depth-modules/. Accessed 2 June 2020.

“Phases of Compiler with Example.” Guru99.Com, 10 Dec. 2019, www.guru99.com/compiler-design-phases-of-compiler.html.

Rauschmayer, Axel. “Exploring ES6.” Exploring JS : JavaScript Books for Programmers, 2015, exploringjs.com/es6/index.html#toc\_ch\_modules. Accessed 2 June 2020.

Roberts, Phillip. “What the Heck Is the Event Loop Anyway? | Philip Roberts | JSConf EU.” YouTube, 9 Oct. 2014, www.youtube.com/watch?v=8aGhZQkoFbQ. Accessed 25 May 2020.

Simpson, Kyle. “Getify/You-Dont-Know-JS.” GitHub, 25 May 2020, github.com/getify/You-Dont-Know-JS.

Techsith. “Javascript Closure Tutorial ( Closures Explained ).” YouTube, 18 Nov. 2015, www.youtube.com/watch?v=71AtaJpJHw0. Accessed 25 May 2020.

Wikipedia Contributors. “JavaScript.” Wikipedia, Wikimedia Foundation, 22 Mar. 2019, en.wikipedia.org/wiki/JavaScript.

Zlatkov, Alexander. “How JavaScript Works: An Overview of the Engine, the Runtime, and the Call Stack.” Medium, 12 Dec. 2018, blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf. Accessed 25 May 2020.

![Coding in Javascript](https://www.harleighabel.com/img/blog/javascript2.jpg)
