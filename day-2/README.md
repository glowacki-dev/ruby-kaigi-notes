# Day 2:

## Kouhei Sutou - My way with Ruby (Keynote)

### Intro

What is a "keynote-ish" topic? You can either talk about one very specific think or give a broad overview. 

### Notes

Let's overview what can we do with Ruby.

* RSS Parser
  * Only parser with validation
* REXML - Ruby Electric XML
  * Used in RSS Parser

Another tool is Rabbit

* Rabbit
  * Presentation tool for Rubyists
  * RD (Ruby Document) support
  * Required some GUI
* Ruby/GTK3
* Ruby/GI
  * Automatic bindings generation
  * Used in Ruby/GTK3

When creating a tool you may miss some libraries. When creating those libraries we can either only add the functionality we need or create full featured library.

When creating Rabbit, we've created Ruby/GI which can now be used in many more places. For example in Pango and Poppler libraries. And we can quickly expand it to more and more tools - all of which are important for creating Rabbit. We need a library for graphic rendering, there is already rcairo so we can improve it and fix some bugs that were troubling us.

For the presentation tool we'll also need an easy way to install. The easiest way will be to satisfy all system packages dependencies during gem install. We can use rake-compiler for this. The same goes for testing. We'll use test-unit, but since it's missing some features we'll add them.

We've also looked at different libraries related to working with text (internationalization, full text search, text extraction etc), data processing (CSV and Apache Arrow) and Computer Vision.

## Kirk Haines - It's Rubies All The Way Down

### Intro

We'll take a look at how much of current web application stack can be handled by Ruby

You can find [the slides here](http://slides.com/wyhaines/its-rubies-all-the-way-down#/)

### Notes

What is a stack? Basically everything from log management, through database and web server all the way to load balancing. Usually only a few of those elements are done in Ruby.

Web development was very different a few years ago. There were no tools that we have today (rails or erb). In 2001 a PoC web framework IOWA was created. It allowed using much better architecture than other options - you were able to listen on a socket. Even first Rails versions were Apache centric.

It changed in 2006 with Mongrel, a fast web server written in Ruby. Also nginx was starting to get more popular choice than Apache. Then came Rack which changed how Ruby apps interacted with the web.

There was more and more places where you could use Ruby in your web stack. It seemed it would be possible to handle everything with Ruby. Need goood asynchronous logging? Write it yourself in a language that you already know. It turns out Ruby can do stuff like this fast enough. At this point we could have the whole stack except for the database written in Ruby.

![ruby_stack](media/ruby_stack.jpg)

If you need front end proxy there is a Ruby solution as well: [em-proxy](https://github.com/igrigorik/em-proxy). It turns out you can even use WEBrick to do the job, and with proper tuning it will be fast enough as well.

How about static asset caching? We can try combining Puma and Rack to do the job for us. It won't be the fastes solution but it may do the job for you. Similarly, creating a static assets webserver in puma is not much slower than using nginx. It's definitely fast enough.

For key-value store you've got ROMA. This is a distributed key-value store written in Ruby.

The only point missing is the database. And there is actually no Ruby solution for this one.

### Takeaways

Nowadays it's possible to use mature, well known tools written in Ruby to handle most of the web application stack tasks. However, it's worth keeping in mind that Ruby is not as fast as C or C++ so if performance is important for you, you'd better choose standard solutions. The other concern will be security. Software like nginx is really well tested by security experts, while smaller solutions may still have some critical bugs.

### Q&A

1. **Q**: What's the source of assumption that it can't be done?

   **A**: People still believe that Ruby is too slow for any serious task

2. **Q**: Why do you think nobody written a database in Ruby?

   **A**: The answer may be similar to the previous one. Most people probably thought that it's not the best language for the job

