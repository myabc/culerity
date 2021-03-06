## Introduction

Culerity integrates Cucumber and Celerity in order to test your application's full stack.

Culerity lets you:
* run Celerity from within Cucumber which allows you to test the full stack of your Rails (or other web) application from Database to in browser JavaScript
* run your application in any Ruby (like MRI 1.8.6) while Celerity runs in JRuby so you can still use gems/plugins that would not work with JRuby
* reuse existing Webrat-Style step definitions

## Getting Started

The following guide is written for a Rails application (tested with 2.2.2) but Culerity should work with any other Web Framework that is supported by Cucumber.

First download JRuby and unpack it to some location, for example $HOME/jruby. Make sure that the jruby executable is in your path. You can do this by either setting your PATH accordingly...

    export PATH=$HOME/jruby/bin:$PATH
 
... or by creating a symlink from your bin directory:

    ln -s $HOME/jruby/bin/jruby /usr/bin/jruby

You will need the celerity gem installed into JRuby:

    jruby -S gem install celerity

Now (assuming you have a Rails application set up already) install Culerity as a Rails Plugin:

    cd RAILS_ROOT
    git clone git://github.com/langalex/culerity.git

or as a gem: (definitely preferred)

    gem install langalex-culerity --source http://gems.github.com
 
Run the RSpec, Cucumber and Culerity generators:

    cd RAILS_ROOT
    script/generate rspec
    script/generate cucumber
    script/generate culerity

This creates the features folder and a file common_celerity.rb into your application. This file contains step definitions for basic interactions like clicking links or filling out forms.

Culerity is a Ruby-side interface to celerity running on JRuby. To install it:

    rake culerity:install
 
After you have written a first feature you can run it just like you would run a standard cucumber feature. The only difference is that you have to start a web server (e.g. mongrel) with the test environment enabled beforehand.

    rake culerity:rails:start
    cucumber features/my_feature.feature

The Rails instance uses a special environment culerity_development.

When you have finished running culerity/cucumber you can turn off the Rails instance:

NOTE: The default port for this server is 3001. You can change this in features/step_definitions/common_celerity.rb

    rake culerity:rails:stop

## How does it work

While Celerity is based on Java and requires JRuby to run, with Culerity you can still run your tests in your own Ruby Environment. When you run your features a separate JRuby process for Celerity is spawned and all Celerity Commands are redirected to this other process.

## Troubleshooting

I get a broken pipe error:

* make sure JRuby is installed and in your path: running _jruby -v_ should not produce an error

I get _Connection Refused_ errors

* make sure you have started a server in the test environment that runs on port 3001

My application can't find the data I create in my steps

* make sure you have disabled transactional fixtures in your env.rb

My database is not cleared automatically between scenarios

* Rails can't clean the database because you had to disable transactional fixtures - which only work if your test process is the same as your web server process. Hence you have to clean your database manually. A quick way would be:

    Before do
      [User, .... all your models].each do |model|
        mode.delete_all
      end
    end

I want to test emails but the ActionMailer::Base.deliveries array is always empty.

* that's because the :test delivery method stores all emails in memory, but now that your test process is not the same as your rails process you don't have access to that array. install the http://github.com/ngty/action_mailer_cache_delivery plugin to access the emails in your rails process via a temporary file


## Links to Celerity documentation

* [How to select elements](http://celerity.rubyforge.org/yard/Celerity/Container.html)
* [FAQ](http://wiki.github.com/jarib/celerity/faq)
* [Tutorial](http://wiki.github.com/jarib/celerity/getting-started)
* [API docs](http://celerity.rubyforge.org/yard/)

## Links

* [Cucumber](http://github.com/aslakhellesoy/cucumber/wikis)
* [Celerity](http://celerity.rubyforge.org)
* [JRuby](http://jruby.org)
* [RSpec](http://rspec.info)
* [HtmlUnit](http://htmlunit.sourceforge.net/)

## Contact

Written 2009 by Alexander Lang, contact alex[at]upstream-berlin.com or <http://github.com/langalex>, released under the MIT license
