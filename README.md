# Day 1:
## Matz - Keynote
### Notes
Names are important (what was the proverb?) -> especially in programming

Programming is not physical, it's made of concepts, behaviors. Good naming make a code more usable. Bad example would be `yield_self`
```ruby
def yield_self
  yield self
end
```
The name literally describes what it does. Take a look at [feature #14594](https://bugs.ruby-lang.org/issues/14594) about better name for `yield_self`. How about `then`? It would chain nicely. (Matz doesn't really commit into the c ruby too often - he does for MRI - this was first commit in 5 years apart from versions bumps)

Native Americans believe that knowing something/someone true name grants you control over them. The name has a certain power to it and is very important. Project's name can be really important. It can make people love the project. What if Ruby wasn't Ruby? What if Matz didn't come up with "ruby" in 1993 (?)... Nowadays ruby will not be a good name. Now we have google and ruby is already occupied by precious stones so people will have trouble finding it. It wasn't issue in 1993. But there are worse examples: "go" (great idea google), "swift" - they have very low "googlability". 

The latest trend for better "googlability" is combining words to form a name. Take a look at "Ruby on Rails", "tensor flow" they are unique. Tweaking spelling is another way to achieve this "streem", "jupyter". Some also use exotic names like "hanami" or "kaminari" - you don't know what it does but at least it's easy to find.

Second proverb is "Time is Money" or rather "time is value" - value can often be converted into money. We only have 24 hours every day but majority will be wasting time. We can't avoid things like sleeping, but what about being unproductive? Prioritizing things is very important. Different things are important for everyone.

Ruby helps you use your time better. It is powerful because it has a lot of useful methods. If you need something you usually have a method for this. There are also lots of libraries and frameworks that give you ever more extensions. Ruby community is also big, which gives you a lot of mentors - people who can help you. Ruby is also conscience. On one hand it has a really short syntax but it is easily abusable. We can create very compact programs. "Succinctness is power" ~Paul Graham. It leads us in a direction of much more conscience programs for example through meta programming.

In the ideal world all programs are small enough to grasp. But reality is different. It has to be. But we can leverage some tools to help us. There will be talks on this conference which should touch this topic (fro example about rubocop).

Other thing people want from ruby is performance. But it was rewritten to be faster in 1.9. One of the features upcoming in ruby 2.6 is JIT Compiler. It's already being worked on and should make Ruby even faster. Nowadays we all use multi core CPUs. That's why GIL was a major obstacle for boosting ruby performance. That's why people are working on improving concurrency into ruby (Eric Wong). Since time is money we want ruby to be as fast as possible. 

Third proverb is from "an old man and a horse" - saiohaguma (?). Things tend to end up good - life has it's ups and downs. When ruby was first released people didn't know what it was about. Then Ruby on Rails came into existence and ruby was getting a lot of traction. It peaked around 2012 when people were all ecstatic about how ruby is amazing. Now, a few years later the hype stopper. People are saying that ruby is dead, because it's slower and less portable than other languages.

People were complaining that Ruby had it's compatibility gap between 1.8 and 1.9. But the same thing happened to python 2 and 3 and it's been over 10 years since and version 2 is still not deprecated (but will be in the future). Let's say that ruby 3 will be released at some point - there will be some division in community, but we can't get stoped by every obstacle. If you just stop worrying and keep moving forward it makes thing easier.

With all things mentioned earlier ruby gives you high productivity and a great community. It's not something provided by one person for other people but is something created by community from community. The next 3 days of Ruby Kaigi will let us see this.

### Q&A:
1. **Q**: What about type definition? Look at TypeScript and JS. I don't like it. How will this be for ruby?

   **A**: There will never be typing in ruby language definition. There is type annotation in python. Typing is useful for compiled languages, because it helps with finding bugs, but types can actually be recognized by computers automatically, so you won't have to define static typing in language specification.

1. **Q**: Are there plans for high level constructs for ruby? Something like macros?
  
   **A**: We have to care about compatibility so such things probably won't come before 3.0, but there are no concrete plans for such things.

1. **Q**: Why is it exactly that we don't want type annotations?

   **A**: Because in the future type annotations will most likely be unnecessary and become obsolete so there is no point adding them.

## Aaron Patterson - Analyzing and Reducing Ruby Memory Usage
### Intro
Practicing Japanese = 👏

Practicing Japanese on stage = 🙅
### Notes
We'll talk about two memory optimizations in Ruby:
- Memory cache
- Stack VM

Let's start by finding memory usage issues. Since Ruby is written in C we'll look at C programs first. The first way (not very good one) is to look at the code. Better way is to use malloc stack tracing.

In Ruby we can divide memory in two spaces. We've got GC allocated and ObjectSpace allocated memory. There is allocation_tracer for ruby which allows tracking Ruby allocated memory, but not anything outside. For this we can use Malloc Stack Logging.
Malloc logging can grow really fast, as it contains information about all allocations and memory freeing. We can analyze those logs to know how much memory the program was using at the given time. We can also find out what was using the most memory.

We can combine two techniques: "insterumentation" + "" to find out more about moemory usage.

But first let's look at shared string optimization. This allows two different strings to reuse the same part of memory but only if they have the same end. Otherwise there will be a string copy created. Therefore if it's possible it's better to copy the string until the end and not trim it.

Second thing to look at is `$LOADED_FEATURES`. It allows Ruby to keep track of files that were already required. But how do we know that two files are the same ones? We can use different paths for the same location. Also, if it's an array then searching it will be slow. But there is some smart caching going on that speeds up the process.

[*Memory cache keys generation algorithm*]

The problem was that the original algorithms was taking a substring. It actually had to create 8 new ruby objects for lookup of simple path like `/a/b/c.rb`. We can use shared strings to speed it up. We can even go further and get rid of ruby objects completely. Since the hash is already written in C we won't use Ruby hash, but instead point directly to C strings.

With this change we've achieved 40% reduction in ruby objects creation. In real world Rails application we've saved over 4% of memory. This change will be available in Ruby 2.6

Issue #14460. The same thing was proposed in #8158 (submitted 5 years ago) - always search for existing issues

So how about the stack VM? How do we actually get instructions to execute?

The code has to go through processing made out of parsing and compiling (where the bytecode is generated). There can be optimizations applied in the meantime. The first step is converting code into abstract syntax tree which is actually implemented with ruby objects. We then have to convert those into linked list of operations. The linked list is much easier to work with so there can be more optimizations applied here. The last step will be converting into bytecode which is as simple as representing our list nodes as a list of instructions (in a form of numbers, so we can't use ruby objcts here - we can use their addresses).

We now know how compilation and code execution work.

Since we're using ruby objects addresses in a program we can accidentially modify the original objects. To be sure we don't do this we can duplicate the objects. That's what `frozen_string_literal` doesn. It tells the VM that the strings can't get modified.

We also have to be aware of another problem. Since the strings are both C strings and Ruby objects they can be garbage collected while the C internals still need those. That's why we have mark arrays which keep track if objects are still used. But it duplicates some information, because both the array and instructions list point to the object. Also, the more objects we track the bigger the aray have to be and there can leave us with some unused memory space for the lifetime of the program.

Caould we change array into something else? We can actually mark objects directly from the VM. It's called Direct Marking Technique (#14370). How does in impact our programs. It reduces number of allocated arrays, and number of allocated objects. Overall it saves you around 6% of memory (depending on how much code you have). Also available in Ruby 2.6.

Upgrade to Ruby 2.6!

### Q&A

[No time for Q&A]

## A practical type system for Ruby at Stripe

### Intro

Focusing at developer productivity. No Rails, a few macroservices, new code in existing services. Bulding upon existing type checkers (such as DRuby, PRuby, RubyDust).

Stripe's type checker is still not open source but there are plans to open source in the future. It's being tested internally. Check it out now at https://sorbet.run

### Notes

How do we start designing a type system for language like Ruby? 

It has to be explicit but useful at the same time. It should be rewarding for developers to use it. It's actually easy to build a complex type system, but it's worth trying to make is as simple as possible. 

Another important point is compatibility. We'd like our type system to be ruby based and work seamlessly with existing core and libraries. This would also alow gradual adaptation.

Another problem is that we want the type checker to be truthful. Due to Ruby nature some objects can for example become nillable or non-nillable at some point. We should be aware of this. The same goes for dead, unreachable code. Or for union types. Stripe's solution covers all those cases.

Ok, so how do we declare it? Keep in mind we want to use a valid Ruby syntax.

[There will be an image here later 😬]

There are obviously different strictness levels. The lowest one will only look for syntax errors while the highest one will not allow to execute code with possible types mismatch. It could be used for some critical parts of application.

So does this actually work and is it useful?

It's currently being adopted into Stripe codebase. There were some bugs and suggestions found along the way:

- Typos in error handling - that's sometimes tricky to find in a real application, because your error will not be handled where you want it to. Type ckecking helps with this

- nil checks - some methods require non-nil object so we have to remember to check for nil before using them. Type ckecker helps finding this pattern.

- Using instance variables from static methods - think something like this:

  ```ruby
  class Foo
    def initialize
      @bar = 'bar'
    end
  
    def self.baz
      @bar
    end
  end
  ```

  It obviously won't work but is a common error 

- Unreachable code in unexpected places - sometimes by accident you can write code that won't ever be executed due to how language works. Type checker can find a lot of those

Stripe's type checker can check up to 100k loc/s which is much faster than other similar tools (also in other languages). How was this speed achieved? The checker was actually written in C++ not in Ruby. There is a speed gain but at the cost of loosing metaprogramming suport. Some basic patterns have been reimplemented, but for others the reflection mechanism is used.

Right now there is only a demo mentioned above. Once the final open source version is released it will be notified on the blog. If you have some feedback or ideas send it to sorbet@stripe.com 

### Takeaways

* Working type ckecker
* Fast and easy to use
* Will be released soon

### Q&A

1. **Q**: Many Ruby programmers use C where you define type in method definition. Should we have something similar for Ruby?

   **A**: Not like in Ruby

2. **Q**: How does your type annotation go with what Matz talked about type annotations becoming obsolete?

   **A**: Type annotations are useful NOW, it's no point waiting.

3. **Q**: How about typing based on YARD?

   **A**: There is a tool to translate YARD declarations into type annotations

4. **Q**: How about Rails? How about libraries with no type annotations?

   **A**: A lot of community code used in codebase. That's where reflection mechanism can help. It provide at least some partial support until type annotations get added there.

## Bozhidar Batsov - All About RuboCop

### Intro

Great stuff about Bulgaria 🇧🇬

Ruby & Rails Community Style Guides

Actually the first time speaking about RuboCop

### Notes

Why should you use something like rubocop?

* Keeping codebase consistent
* Saving developers time
* Helping Ruby language and community
  * Efficient way to update codebase (cops for finding obsolete code)

>  Lint tools **are not** a replacement for common sense

Over 300 open issues - volunteers needed 💪

@bbatsov's first gem ever - learning to work with RubyGems int the beginning

First versions of rubocop were using regular expressions, but were ultimately updated so it uses proper language parsing now. There is a tool called Ripper in ruby which lets you see how ruby reads your code but it is tied to MRI, not cross platform and had missing documentation. Since it was tied to MRI it was also tied to specific ruby version. It doesn't go well with rubocop tying to be cross platform.

When struggling with Ripper quirks finding `parser` gem, which solved a lot of problems. It was cross-platform, had good documentation and supported a lot of ruby versions. It showed nicely formatted syntax and had an option of rewriting code. But it was a new project with no real world usage yet. Rubocop used parser since version 0.8

Since then it got a lot of useful features like caching, parallel execution etc. It now has very rich configuration options for all available cops which lets you use it in any project you want. And everything is described in the [documentation](http://rubocop.readthedocs.io).

Rubocop has evolved from simple linter into a full featured code formatter.

Writing cops is as simple as writing a few methods so it's easy to add ones you need for your internal projects.

What needs to happen before rubocop 1.0 will be released?

- Removing rails specific cops (and maybe performance ones) from rubocop core
- Moving repository from bbatsov into rubocop hq organization on github
- Reviewing all configs and their defaults
- Comming up with a better process of cops updates (curently, `mry` can be used to ease the process)
- Resolving all potentil breaking changes in cops API

### Q&A

[No time left for Q&A]

## Shibata Hiroshi - RubyGems 3 & 4

### Intro

Ruby core and rubygems developer

Asakusa.rb - Rubyis group in Japan

Slides are [available here](https://www.slideshare.net/hsbt/rubygems-3-4)

### Notes

We'll start with RubyGems version 2.7. Previous versions only have security fixes, while 2.7 is a stable version and gets both bugs and security fixes.

RubyGems supports Ruby down to 1.8, which is a really obsolete version by now. There are a lot of Ruby versions to support, so there is a big build matrix for rubygems on CI.

Support for ruby 1.8 is mainly done by using `respond_to` in a lot of places. There are also two versions of rubygems: independend and bundled in ruby. The second one needs additional checks to make sure that things like bundler are available.

How rubygems installs bundler? If you look at `update_rubygems/setup.rb` you'll se how it adds bundler to default gems.

In the end sometimes rubygems and bundler are installed together while at other times they can be installed separately (depending on versions). This used to cause a lot of problems in certain environments (such as Travis CI). This was ultimately fixed in colaboration with travis team.

RubyGems security is handled by hackerone - 3 people handle vulnerability issues.

Since the project is tightly coupled to specific versions of other projects in causes some problems with maintaining code on git.

What's coming with rubygems 3.0?

Introduction of `Gem::Deprecate`, an easy way to show deprecation warnings to gem's end-users.

Way of searching code in gems with `gemsearch`. Using `all-ruby` to check ruby version compatibility. Those tools can be used to get rid of workarounds for older ruby versions.

Used tools have been updated and support for missing bundler have been added - it could be installed automatically now.

What's up with rubygems 4?

Changes to how gems are installed. Better support for standard libraries defaults.

Rubygems 4 will install to `~/.gem` by default so there will no longer be errors about not having root access. But what about compatibility with things like rbenv?

 ### Q&A

[No time for Q&A]

## Architecture of hanami applications

## Lightning talks

