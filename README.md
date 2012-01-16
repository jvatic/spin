Spin
====

Spin is dead simple to use and makes autotesting with Rails faster.

Installation
===========

Spin is available as a rubygem.

``` ruby
gem i spin
```

Spin is a tool for Rails 3 apps. It is compatible with the following testing libraries:

* any version of test/unit or MiniTest
* RSpec 2.x

Usage
=====

Start up `spin` the same way you would `autotest`, with a single command:

``` bash
spin
```

Under the hood that fires up two processes, one to watch files and one to preload your Rails env. Each test run involves forking a child process off of the master. So each time you skip the cost of loading gems, rails, and the basic initialization.

## Batched Testing

## Always Listening

## Backwards Compat

``` bash
spin push test/unit/product_test.rb
```

Or push multiple files to be loaded at once:

``` bash
spin push test/unit/product_test.rb test/unit/shop_test.rb test/unit/cart_test.rb
```

Running a single RSpec example by adding a line number is also possible, e.g:

``` bash
spin push spec/models/user_spec.rb:14
```

Send a SIGQUIT to spin serve (`Ctrl+\`) if you want to re-run the last files that were ran via `spin push [files]`.

Custom Paths
============

Custom Preloading
=================

Motivation
==========

A few months back I did an experiment. I opened up the source code to my local copy of the ActiveRecord gem. I added a line at the top of `active_record/base` that incremented a counter in Redis each time it was evaluated. After about a week that counter was well above 2000!

How did I load the ActiveRecord gem over 2000 times in one week? Autotest. I was using it all day while developing. The Rails version that the app was tracking doesn't change very often, yet I had to load the same code over and over again.

Given that there's no way to compile Ruby code into a faster representation I immediately thought of fork(2). I just need a process to load up Rails and wait around until I need it. When I want to run the tests I just fork(2) that idle process and run the test. Then I only have to load Rails once at the start of my workflow, fork(2) takes care of sharing the code with each child process.

I threw together the first version of this project in about 20 minutes and noticed an immediate difference in the speed of my testing workflow. Did I mention that I work on a big app? It takes about 10 seconds(!) to load Rails and all of the gem dependencies. With a bit more hacking I was able to get the idle process to load both Rails and my application dependencies, so each test run just initializes the application and loads the files needed for the test run.

(10 seconds saved per test run) x (2000 test runs per week) = (lots of time saved!)

### How is it different from Spork?

There's another project ([spork](https://github.com/sporkrb/spork)) that aims to solve the same problem, but takes a different approach.

1. It's unobtrusive.

    Your application needs to know about Spork, Spin works entirely outside of your application.

    You'll need to add spork to your Gemfile and introduce your `test_helper.rb` to spork. Spork needs to know details about your app's loading process.

    Spin is designed so that your app never has to know about it. You can use Spin to run your tests while the rest of your team doesn't even know that Spin exists.

2. It's simple.

    Spin should work out of the box with any Rails app. No custom configuration required.

3. It doesn't do any [crazy monkey patching](https://github.com/sporkrb/spork/blob/master/lib/spork/app_framework/rails.rb#L43-80).

Docs
============

[Rocco](http://rtomayko.github.com/rocco/)-annotated source:

* [spin](http://jstorimer.github.com/spin/)
    * [spin serve](http://jstorimer.github.com/spin/#section-spin_serve)
    * [spin push](http://jstorimer.github.com/spin/#section-spin_push)

Hacking
=======

I take pull requests, and it's commit bit, and there are no tests.

Architecture
============

Related Projects
===============

If Spin isn't scratching your itch then one of these projects might:

* [guard-spin](https://github.com/vizjerai/guard-spin)
* [Spork](https://github.com/sporkrb/spork)
* [TestR](https://github.com/sunaku/testr)

