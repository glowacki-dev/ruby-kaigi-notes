# Day 3:

## Benoit Daloze - Parallel and Thread-Safe Ruby at High-Speed with TruffleRuby (Keynote)

### Notes

TruffleRuby has two startup modes: either on JVM or on SubstrateVM which compiles Ruby to native executable. 

With Ruby 3x3 comming up there is a plan to make CRuby 3.0 3 times faster that 2.0, but do we have to wait until 3.0 (until at least 2020)? And should we only aim for 3x speedup?

[Demo of running OptCarrot with CRuby and TruffleRuby]

TruffleRuby already achieves close to 5x faster CPU performance than CRuby. Apart from CPU intensive tasks, TruffleRuby is also much faster at tasks like rendering ERB templates due to more performant String implementation. But it's still in early stages, so running big projects like Discourse is still a challenge. There are a lot of dependencies and many C extensions which need manual patching.

TruffleRuby achieves such speedup with Partial Evaluation and Graal JIT Compiler. 

![truffle_partial_evaluation](media/truffle_partial_evaluation.jpg)

Partial Evaluation speeds up a method execution chain by inlining methods, blocks and constant expressions thus simplifying the AST. Another thing that is done under the hood is optimizing data types usage. For example, while iterating you don't necesairly have to use ruby array, it can be changed to native jvm array which will be faster. Since we compile the code it's possible to perform even more optimizations like constant folding.

On the other hand we have different approach in CRuby and MJIT. It uses Ruby bytecode to create C libraries of methods that are used most often and use those libraries instead of Ruby version. But MJIT doesn't have access to Ruby binary so it cannot optimize calls to internal methods. We should focus on expanding JIT with a way to understant native Ruby methods so it can use this for further optimization.

The next part of the talk will be about Parallel and Thread-Safe Ruby. Dynamic languages tend to have poor support for parallelism, because of their implementation. Both CRuby and CPython have GIL which makes it impossible to execute parallel code in a single process. Solutions like JRuby or Rubinus have unsafe calls, so things like using `Array#<<` concurrently raises an exception.

Those problems are being slowly tackled by Guilds. It focuses on better memory model with almost no shared memory and no low level data races. But it would require many libraries to be rewritten, as it uses different approach than Ruby Threads.

If we try to concurrently append to an array we won't get problems in CRuby (but no real parallelism as well). Doing this with JRuby or Rubinus would require us to create a mutex, but it adds a significant overhead. Since Arrays are thread-safe in CRuby people assume that they will behave the same in other implementations, which leads to a lot of errors.

That's actually a hard problem. How do we make a collection thread-safe but don't add overhead for single-threaded operations? In dynamic languages there is a similar problem regarding objects. All objects' fields are basically a collection that can be modified at any given time. 

The idea for solving this is to only wynchronize objects that can be accesed by multiple threads. If something is used in a single thread only there is no need to add synchronization mechanism.

### Takeaways

TruffleRuby runs unmodified Ruby code faster. It also supports safe parallel execution (safe arrays will be available in a few months). It also supports Ruby Threads so there is no need to rewrite any piece of your code.

It shows that we can achieve high performance for Ruby with parallelism and no single-threaded overhead.

Try out TruffleRuby by installing it with your ruby manager of choice. 

### Q&A

1. Q: The setup time for TruffleRuby is but. Are there any plans to improve startup performance?

   A: We want to improve on this and there are some works already in progress. Loading code is indeed slower than it should be and improving this will be the next step.

2. Q: Is Partial Evaluation done by you or is there some framework for this?

   A: This is similar approach to what has been used for JavaScript. Actually AST interpreter doesn't care about the language.

3. Q: How does the memory usage differ between the implementations?

   A: SubstrateVM has bettter memory usage than JVM version.

