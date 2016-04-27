---
layout: post
title: SocketIO Debugging
---
Now you know how to get the different languages to talk to each other. But what about debugging? You have 3 languages and database software that could mess up, and you need to know where. So how do we do it? Simple. Print statements everywhere. Print statements telling you where you are, what your variables are, and what your errors are. But hold up, not every language does print statements the same way. Some dont do them at all. So debugging is a bit trickier than just print statements.

The way I always start is from the beginning of my function "chain". For SocketIO, thats the HTML. Luckily, the only thing that HTML does is formatting. SO if you have a format error, it's the HTML. Otherwise you can move on to the next link in the chain, Angular.

Remember all those `console.log()` 
