---
layout: post
title: 'Welcome to Jekyll!'
categories: 'jekyll update'
---

# Jekyll를 이용한 GitHub Pages 만들기

> - Requirements
- Step 1: Create a local repository for your Jekyll site
- Step 2: Install Jekyll using Bundler
- Step 3 (optional): Generate Jekyll site files
- Step 4: Build your local Jekyll site

## Requirements
	$ ruby --version
	ruby 2.X.X

------------



    $ gem install bundler
    # Installs the Bundler gem

------------

## Step 1: Create a local repository for your Jekyll site

	$ git init JEKYLL-SITE-REPOSITORY-NAME
	> Initialized empty Git repository in /Users/octocat/my-site/.git/
	# Creates a new file directory on your local computer, initialized as a Git repository

------------
	$ cd JEKYLL-SITE-REPOSITORY-NAME
	# Changes the working directory


------------


	$ git checkout -b gh-pages
	> Switched to a new branch 'gh-pages'

------------
## Step 2: Install Jekyll using Bundler

	$ ls
	> Gemfile

------------
	source 'https://rubygems.org'
	gem 'github-pages', group: :jekyll_plugins

------------



	$ bundle install
	 Fetching gem metadata from https://rubygems.org/............
	 Fetching version metadata from https://rubygems.org/...
	 Fetching dependency metadata from https://rubygems.org/..
	 Resolving dependencies...

------------

## Step 3 (optional): Generate Jekyll site files
	$ bundle exec jekyll _3.3.0_ new NEW-JEKYLL-SITE-REPOSITORY-NAME
	> New jekyll site installed in /Users/octocat/NEW-JEKYLL-SITE-REPOSITORY-NAME.

------------


	$ cd NEW-JEKYLL-SITE-REPOSITORY-NAME

------------
	$ "jekyll", "3.3.0"
------------
	$ gem "github-pages", group: :jekyll_plugins

------------
	$ git init	

------------


	$ git remote add origin https://github.com/username-or-organization-name/your-remote-repository-name
	

------------


	$ git add .

------------
	$ git commit -m "updated site"

------------
	$ git push -u origin master

------------
## Step 4: Build your local Jekyll site

	$ bundle exec jekyll serve
	 Configuration file: /Users/octocat/my-site/_config.yml
	            Source: /Users/octocat/my-site
	       Destination: /Users/octocat/my-site/_site
	 Incremental build: disabled. Enable with --incremental
	      Generating...
	                    done in 0.309 seconds.
	 Auto-regeneration: enabled for '/Users/octocat/my-site'
	 Configuration file: /Users/octocat/my-site/_config.yml
	    Server address: http://127.0.0.1:4000/
	  Server running... press ctrl-c to stop.
	Preview your local Jekyll site in your web browser at http://localhost:4000.
