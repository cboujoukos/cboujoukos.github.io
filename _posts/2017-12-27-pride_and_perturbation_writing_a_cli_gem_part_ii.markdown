---
layout: post
title:      "Pride and Perturbation: Writing a CLI Gem Part II"
date:       2017-12-27 16:26:25 +0000
permalink:  pride_and_perturbation_writing_a_cli_gem_part_ii
---


Welcome back. If you haven’t read [Pride and Perturbation: Writing a CLI Gem Part I](https://cboujoukos.github.io/pride_and_perturbation_writing_a_cli_gem), you should probably give it a once-over, if you want any context at all for the following post. Or don’t, totally your call. So I had just finished telling you all how proud I was. I felt like a new parent, and my CLI gem was like my beautiful, newborn babe, opening its eyes and seeing the world for the first time.

The real heartbreak came the next day. I had scheduled a 1-on-1 with an instructor to go over a couple of questions I had. By the time my appointment rolled around, I had a fully-functioning program (still somewhat bug-ridden, but functional). The main question that I had for the instructor had to do with the general architecture of my program. You see, I designed the program with a CLI class, a Scraper class, a Klass superclass, and nine child classes inheriting behavior from the parent. I had one child for each of the Ruby classes that I wanted my "dictionary" to cover. As you may imagine, my code wasn't exactly what you might call DRY code with that type of set-up. I had already moved a lot of the methods into the superclass and a Findable module in order to reduce repetition, but just looking at my file directory, I knew that it didn't look right.

A quick look over my program and the instructor pointed out the obvious: my nine child classes would function better as instances of the Klass class, and it would more appropriately model the relationship between Class and Methods. Unfortunately, the way I had designed the code, both the Scraper and the CLI relied on these nine child classes in order to function. After we discussed my options, we decided that changing the model now would require a total overhaul of the entire codebase, which may or may not have any impact on the actual functionality of the program.

An internal struggle ensued. On the one hand, I worked really hard on the code, and was thrilled to have it up and running! On the other hand, I'm not here to learn how to write sloppy code. So I started to rewrite my entire program. Luckily a lot of the gruntwork was already done the second time around. I didn't need to figure out Nokogiri selectors, or set up all the gemspec files and whatnot. So this time, I started with what I should have done in the first place. I mapped out the relationship between the different objects I would be working with. A Klass has many Methods, and a Method belongs to a Klass.  That was a good starting point. From there I was able to quickly stub out the functionality that I wanted to see, and then start making it happen. With the end in mind, and a strong understanding of the objects and relationships that I needed to encapsulate, code starting flowing.

I learned a ton working on this program, but if I could impart just one pearl of wisdom to you, dear reader, it would be this: Begin with the end in mind. Understand where you want your code to go, and *then* figure out how to get there. As much as you may want to just jump in and start writing code, restrain yourself. It will save you both time and hardship in the long run. 

[Part I](https://cboujoukos.github.io/pride_and_perturbation_writing_a_cli_gem)

