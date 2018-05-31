# Day 1:
## Matz Keynote
Names are important (what was the proverb?) -> especially in programming

Programming is not physical, it's made of concepts, behaviors. Good naming make a code more usable. Bad example would be `yield_self`
```ruby
def yield_self
  yield self
end
```
it literally describes what it does -> Feature #14594 about better name for yield_self. How about "then"? It would chain nicely. (Matz doesn't really commit into the c ruby too often - he does for MRI - this was first commit in 5 years apart from versions bumps)

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

   **A**: There will never be typing in ruby language definition. There is type annotation in python. Typing is useful for compiled languages, because it helps with finding bugs, but types can actually be recognized by computers automatically, so you won't have to defie static typing in language specification.

1. **Q**: Are there plans for high level constructs for ruby? Something like macros?
  
   **A**: We have to care about compatibility so such things probably won't come before 3.0, but there are no concrete plans for such things.

1. **Q**: Why is it exactly that we don't want type annotations?

   **A**: Because in the future type annotations will most likely be unnecessary and become obsolete so there is no point adding them.

[Lunch break]

## 