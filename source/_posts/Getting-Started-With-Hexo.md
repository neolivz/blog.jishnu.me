---
title: Getting Started With Hexo
date: 2016-12-31 02:13:24
tags: [hexo, nodejs, blog, github]
---
## Introduction

Finally I decided to start with my own blog. So naturally the question came up was which one to use? After careful consideration I decided to go for [Hexo](https://hexo.io), which would be an ideal blogging platform. So naturally the first blog entry is how to setup Hexo and deploy in the server.


## Getting Started With Hexo
You would need nodejs and npm installed in your machine.

```
npm install hexo-cli -g
hexo init jishnu.me
cd blog
npm install
hexo server
```
Where `jishnu.me` is the folder the code will be generated. You can see the blog in port 4000 on your localhost.

## Creating First Post

All the posts go to the folder `blog/source/_posts/` and are of type `markdown (.md)`

`hexo-cli` provides command line tool to create new posts

```
hexo new post "Getting Started With Hexo"
```
this will create a file called `Getting-Started-With-Hexo.md` under the `blog/source/_posts/` folder.

Open the folder `jishnu.me` in your editor. You can start writing your blog.

## Creating github page

You will need to create a public repository with `yourGithubUserid.github.io` repository for you to have github page. I am going to use this page as my blog.

`https://github.com/neolivz/neolivz.github.io` would be my github project name for my github page since my github userid is `neolivz`.

PS:
Every time you make a build you need to commit from submodule and followed by committing in the parent module.

## Adding Github Page as a Submodule to your project

Add your github page as submodule to your project. This will help to generate the static pages and directly check into the
```
git submodule add git@github.com:neolivz/neolivz.github.io.git
```

###NOTE
If you are cloning a fresh checkout submodules won't be already fetched. So you will have to update the submodule with
```
git submodule init
git submodule update
```

## Configuring your hexo project

In the file `_config.yml` makes the necessary edits, title subtitle, url etc.

Apart from that change the default `public_dir` to your newly added submodule. So in my case it would be


## Creating Static Site

Next step is creating a static site that we can host

```
hexo generate
```

This will generate a static folder which you can host anywhere. Since the generated files are in submodule of static page of your github it will be coming into your static page. Checkin everything to see if its working.
