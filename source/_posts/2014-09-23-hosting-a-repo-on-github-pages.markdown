---
layout: post
title: "Hosting a Repo on Github Pages"
date: 2014-09-23 12:07:35 -0400
comments: true
categories: [Github, Github&nbsp;Pages, Jekyll]
---

I'm building a small Backbone todo-list app (because there will never be enough of those!) as a learning exercise.  And I was running into difficulties when I started using the RequireJS text plugin; my browser was complaining about `Cross origin requests are only supported for HTTP` when running totally locally.  Looking into it, I discovered I needed to run on a local server, so I could serve everything over HTTP.  A friend had been telling me about hosting on github-pages, but I already had this blog app running on my gitub-hosted site.  Could I host the todo app as a sub-page on my site?

### Github's Project Pages

Well, figuring out how to host multiple repos on a Github Pages site was absurdly easy.  [Here's a link to the official Github documentaion](https://help.github.com/articles/creating-project-pages-manually).  Those instructions are geared to hosting a simple static information site, but you can also use project pages to host an HTML/CSS/JS only application.  All you need to do is:

- Ensure you have an `index.html` page in your root directory
- Create an orphan branch named gh-pages: `git checkout --orphan gh-pages`
- `git push origin gh-pages`

And now, the contents of `index.html` will be available on `[your-github-site]/[repo-name]`.  It can take a while to register the changes.  The docs say up to 15 minutes, but for me it's usually done in less than a minute.

### Running as a local server

So that deployed the app to the Github Pages app, but I still need a local server for development.  Fortunately, if you use Ruby, it's simple!  All you need is to setup a Gemfile, add `gem 'github-pages'`, `bundle install`, and finally `bundle exec jekyll serve`.  Now there's a basic local server running at localhost:4000.  I'm sure this could get a lot more complicated, but that's all I need right now.