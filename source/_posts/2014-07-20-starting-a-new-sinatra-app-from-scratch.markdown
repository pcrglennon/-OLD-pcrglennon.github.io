---
layout: post
title: "Starting a new Sinatra App from Scratch"
date: 2014-07-20 13:49:52 -0400
comments: true
categories: [Ruby, Sinatra, Bootstrapping]
---

My friend [Justin](https://github.com/jusjmkim) and I are working on a small app to present at The Flatiron School in two weeks.  The app itself isn't hugely interesting: we're basically allowing a user to save a point on a Google Map and share it.  And really, that's just repeating some functionality of Google Maps itself.

Since the app will be relatively small and simple, we decided to use Sinatra instead of Rails.  One of the more interesting challenges we've faced so far is setting up the framework of the app itself.  It's easy to get used to all the scaffolding you get by just typing `rails new`.  Starting from an empty folder was really a project in itself, when you haven't done it before.

Of course, there are a number of apps which set up the structure of a barebones Sinatra app (such as [Trevi](https://github.com/maccman/trevi) or [Hazel](https://github.com/c7/hazel)), but we decided to do it by hand.

Here's what we did, in a very rough order:

###Gemfile:

I don't think I had ever created a Gemfile before.  I always relied on it just...being there.  Fortunately, [Bundler](http://bundler.io/) has quick setup instructions right on its homepage.

Setting up the Gemfile by hand also forced Justin and I to make decisions about what we REALLY needed for our app.  We chose to do without ActiveRecord, as the app will only have one table and maybe 5-6 SQL queries all told.  In a similar vein, we had to stop and think about which gems are needed in which environment.

###Environment file/Configuration file:

Getting everything to work together was definitely the most difficult part.  Again, I'm used to a Rails app, where it just works right away.  We had to go back to old exercises from Flatiron to set up the `environment.rb` file.

Step 1: Ensure there is a variable set (in ruby's global `ENV` hash) which represents which environment to use.  Default to 'environment'.  i.e. something like `ENV["SINATRA_ENV"] ||= 'development'.  **Note:** we have yet to deploy this to production, so it's possible this won't be enough.

Step 2: Require bundler, and use it to require all gems for the environment in use.

```ruby
require 'bundler'
Bundler.require(:default, ENV['SINATRA_ENV'])
```

Step 3: Set up the DB connection (if using a DB, that is).  We used SQLite3, again for simplicity.

```ruby
DB = {:conn => SQLite3::Database.new("./db/your_db.db")}
```

Step 4: Requiring your Ruby source files.  In this tiny project, we really only had one file to require (the 'Map' model which stores a name and a coordinate), so this was easy.  In a larger project, it would be best to use something like [require_all](https://github.com/jarmo/require_all).


###The Sinatra App and config.ru

And of course, we need an actual Sinatra Application, and a means of running it!  There are (at the moment) only 3 routes needed, so we bundled them all into a file called `app.rb`, which lives in our root folder.  That app does 4 things:

1. `require /.config/environment`.  Duh.
2. Extend `Sinatra:Base`
3. Set up route handlers.  In our case, this is just `get '/'`, '`post '/'`, and `get '/:map_name'`.
4. Other configuration options. For us, we had to set up a file to store our Google Maps API key, and make sure that key is accessible by the application.  We used the common pattern of creating a file `config.yml` which we don't track in version control (not wanting to expose our key to the world!), and loading configuration options using Sinatra's `set`.

```ruby
configure do
  yaml = YAML.load_file("config/config.yml")[settings.environment.to_s]
  yaml.each_pair do |key, value|
    set(key.to_sym, value)
  end
end
```
In order to run this app, all we have to do to is set up the `config.ru` file.  Once again, thanks to the simplicity of our app, this is easy.  Only two lines:

```ruby
require './app'
run App
```

Run `rackup` in your terminal, point your browser to localhost:9292, and you're good to go!