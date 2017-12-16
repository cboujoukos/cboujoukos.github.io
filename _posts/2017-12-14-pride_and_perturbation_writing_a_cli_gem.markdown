---
layout: post
title:      "Pride and Perturbation: Writing a CLI Gem Part I"
date:       2017-12-14 22:53:25 +0000
permalink:  pride_and_perturbation_writing_a_cli_gem
---


This week I embarked on a new type of journey: writing a program from scratch. The assignment was to write a CLI gem that scraped a website, and to build it from scratch. Those last two words, 'from scratch', made the prospect all the more daunting. The first obstacle to overcome was deciding what I wanted my program to do.  My initial thought process in approaching this question went something like this: "Oh my god. Where did my training wheels go?!" After my minor freakout, though, I started to consider what kind of CLI gem I, as an end user, would actually want to use. In answering that question, I decided that my gem would scrape the Ruby Core API docs and function as a command line ruby dictionary. 

With that first hurdle behind me, It was time to code. I decided to dive right in. Learn by doing. What I *should* have been doing was getting out a pen and paper and thinking about what kind of object relationship model would make sense for the program I was writing. More on that later.

A couple of hours in and I was feeling great. I had picked out all of the CSS selectors that Nokogiri would use to scrape relevant information from the ruby-doc website, I had set up a nice readme and started stubbing out my classes, and so far all of the files were working together with the `require_relative`s I had set up. It was time to actually set up my Scraper class. This is where I started running into some issues. Like I said, I had picked out all of me CSS selector beforehand, so in theory I should have been good to go. Unfortunately, in practice, it wasn't so simple. Much of what I was scraping turned out to have other `code`, `span`, or `a` elements nested within the element that I was scraping for. For example, `m.css(".method-heading + div p").inner_html` was supposed to provide me with a description of each method. Here is what that selector spit back in my face for the array instance method, `#&`.

> => "Set Intersection — Returns a new array containing unique elements common to\nthe two arrays. The order is preserved from the original array.It compares elements using their <a href=\"Array.html#method-i-hash\">hash</a> and <a href=\"Array.html#method-i-eql-3F\">eql?</a> methods for efficiency.See also <a href=\"Array.html#method-i-uniq\">#uniq</a>."

As you can see, there are minor issues. So what did I do? I applied a ridiculous set of `gsub`s to eliminate the problem, of course. Allow me to show you an example. `m.css(".method-heading + div p").text.gsub(/<.{2,5}>|\n/,"").strip.gsub(/&lt;|&gt;|&amp;/, '&lt;' => "<", '&gt;' =>">", '&amp;' => "&")`. Pretty, isn't it? I was so proud that I was able sub out all that ugly HTML! Well it turns out that, as you probably guessed, there is a more eloquent solution. `m.css(".method-heading + div p").text`.  Almost too easy, I know. I suppose that is what I get for not reviewing my selectors beforehand.


Before long, all of my stubbed out code was real, functional code. I had a command line dictionary up and running. The pride I felt the first time my program executed without any bugs is hard to put into words. I had built something out of nothing. That is a great feeling, and it really solidified my belief that I made the right decision by enrolling at Flatiron School. Unfortunately I wasn't quite out of the weeds yet, because as I have discovered, a functional program is not necessarily a finished program. 

Stay tuned later this week to hear the rest of my woes and exultations in Part II.

