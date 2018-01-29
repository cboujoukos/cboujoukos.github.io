---
layout: post
title:      "Sinatra, ERB, and Sinatra::ContentFor"
date:       2018-01-25 17:29:19 -0500
permalink:  sinatra_erb_and_sinatra_contentfor
---

For those of you that are unfamiliar with Sinatra, Sinatra is a Rack-based DSL (Domain Specific Language) that is utilized in creating web applications. Including Sinatra in your projects unlocks a ton of useful methods and adds some functionality to your application. In today's blog I want to take you through one of the many add-on features that have been built around Sinatra, specifically, Sinatra::ContentFor, which is a part of Sinatra::Contrib.

If you have worked with Sinatra before, you have certainly become familiar with the convential file system associated with Sinatra apps.

!["Sinatra MVC File Structure"](https://i.imgur.com/toNkNxR.png)

If you have used this MVC structure, then you have probably also seen something like this:
```
<%= yield %>
```
 nestled within the body of your layout.erb view (If you use haml or slim, dont worry about the `<%=  %>` syntax, it's just erb's way of telling the program to interpret the enclosed script as ruby). This is an incredibly handy tool, since it allows you to DRY up your code by omitting a a bunch of html in all the rest of your views. Basically, we code a skeleton of our html in the layout page (anything between html <head> tags for example, along with navbars, headers, links to js and stylesheets, and anything else that conceivably you would want displayed or accessible on every page of your site), and then in the body of layout.erb, we include that little `<%= yield =>` and then the block of code from any of our other views gets passed in there. Pretty neat, right!? Well what happens when you want something else that will be present in every view to dynamically change as you navigate around your app? You can't include multiple calls to `<%= yield =>` because your program won't know what to pass where. Okay, so lets say you want each view of your app to have a different `<title>`. If you have a layout page as described above, and are utilizing `<%= yield =>` in the body of that document, then you are stuck with the same title on every page of your app (whatever you passed in as a title in the head of the layout page). Enter Sinatra::Contrib. This little gem adds some much needed extra functionality to your sinatra app.

Setting it up is as simple as including `gem 'sinatra-contrib'` to your Gemfile, and then running a quick `bundle install`. Alternatively, if you don't use Budler for whatever reason, you can install the gem manually by executing `gem install sinatra-contrib` from the command line. Once installed, its usage is just as easy. 

So now that you have included sinatra-contrib in our project, we need to add the line `helpers Sinatra::ContentFor` in your ApplicationController (or whatever controller you have inheriting from Sinatra::Base).

Your main controller will look something like this:

```
class ApplicationController < Sinatra::Base
  helpers Sinatra::ContentFor

  configure do
    set :public_folder, 'public'
    set :views, 'app/views'
    enable :sessions
    set :session_secret, "password_security"
  end

  get '/' do
    if logged_in?
      @user = User.find(session[:id])
      @campaigns = @user.campaigns
      erb :index
    else
      erb :'/users/login'
    end
  end


  helpers do
    def logged_in?
      !!session[:id]
    end

    def current_user
      User.find(session[:id])
    end
  end
end

```

Now that our app is all set up to use Sinatra::Contrib, lets take a look at how my favorite feature works. Going back to our example above, I want each of my views to have a unique title. So how do I accomplish that? With Sinatra::Contrib's content_for, that's how. In my layout, where ever I want to yield some secondary content, I can include `<%= yield_content :some_key %>`. So in our example I would write `<title><%= yield_content :title %></title>` in the `<head>` section on layout.erb. Now, from any of my views, I can call `content_for`, specifying the key and what I want rendered, and it will be yielded to the appropriate `yield_content`.
```
<% content_for :title do %>
  My Sinatra App - Home
<% end %>
```
By including something like that within each of my views, I accomplish what I set out to do, include a unique title in every separate view. You can also provide a default value to be yielded in case not content is provided in a particular view. To do so, in layout.erb, I would call:
```
<%= yield_content :title do %>
  My Sinatra App
<% end %>
```
By including that snippet in the layout, if the program is unable to find the` content_for` statement in a particular view, it will default to rendering result of `yield_content`.

There are a ton of other useful tools inluded in Sinatra::Contrib, I know that I look forward to exploring more of them, and I hope you will do the same.


