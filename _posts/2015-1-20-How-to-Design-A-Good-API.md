---
layout: post
title: How to Design A Good API
date: 2015-5-21
categories:
  - 笔记
description:
image: http://vibetickets.co.uk/wp-content/uploads/2016/03/restful-300x300.png
image-sm: http://vibetickets.co.uk/wp-content/uploads/2016/03/restful-300x300.png
---

### Characteristics of good API
* Easy to learn
* Easy to use,even without documentation
* Hard to misuse
* Easy to read and maintain code that uses it
* Sufficiently powerful to satisfy requirements
* Easy to extend
* Appropriate to audience

#### So,what makes an API a good one?
* It must be easy to learn.You don't want developers constantly checking the documentation,so the names ou use for methods,classes,arguments,and so on need to be self-explanatory when possible.
* The API also has to be hard to misuse,so make it as rock solid as you can.
* It has to be easy to read and maintain because you will have to deal with that code for a long time,especially if it gets popular among other users who will have many feature requests and bug reports.
* The API has to be powerful enough to satisfy your requirements. Sometimes you will receive requests that are against the design principle or the requirements you had, so you must have the courage sometimes to say when you won't implement something.
* It's also important that your API is easy to extend.Think of [Jenkins][1]:it's not an API,but it's a very successful open source project, partly because it's so easy to extend and write plugins for it.
* Lastly,you have to choose your audience and make your API appropriate to the audience,whether it's yourself,your team,the company you work for,or the whole world.

[Link][2]

[1]:https://jenkins-ci.org/
[2]:http://realm.io/news/writing-android-libraries/?utm_source=androiddevdigest
