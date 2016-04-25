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

  The trickiest part about sockets and multiple languages is figuring out who is calling whom, and in what order. For example take a look at this AngularJS function:

```
$scope.join = function(room){
  $scope.room = room;
  console.log("in join",$scope.room)
  socket.emit('join', room);
};
```

  That just looks like a mess. So `$scope.join` is a function, that seems obvious. It takes a room, whatever that might be, as an
argument. First line looks like it sets some variable as the argument pass. What in the world is console.log()? Looks like a print statement, but where does it print to? Okay lets put a tack ing that and come back. On to the real question. What in the world does `socket.emit('join', room);` do? Is it calling itself? But that leads to an infinite loop. If you think that's what it's doing, well you're half right.

  `socket.emit()` does indeed call a function called join, and it does pass it a room variable, but it's not calling an Angular function. It's calling a function defined in your server, which in my case was written in Flask which is a type of Python. Specifically, it was calling this function:
  
```
@socketio.on('join', namespace='/iss')
def join(room):
    session['room'] = room
    print(session['room'])
    join_room(room)
    (Does some fancy stuff with databases)
    emit('populate', messages)
```

  Oh ya, did I mention this also worked with Postgresql? Surprise! 4 things working together. But we can ignore the database code for now. I want to focus on 3 things: `session['room']`, `join_room(room)`, and `emit('populate', messages)`.
  One of those is a variable, the other two are functions, one of which is calling another function. See how fast this is getting tangled up? Breathe. We got this. `session['room']` is a variable for the server so that it can keep track of all the users. Each user has their own `session['room']` with their own values for it. One user to one room. Keep that concept in mind. We'll be using it later. `join_room(room)` is another function. What does it do? It makes keeping groups of sockets tied to the same data a whole lot easier. Flask sockets have the concept of rooms built into them. Say Timmy, Anna, and Joe are all chatting it up in the big chatroom where everyone is. We'll call that room 'General'. They decide they want to leave 'General' and go have their own private conversation in another room, we'll call it 'Private' to keep things simple. What `join_room()` does is take the name of a room as an argument, and connect the socket that called it to that room. There's also a `leave_room()` which does the same thing, but as the name says, disconnects the socket from that room. If both are called with 'General' passed to `leave_room()`
