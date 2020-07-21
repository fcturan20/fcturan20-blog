---
title: Hello, Blog!
subtitle: Netlify + Hugo + Github Pages = Magic!
date: 2020-07-21T14:22:28.096Z
draft: false
featured: false
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
This the first post of the blog. The blog is meant to help me develop my English writing skills and share my experiences. But this post is about how to have a blog. Most importants parts are done by the Hugo and Github Pages already, so the topics I'll cover requires more-or-less 1-2 hours of time to understand without reading anything because Hugo and Netlify documentations are very clear.

I only "know" C++, so writing a blog is not possible without learning any new programming language. I was keep thinking in somewhere in my mind about how to have a blog without having to learn any new language. I always see ome ads like "Wix" on Youtube but systems like these is not something i looked for. While reading [Gabriel Sassone's blog](https://twitter.com/GabrielSassone), I somehow looked at the very bottom of the side and saw "powered by the Academic theme for Hugo" and at the same week I saw someone on Twitter said "I moved my blog to Hugo + Github from Wordpress". Then I searched about it a little bit and saw that I don't need to pay anything to anyone if I want to build a nice looking blog and I can change it by coding. I was thinking to take this step and create my own blog for 4-5 months and that's the time.

I started to look at documents of "Hugo with Github Pages". Hugo is very nice, but you need to manage your website like your any coding projects. I didn't investigate it much, but there is some basic code to write your posts etc. I was looking for a some more basic solution to write posts on my phone -I'm not always on computer and writing posts on mobile is a good idea-. When I cloned the Academic Theme repository of Hugo, I saw that on the README "one-click install using your web-browser (recommended)". I followed the documentations and created the site. But the problem was I had some unnecessary things on the main page (Like demo tabs of Hugo, "Skills" tabs etc.) Because all syntax is very meaningless from a C++ user side, I was scared of changing source code. But I should've done something, so I did read the params.toml and config.toml . Params.toml had something like address informations that I want to go off. Because I didn't want any Hugo or Netlify compilation errors and US's address isn't same with TR's, I just wrote something ridiculous. Then I found that all those unnecessary tabs like Skills, Contact, Recent Talks etc are called "Widgets" and I can program them as I want in repository's content/home/ folder. For Disqus support, I needed Disqus short name. You need to subscribe as a admin to Disqus, but luckily there is a free membership. After subsciption, your "Web Site Name" is your short name.

Netlify helps me write posts on mobile but I had an issue too. Before writing any posts, I deleted "Featured Posts". At Posts section in Netlify, you see a post titled "Posts". I modified it and published it but it didn't show up in "Posts" section of the blog. I created an other post, but it didn't show up too. I got a little bit nervous, but after deleting all the posts and created the first one again, it worked! Netlify has one issue more that if you try to change your author profile picture, uploaded picture's name should be "avatar" -like "avatar.jpg"-.

That's the end of my first blog post, thanks for reading it. I hope this blog's gonna be full of GraphicsDev-GameDev posts.