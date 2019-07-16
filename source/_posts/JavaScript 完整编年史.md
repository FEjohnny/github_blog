---
title: JavaScript 完整编年史
date: 2018/5/15 10:53:41
tags: [javascript]
---

    Where did we start, where are we now, how did we get here, and why?

# Introduction

&emsp;&emsp;In 2017, JavaScript development can leave newcomers to the language—and many veterans—paralyzed, unsure of where to begin or which paths are the right ones to take. People often dive into the latest-and-greatest without any real understanding of why it’s so great (or why it might not be). Understanding JavaScript's history can help make sense of its current state.

<!--more-->

&emsp;&emsp;Let’s talk about problems. All programming, in any language, is problem solving. This is true on both a macro level (“How do we deliver a solution to our users?”), a meso level (“How do we sort this data as quickly and efficiently as possible?”), and on a micro level (“How do I loop through this Array?”). Programming languages are built to give the user tools with which to solve these problems. JavaScript’s is no different than anything else out there with which you can code, for the web or otherwise. Snarky folks will also be happy to let you know how many problems JavaScript introduces, and I won’t deny that there are plenty, but that’s also true of any other language. I’ve never known an experienced coder who didn’t periodically curse some decision made by some long-ago standards board for reasons that may no longer even make any sense.

&emsp;&emsp;This article isn’t meant to be an in-depth and exhaustive look into all of the problems JavaScript solves, let alone how to use it to do so. Rather, it’s a broad-level overview of the history both of the language itself and of the ways in which it’s been used to solve increasingly complex problems on the web. It attempts to give big-picture answers to questions like Where are we? and Why are we here? I've had to keep it pretty high-level; a complete history of the language could easily fill an entire book. That's what I mean by “brief” history.

&emsp;&emsp;Without knowing the fundamentals, it becomes difficult to grasp the answers to common questions like “what is a framework?” and “why is jQuery a good solution for problem A, but not for problem B?” Learning the answers to those questions allows one to make smarter and more informed choices as a developer, which saves a whole lot of time, effort, and sanity.

&emsp;&emsp;I’ve broken the article up into four basic time periods. The Early Era, which covers the first ten or so years during which the nascent language was available in browsers; the jQuery Era, during which jQuery and other frameworks began popping up in order to address some fundamental and extremely frustrating problems in JavaScript development; the Single Page App Era, during which developers began to reach the limitations of jQuery and frameworks like Backbone and AngularJS rose to prominence; and the Modern Era, during which we’ve seen an explosion of smart, tightly-written frameworks that focus on speed, ease of use, and compartmentalized application development, as well as a return to “Vanilla” JavaScript. For each era, I’ll give some background on where web development was at, talk about the problems people were running into, and explain how that era’s technology addressed those problems.

&emsp;&emsp;But we’re getting ahead of ourselves. Let’s take a time machine back to the days when dinosaurs roamed the Earth and one particular browser reigned supreme: Netscape Navigator.

# The Early Era

    Timeline: Roughly 1996 – 2004
    Problems: Basic DOM manipulation, User Interaction
    Innovations: JavaScript itself, XHR & AJAX
    Dominant Browsers: Netscape Navigator, Microsoft Internet Explorer

&emsp;&emsp;JavaScript was created over a period of ten days by Brendan Eich, then an employee of Mozilla who’d been hired to wedge the Scheme programming language into Netscape Navigator. This plan was ditched in favor of a language whose syntax was more similar to Java. Eich created LiveScript, a language that could be embedded directly into an HTML document and parsed by the browser without need for compilation. LiveScript was first shipped with Netscape Navigator in September of 1995, and was renamed JavaScript for the 3rd beta release of that software in December 1995.[1]

&emsp;&emsp;Despite the name, JavaScript shares little in common with Java beyond being a C-like, indentation-indifferent, object-oriented language … which is actually quite a lot in common, but it contains no shared codebase with Java and significant core aspects of the languages are completely different. The decision to name it JavaScript was a marketing move and has led to twenty-one years and counting of recruiters contacting JavaScript developers and breathlessly listing off all of the exciting Java programming opportunities they have available. Thanks, Mozilla!

    Netscape 3 and, particularly, Netscape 4 were the Tyrannosaurus Rexes of their day, crushing all challengers between the mighty power of their colossal jaws.

&emsp;&emsp;For the first couple of years, JavaScript was in a battle with Microsoft’s various scripting languages, which was a significant contributing factor to the period where any given website was only likely to work properly in either Netscape or Internet Explorer (then in its third version), but not both.

&emsp;&emsp;Make no mistake, Netscape was no lumbering sauropod in these years. Netscape 3 and, particularly, Netscape 4 were the Tyrannosaurus Rexes of their day, crushing all challengers between the mighty power of their colossal jaws. IE was an also-ran that could barely render HTML properly, let alone do so once Cascading Style Sheets came into wide popularity. Netscape seemed unstoppable.

&emsp;&emsp;But by the early 2000s, Netscape development languished and Internet Explorer—first version 5, followed by 5.5, and then the god-killer, version 6—continued to improve, gaining greater market share. JavaScript development at this time was limited. Attempts at standardization were made and largely followed, both by Mozilla and by Microsoft, whose “JScript” language was so named mainly to avoid copyright issues. Still, differences in browser capability abounded.

&emsp;&emsp;As a result, writing complex scripts that worked in multiple browsers was tedious and sometimes outright impossible. Much scripting in that era was relegated to bells and whistles. As James K. Nelson, creator of React Armory (and many excellent JavaScript posts and cheat sheets), puts it, “I got into JavaScript in this era because I wanted to add mouseover images to a menu bar on a website I was creating. I also used JavaScript to create user-unfriendly dropdown menus, create annoying popups and make some simple animations.”

&emsp;&emsp;Obnoxious scrolling text abounded. So did alerts, confirm boxes, and a whole lot of security exploits. Probably the single most-common use of JavaScript in those early days was to create the aforementioned DHTML effects like image rollovers, functionality which was eventually replaced in large part by CSS.

&emsp;&emsp;There were people doing good work in JavaScript, though. As Dori Smith, author of JavaScript: Visual Quickstart Guide and a programmer / author since the early days of the language notes, “There were plenty of JS frameworks and libraries in the late 90s. Nick Heinle and Steve Champeon each had one, as just two examples. But they were all dependent on the owner's keeping them updated, and that was difficult for anyone trying to also, well, develop sites at the same time.” It was also harder to find these libraries, and fewer people were looking. When it came to dynamic interaction on the web, the landscape was dominated by Java applets, ActiveX widgets, and Macromedia Flash.

&emsp;&emsp;It took a move into the 2000s, and a few happily confluent developments, for JavaScript to really take off. One of those things was the creation of various approaches for asynchronous communication between a JavaScript-driven front-end and external back-end servers, including the eventual adoption by all major browsers of XMLHttpRequest (XHR). The other, coming a bit later, was the development of frameworks like Prototype, MooTools, and, of course, jQuery.

# The jQuery Era

    Timeline: Roughly 2004 – 2010
    Problems: Increased Site Complexity, Tons of Browsers to Support
    Innovations: Robust DOM Manipulation, Early Single-Page Apps
    Dominant Browsers: Microsoft IE, Mozilla Firefox

&emsp;&emsp;In the early 2000s, DHTML started to catch on. The D stood for Dynamic, and basically meant “we do stuff with the HTML you see on screen without requiring a browser refresh.” It seems a little ridiculous at this point, but this was a huge deal at the time. Traditionally, websites required a page refresh when you wanted to do anything. JavaScript had provided some toys, but the standard website was still very much page-based. When a user clicked a tab, they were taken to a separate HTML document or given a full page refresh that adjusted template variables before re-rendering the HTML. This is still how the core functionality of many sites works (including this one), but the advent of robust DHTML support meant that more pieces of a website could become more, well, dynamic.



&emsp;&emsp;During this period there were only two major browsers, Microsoft’s IE6, which was an incredible browser at release but eventually became an unsupported zombie with a strangle-hold around the internet’s neck, and Mozilla’s Firefox. However, there were others on the fringes, and multiple versions of IE still in use. “Developing for the web at that time was extremely difficult because of the poor state of web standards and the large volume of bugs in the web,” Douglas Crockford, author of JavaScript: The Good Parts (and many other texts on the language), told me. “Auto-update had not been invented yet, so [the] release of a new browser version did not eliminate bugs from the web. But it did add new ones.”

&emsp;&emsp;browser version did not eliminate bugs from the web. But it did add new ones.”

&emsp;&emsp;Trying to present a consistent experience between these browsers was a nightmare; trying to make that experience dynamic was even worse. Here’s John Resig, jQuery’s creator, speaking of the framework’s genesis:

    When I originally created the library I wanted to scratch two personal itches: 1) To provide a simple interface for interacting with the DOM and 2) Reduce the number of cross-browser issues that exist during development.[2]

&emsp;&emsp;Dealing with the DOM across multiple browsers was a significant factor for early-2000s web developers, but it wasn’t the only thing on their minds. The other huge revolution in the industry was AJAX, which allowed for asynchronous exchange of data with the server instead of relying only on what was available at page-render time. “In 2005, Jesse James Garrett discovered AJAX, a new name for DHTML,” Crockford says. “AJAX was more successful than DHTML because Netscape had died, and because Microsoft had abandoned the web after IE6, and because W3C was fruitlessly distracted chasing the Semantic Web. A long period of neglect had brought badly needed stability. AJAX was a big hit, inspiring many new libraries for making single page web applications.”

&emsp;&emsp;Early pioneers at Microsoft, Google, and other large companies did a lot of compelling things with AJAX in those years, but it was the release of Gmail, in 2004, that really made the web development world perk up. Here was a completely new way of dealing with email: by doing it all in the browser and storing the emails on Google’s servers, it meant that a user could access their mail from any internet-enabled device, anywhere in the world, without having to install a native email application. This was not the first single-page app, but it was the best of its era by a wide margin, and the world took notice. Gmail used DHTML and AJAX-like code in a way few other websites had, and it was other developers’ desire to use those two things, quickly and easily, that led to the rise of frameworks, including the big dog: jQuery. “It solved browser compatibility issues, added a bunch of useful utilities, and introduced selectors other than document.getElementById,” Nelson says. “The only problem with jQuery was its weight. ~30kb is a lot if you’re on dial-up.”

&emsp;&emsp;But jQuery was not the first! Prototype, first developed to add AJAX support to Ruby on Rails but also capable of DOM manipulations similar to those for which jQuery would become widely known, was released in February of 2005. It was followed by jQuery in 2006, and MooTools in 2007, among others. While each of these libraries provided a lot of similar functionality, they had their own approaches. ProtoType rewrote and extended many of JavaScript’s native methods, which some developers found troubling. MooTools made changes to JavaScript’s Element object, which meant it allowed more manipulation of the DOM.

&emsp;&emsp;jQuery did neither of these things, focusing on providing a framework that was built on top of basic JavaScript. This approach proved the most successful and in a few short years, jQuery had become the dominant framework. It remains so to this day. More websites in 2017 use jQuery than any other JavaScript framework.

&emsp;&emsp;But what did the frameworks really offer? Well, let’s take a look at these code snippets. Here’s how one had to hide an element on click in plain JavaScript:

```
var el = document.getElementById('myElement');
var el2 = document.getElementById('myOtherElement');
el.addEventListener('click', function (e) {
el2.style.display === 'none';
}
```

&emsp;&emsp;That would get the job done, assuming your browser properly supported those commands, which at the time was not a safe assumption for many DHTML and AJAX use cases. Here’s the same functionality in jQuery:

```
$('#myElement').click(function() {
$('#myOtherElement').hide();
});
```

&emsp;&emsp;Not only is that much more concise and easy to read, it also comes with another benefit: jQuery makes sure that it works in all browsers, so that the engineer doesn’t have to spend time worrying about that. It also meant that developers didn’t have to spend as much time recreating the wheel. Why write your own show/hide/toggle functions when jQuery already offers those and dozens of others? “jQuery didn’t really change what I built with JavaScript,” Nelson says, “but certainly changed how I built things. It made JavaScript simpler in a way that seemed magical at the time.” Simply put, jQuery and its competing frameworks made development faster and easier for everyone who used them.

&emsp;&emsp;… to a point. As websites grew ever more dynamic, and companies without Google’s army of engineers began to try and build applications as complex as Gmail (or moreso), they began to run into trouble. Codebases consisting of tens of thousands of lines of jQuery code were difficult to maintain and contained so much custom functionality that bringing new developers on board became challenging. If your website has five clickable elements, having five instances of $('#myElement').click() is manageable. If it has 500 clickable elements, it becomes problematic. If it has 5000 clickable elements, it becomes a nightmare.

&emsp;&emsp;Something more was needed. JavaScript frameworks began to evolve, taking on features and approaches that were decidedly back-end-like. The Single Page App Era had arrived.

# The Single Page App Era

    Timeline: Roughly 2010 - 2014
    Problems: DHTML Overload, Large-Scale Data Manipulation, Speed
    Innovations: MVC-like frameworks, Bi-Directional Data Flow, DOM “Automagic”
    Dominant Browsers: Google Chrome, Microsoft IE, Mozilla Firefox, Apple Safari

&emsp;&emsp;A few other innovations helped usher in the single-page app era. One was Google’s Chrome browser, initially released in 2008. Among Chrome’s innovations was a set of development tools more robust than any previously seen. Constantly improved and updated, Google’s tools allowed developers to peer into, and edit in realtime, what was happening with the HTML, CSS, and JavaScript. It also featured an integrated JavaScript debugger which allowed the language to be debugged in similar ways to traditional, compiled languages (previously, developers had relied on browser plugins or outside programs for these features). Most major browsers now provide similar capabilities natively.

&emsp;&emsp;Chrome’s other major contribution? The V8 JavaScript rendering engine, which allowed the creation of standalone JavaScript platforms like Node.js. This article’s history is focused predominantly on JavaScript’s front-end , but we’d be remiss not to mention that Node.js has become a major factor in website development. Adopted by hobbyists and large corporations alike, Node’s fast, asynchronous approach to application development proved popular with developers, and now powers many websites, including this one.

    JavaScript frameworks needed to become more like those that already existed for back-end languages like C++, PHP, Ruby, and others.

&emsp;&emsp;It wasn’t all Chrome, though. The Single-Page App Era had the most equal usage split between browsers of arguably any period of the web’s life, and yet … it was something of a joy in which to develop. Browsers, even IE, had mostly adopted standards. For the first time, there was a good chance that the website you developed would both look and function the same no matter what browser you opened it in, or at least need only minor tweaks. CSS frameworks like Sass had appeared, making the visual side of web development dramatically easier, and the limitations of the box model had been pretty thoroughly cataloged (leading to a lot of talk, some good-hearted, some less-so, about how much all “Web 2.0” websites looked the same). Disruption was on the horizon: flexbox was coming, and in 2010 Ethan Marcottepublished his landmark article on Responsive Web Design, but for a brief period of time, things felt like they’d stabilized a bit.

&emsp;&emsp;In the single-page app world, that wasn’t so much the case. Tired of dealing with the inscrutable morass of tens of thousands of lines of jQuery code, developers began casting out for other alternatives. New frameworks were developed with a different focus than the major releases of the previous era. DOM Manipulation and basic AJAX was no longer a problem. What was needed was a suite of tools that would allow them to manage their increasingly complex applications. JavaScript frameworks needed to become more like those that already existed for back-end languages like C++, PHP, Ruby, and others.

&emsp;&emsp;Enter Backbone.js. In 2010, developer Jeremy Ashkenas released his novel collection of tools for single-page app developers. Lightweight, fast, and not reliant on jQuery (though developers could include jQuery to unlock even more of Backbone’s features), Backbone was specifically built to address the “jQuery swamp” issue. This text, from its website, is illuminating:

    “It's all too easy to create JavaScript applications that end up as tangled piles of jQuery selectors and callbacks, all trying frantically to keep data in sync between the HTML UI, your JavaScript logic, and the database on your server. For rich client-side applications, a more structured approach is often helpful.”

&emsp;&emsp;Backbone’s approach was to split code up into data models, collections of functions with which to manipulate that data, and views with which to display it. It also provided a lot of “automagic” in the sense that it handled some amount of manipulation behind the scenes. By connecting their views to their data, developers no longer had to worry about updating their sites when their data changed. Backbone did it for them … and still does. Backbone is an excellent product and remains in widespread use among some very large, very well-known web applications.

&emsp;&emsp;Also in 2010, the first version of AngularJS showed up on the scene. Initially developed by Miško Hevery and Adam Abrons, it ended up in Google’s hands when Hevery became an employee. It enjoys continued support from Google and a crew of dedicated outside developers, though the more recent v2.x releases are significantly rewritten and really belong in the next section. AngularJS 1.X, however, is still supported by the developers and Google, and still in widespread use around the internet.

&emsp;&emsp;AngularJS presented a complete front-end architecture solution in a way that even Backbone.js did not. It provided powerful tools and a component-based architecture that were difficult or impossible to recreate with plain jQuery. As Nelson says, “I’d already been trying (and failing) to build useful single page apps with jQuery and raw JavaScript for a couple years. And then I stumbled across AngularJS, which taught me that an app's model doesn’t need to live in the DOM. This made large applications workable.”

&emsp;&emsp;Using its concept of bi-direction data flow, AngularJS allowed developers to build apps that reflected changes in data on both the server and client side. For example: one could create an AngularJS application in which a user filling out a form could see the data they were entering accumulating in real-time elsewhere on the page, and know that the data was simultaneously being saved to a server somewhere. No longer did one need to write large amounts of jQuery to achieve this sort of synchronicity – it just happened.

&emsp;&emsp;AngularJS, like Backbone, also provided a great number of helpers for manipulating the DOM. Also like Backbone, it could work well with jQuery, but didn’t require it to perform much of its main functionality. This allowed developers who were familiar with the jQuery ecosystem to gradually move to AngularJS.

&emsp;&emsp;The framework also helped popularize the use of components – individual functions that were presented as HTML tags but could contain complex functionality. This allowed for wildly cleaner and more compartmentalized markup, in which a component might be called with a tag like this:

```
ng-repeat="user in $user.list">
```

&emsp;&emsp;And generate an entire list of users, complete with details, and embedded HTML such as links to the user’s profile page. Equally important, if the data in the $users.list array were to change, AngularJS would re-render the list with the newly updated data without the developer needing to do anything further.

&emsp;&emsp;Between Backbone and AngularJS, suddenly there were two entire toolboxes full of single-page app development tools that addressed the many shortcomings of large-scale jQuery development and kept developers from having to constantly write their own similar functions. If JavaScript was basic hand tools, and jQuery power tools, then frameworks like Backbone and AngularJS were assembly lines – specialized collections of highly complex equipment designed for a singular purpose: creating single-page applications.

&emsp;&emsp;Problem was … they weren’t particularly small or fast (especially on mobile devices), could be difficult in their own right to maintain, and the whole bi-directional data approach could turn out to be a real double-edge sword.

&emsp;&emsp;In 2013, Facebook released React, a front-end framework that was small in size and blazingly fast at render time. In 2014 they followed it up with their event-based approach to app organization and development, Flux. These things, and the associated technologies that grew up around them, changed JavaScript app development yet again.

# The Modern Era

    Timeline: Roughly 2014 – Present
    Problems: Speed, Increased App Complexity, Reliability
    Innovations: Virtual DOM, Single Direction Data Flow, Types, Testing
    Dominant Browsers: Google Chrome, Apple Safari

&emsp;&emsp;In the last few years, we’ve seen a steady diversifying when it comes to how web pages are accessed. Once exclusively the domain of home PCs, it’s now very reasonable to want to visit a website on one’s phone, tablet, laptop, and desktop, all in the same day. The bandwidth, processor power, and screen resolution available to these devices varies wildly. Small downloads and fast rendering are extremely valuable … not that you’d know it from visiting a lot of content websites that feel completely comfortable hammering the user with massive image downloads, megabyte upon megabyte of ad code, and auto-playing video!

&emsp;&emsp;But content websites aren’t single-page applications, and the expectations of their users are different. Single-page apps are intended to replace native applications, and as such are expected to behave with the speed and responsiveness of the things they’re replacing. A user who’ll tolerate a five-second load to read the latest sports news is less likely to tolerate a five-second load when they click a button in their banking application or analytics dashboard.

&emsp;&emsp;Facebook developed React for speed and ease of use by their developers. A lot of smart people spent a lot of time on it, and it shows. It’s not perfect, and there are new and potentially better options always coming down the pipeline, but it has a lot to offer to developers – so much so that it often gets used in projects that don’t really need it … but we’ll get to that.

    And yet, if you’re reading this article, there’s a very good chance that you’ve heard of or actively used React as your entire front-end solution. Why is that?

&emsp;&emsp;React didn’t supplant Backbone and AngularJS as the JavaScript framework of choice, though it’s made a dent in their usage, in part because it’s not a complete framework. React was initially conceived principally as the view language for front-end MVC frameworks. Indeed, React was built to work with other existing technologies, though several of those were custom technologies also developed by Facebook. That said, it’s equally at home working with tech that Facebook has never touched. It doesn’t handle data, it doesn’t handle events, it doesn’t handle XHR/AJAX … all it does is render components.

&emsp;&emsp;And yet, if you’re reading this article, there’s a very good chance that you’ve heard of or actively used React as your entire front-end solution. Why is that? Because the ecosystem that’s grown up around React, enabled by bundling technologies like Webpack or Browserify, has allowed “React” to become a handy shortcut for “React plus a whole bunch of other stuff.” Nelson sums it up succinctly: “In a way, the ‘modern era’ feels similar to the jQuery era in that it has made it much simpler to build single-page-apps, without making new types of apps possible. React gives me a much simpler way to create re-usable components. Webpack and NPM facilitate the sharing of those components and their assets. And Babel means that we don’t need to create new languages just to make JavaScript usable.”

&emsp;&emsp;React is not without its problems, however. The bundle-based ecosystem means that JavaScript file sizes can rapidly get out of control unless a lot of attention is paid, and the overall landscape is aggressively intimidating even for experienced developers, let alone newbies. Quality documentation and a friendly community have helped to mitigate this, but the learning curve is still very steep.

&emsp;&emsp;Other frameworks have also sprung up. AngularJS 2, as mentioned, has been largely rewritten to function in a more React-like manner, and its rendering is wildly faster than AngularJS 1’s, which is a significant factor when displaying large amounts of data. Meteor, which predates React by at least a year (and, in fact, can use React as its view layer), has a dedicated following. Vue.js, first released in February 2014, is a small, fast front-end framework that plays nice with others, allowing for incremental implementation. Its syntax is similar to React’s, and its structure, while not identical, follows a similar component-based approach. Preact is an extremely small, fast React alternative built from the ground up using ES6, a more modern version of JavaScript that’s enjoying increased popularity (React can also work with ES6 but was initially built with ES5, which is more or less the JavaScript we’ve known for about a decade). Others are following.

&emsp;&emsp;It’s important to note that what React, AngularJS, and others are doing is not really reinventing the wheel. Other languages have been doing this stuff for decades and even in the JavaScript world, as Crockford notes, “[We’ve] stopped calling it DHTML or AJAX and started calling it Single Page Apps, but it is still the same stuff with newer libraries.” This is largely true; the underlying code is still JavaScript, and still doing the same basic things that people have been doing with the language since its earliest days. The difference now is one of approach.

&emsp;&emsp;All of these frameworks tend to focus on solving the same problem: creating a variety of tools for developers that allow them to quickly build fast, single-page web applications that will work well on a variety of devices. They tend to focus very heavily on data flow and display: basically, on cutting down the work a developer has to do in order to get the data being provided to them onto the screen in a way that’s useful for the end-user, and updating that screen automatically when the data changes. When you’re dealing with immense amounts of data, and you’re looking to present an application-like experience to the user, they’re life-savers.

&emsp;&emsp;Which brings us to Vanilla JS. You might, at this point, be wondering what someone who’s developing a small website in 2017 should be using for their fairly meager JavaScript needs. AngularJS and React certainly seem like overkill, don’t they?

&emsp;&emsp;They are. While fantastic for single-page applications or websites with a ton of moving parts, modern JS frameworks are unnecessarily complicated if all you’re trying to do is catch a few button clicks and swap between tabs. The answer to “what do I use?” is, depending on your specific needs, either jQuery 3 or Vanilla JS.

&emsp;&emsp;Vanilla JS is not a framework. It’s not a library. It’s not anything except, well, JavaScript. Recent additions to JavaScript have helped make it much easier to work with. document.querySelector and document.querySelectorAll, for example, provide jQuery-like element selection using a CSS-like syntax (“.exterior-link”, for example). ES5, ES6, and ES7 are supported essentially perfectly by all modern browsers, reducing the need for jQuery’s cross-browser help for those who’re no longer stuck catering to the rapidly dwindling pool of legacy-browser users. Performance-wise, writing plain JavaScript code is almost always going to be blazing fast (unless your routines are suboptimal!) even on older, slower mobile devices. Smith, like many developers, is excited about this new focus. “… the return of Vanilla JS; that's huge to me. I got thoroughly disgusted with the number of people on Stack Overflow that, imo, abused jQuery and similar frameworks. Bringing in all of jQuery just so you can write three lines of code instead of five? Pfffft.”

&emsp;&emsp;We don’t cover a lot of Vanilla JS here at CloseBrace (yet!), but the web is full of good resources. In particular, friend of the site Chris Ferdinandi has produced a tremendous amount of excellent Vanilla JS content over at Go Make Things, including plenty of open source code you can use in your own projects.

&emsp;&emsp;So, here we are. It’s 2017, the JavaScript ecosystem is both thriving and confusing as all hell. No one seems to be quite sure where it’s headed, only that it’s going to continue to grow and change. The web’s not going anywhere, which means JS isn’t going anywhere, and I’m excited to see what future eras bring us. Hopefully you found this article interesting and informative, and are leaving with a better sense of where we are, how we got here, and why we took the path we took. Thanks for reading.