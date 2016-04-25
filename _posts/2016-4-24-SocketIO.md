---
layout: post
title: SocketIOOOO!
---

  You think sockets sound easy huh? Well nice try but these aren't your everyday plug in your cellphone and let it charge sockets. 
These are some finnicky and confusing things. Think you know what you're doing just from looking at them once? Kudos to you bud, but 
I'm pretty sure you're wrong.
  Still with me? Cool, because sockets aren't actually that bad. Granted, when I first looked I was making a chatroom. I had to get
Flask, AngularJS, and HTML to all cooperate with each other. When I first saw the code I was given as a base I thought they were
playing telephone with each other and keep away with me. Fun times. 
  The trickiest part about sockets and multiple languages is figuring out who is calling whom, and in what order. For example:
```
$scope.join = function(room){
  $scope.room = room;
  console.log("in join",$scope.room)
  socket.emit('join', room);
};
```
  That just looks like a mess. So `$scope.join` is a function, that seems obvious. It takes a room, whatever that might be, as an
argument. First line looks like it sets some variable as the argument pass. What in the world is console.log()? looks like a print statement.
