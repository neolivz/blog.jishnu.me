---
title: The Beauty Of The Yarn
date: 2017-03-08 17:59:09
tags: [yarn, npm, verdaccio, sinopia]
---

# History
About a 4 months back when I was coming back from my India trip, I wanted to make use of my time in flight by doing som RnD on React and Aurelia. I had two option either setup everthing before hand and work with it or find a way to do offline installation. After some googling (`local npm server`) I came across a package called [sinopia](https://github.com/rlidwka/sinopia), which is a local npm server and npm install fetches result from that. It's uber cool with one problem. It was broken, newer versions of node and npm were not supported. Futher asking help of god-all-knowing (read as google) I came across a port of `sinopia` called [verdaccio](https://github.com/verdaccio/verdaccio) which works fine and was using it till the point I stumbled across [yarn](https://yarnpkg.com/) from one of the old JS Weekly mails. Once you open your eyes, you will never go back.

# Why Yarn?
Well easier question is why not yarn? The simple answer is No, there is no reason not use yarn other than being in the comfortablity of familiarity of npm.

## Fast
Yarn is really fast, really really fast. Yarn caches your packages locally so next time it's even faster. For the example let me use [Aurelia-TypeScript-Webpack-Skelton](https://github.com/aurelia/skeleton-navigation/tree/master/skeleton-typescript-webpack). It took around ~50 seconds with npm (`version 4.1.2`) while yarn (`version 0.21.3`) completed in 28 seconds. If I have to delete the node_modules folder and reinstall npm will take the same time while yarn has cached local copy of the packages it finishes in 18.5 seconds, almost 3 times as fast as npm. So it is FAST!


## Deterministic
The biggest dev problem with npm is that it's not deterministic. If you install a package `npm install --save xyz@1.0.0` it will install package xyz of the version 1.0.0 which is expected result but in package.json dependency is created as `"xyz":"^1.0.0"`, which means it would install any package as long as major version is not changes. So the next dev comes and runs `npm install` in his machine. He would get the latest `1.x` version, something like `xyz@1.20.0` rather than getting `1.0.0`. This will become a problem as you will face different issues for each developer rather one version for every developer. This can be solved by using `shrinkwrap` but again a manual process and which can create issues. Yes, apart from that [this](https://docs.npmjs.com/how-npm-works/npm3-nondet) shows non deterministic nature of npm.
Yarn solves this by doing two things, `yarn add` adds exact version `"xyz":"1.0.0` not with cap and creating `yarn.lock` file which is very similar to shrinkwrap along with checksum. Now once you checkin both package.json and yarn.lock file into the version control, anyone who checks out the code will get exact same version.

## Secure*
It's secure becuase it uses sha1 checksum. From yarn website "Yarn uses checksums to verify the integrity of every installed package before its code is executed.". I am no security expert but I guess integrity check provides one additional level of security for something like man in the middle attack.

## Conclusion
I am not big fan of the secure part, mainly I don't think it gives a big leg-up over npm. Apart from that yarn hands down better than npm.

