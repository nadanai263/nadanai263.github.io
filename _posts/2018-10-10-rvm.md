---
layout: post
title: "RVM"
date: 2018-10-10
---

Today I got RVM to work, after trying to follow the completely accurate but not so helpful [official documentation](https://rvm.io/rvm/install), and then resorting to more friendly tutorials (like [this one](https://usabilityetc.com/articles/ruby-on-mac-os-x-with-rvm/) and [this one](https://sirupsen.com/get-started-right-with-rvm/)). RVM is the Ruby Version Manager. The idea is the same as with python virtual environments. Basically you install RVM, which allows you to then have multiple independent versions of Ruby, completely separate from the system Ruby which you don't touch. Ruby packages ('gems') are also installed under what are called 'gemsets'. You can import and export gemsets in the same way as python's pip requirement files, and additionally you can match a gemset to different versions of Ruby. So it's a nice way of keeping everything organised: for a given project, you can specify the Ruby version as well as a gemset, which are its dependencies. 

I wanted to do this so I could look at my website locally, without having to wait for Github to build it every time (which takes a couple of minutes). 

The problem was my Homebrew was completely mucked up. After reinstalling Homebrew (and my [Fish](https://fishshell.com) shell), I needed to install `gpg`

	brew install gnupg 

followed by RVM itself

	gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB

	curl -sSL https://get.rvm.io | bash -s stable

 which uses some key authentication I don't understand yet. This installation also brings in the latest version of Ruby and sets that as RVM's default. To make it work with Fish shell I had to run

	curl -L --create-dirs -o ~/.config/fish/functions/rvm.fish https://raw.github.com/lunks/fish-nuggets/master/functions/rvm.fish

 and add one line to my `config.fish` file:

	echo "rvm default" >> ~/.config/fish/config.fish.
 
 To test the installation, I ran some basic commands. `rvm system` switches to the system Ruby, and `rvm default` switches to the new pristine Ruby (version 2.5.1 in my case). I created a new gemset called lbnc with `rvm gemset create lbnc`, checked what was in it using `gem list`, and installed the github-pages gem using `gem install github-pages`. Finally, I navigated to the local clone of my site's repository and ran `jekyll serve`, which builds a `_sites` folder containing the generated html files, and starts the server. Opening `http://127.0.0.1:4000` on my browser then lets me look at my site. Finally, it was working! 
