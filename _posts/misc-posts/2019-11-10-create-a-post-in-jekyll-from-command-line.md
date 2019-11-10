---
layout: post
title: Create a post in Jekyll via command line
date: 2019-11-10 21:59 +0530
---

Jekyll needs all the posts to be of a certain format:  
**YEAR-MONTH-DAY-NAME-OF-THE-POST.md**

It can easily get frustrating to do it manually everytime. I found a really great way to create posts using jekyll-compose: [https://github.com/jekyll/jekyll-compose](https://github.com/jekyll/jekyll-compose)  

### Steps:

Add the following to the gemfile:
```ruby
gem 'jekyll-compose', group: [:jekyll_plugins]
```

Run the command:
```ruby
bundle
```

And now you can create a post by running:
```ruby
bundle exec jekyll post "My New Post"
```