---
layout: post
title: SocketIO Debugging
---
Now you know how to get the different languages to talk to each other. But what about debugging? You have 3 languages and database software that could mess up, and you need to know where. So how do we do it? Simple. Print statements everywhere. Print statements telling you where you are, what your variables are, and what your errors are. But hold up, not every language does print statements the same way. Some dont do them at all. So debugging is a bit trickier than just print statements.

The way I always start is from the beginning of my function "chain". For SocketIO, thats the HTML. Luckily, the only thing that HTML does is formatting. SO if you have a format error, it's the HTML. Otherwise you can move on to the next link in the chain, Angular.

Remember all those `console.log()` calls I told you to ignore in part 1? That's Angular's version of a print statement. But where does it print to? The answer is.... your web browser. Huh? Where? I don't know about you, but I 99.99999% of the time don't see random debug statements on my browser when I'm surfing the web. That's because it prints it to a special section of your browser, the console. Hence the `console` in `console.log()`. Where's the console? For chrome, you have to click on the options menu underneath the "X", go to "More tools", and click on "Developer tools" in the side menu. Or hit Ctrl-Shift-I. I run windows, so the key command might be something different for Macs. After that, a little terminal looking thing will appear on the right hand side. That's where `console.log()`, as well as any errors your Angular sends out, prints to.

Now you have a choice: go to the next link and check your Flask code, or jump to when you're back in your Angular since it's already open. Since I'm going through the chain I'll talk about Flask next, but you're welcome to skip ahead to when I talk about going back to Angular. Be warned though, the problem could well be in your flask code.

Now let's talk about Flask. This was the easiest to debug for me because I was using cloud 9 to run everything so my print statements were in the bash terminal under my code. Debugging Flask is a bit more complicated however, and because it works so closely with Postgresql I'm going to wrap all that together. 

First thing you need to do is make put a print statement in with something as simple as "In function 'foo'" with foo being whatever function you're trying to fix. Oh, and to print something in flask it's just `print()`. If you run your program and you don't see a "In function 'foo'" pop up, you're not even there. 9 times out of 10, you misspelled the function name somewhere. If not that, then you have some weird conditional that's always set to the opposite of whatever you need it to be for your function to be called.

Next, make sure any variables you passed to the function were passed correctly. Again, simple `print(variable)` statements will work. Assuming you're passing the variables correctly, this is where it gets tricky. I'm going to talk about working with Postgres now, because most of my functions if not all used it and if theres any weird logic you did that's on you to figure out. Just throw print statements at it.

Now for what I found to be the most annoying part, getting Postgres to play nice. You're going to have to connect to the database with a user and login, make a cursor, and use that cursor for all your interactions with the database. I always start any server file that messes with databases with 

```
def connectToDB():
    connectionString = 'dbname=####### user=###### password=###### host = localhost'
    print(connectionString)
    try:
        return psycopg2.connect(connectionString)
    except:
        print("Can't connect to database")
```

This returns an object with a connection to the database. Note: you have to import psycopg2 at the top of your server file for it to work. Then at the beginning of any function that uses the database, I do

```
  conn = connectToDB()
  cur = conn.cursor()
```

Now I have a cursor object that I can use to interact with my database. To actually use it with a database, I need to put it in a try-catch block, like so:

```
try:
  cur.execute("""whatever you're attempting""")
except:
  print("Error doing whatever")
```
This way, if the database rejects whatever you want to do, you have a print statement ready to go. If you're using variables:

```
cur.execute("""whatever you're attempting with %s""", (variable,))
```

Using `%s` lets you substitute a variable in so your statement can be dynamic. Notice the `,` after `variable`. That's because `cur.execute()` uses tuples for variable substitution. So even if there is only one variable, it has to be in tuple form.

If you're looking up something from the database, you can assign the results to an array of lists with `variable = cur.fetchall()`, which will let you index a whole list with `variable[index of list]` and an item of that list with `variable[index of list][index of item]`. If your code breaks anywhere in here then one of the following happened: with cloud 9 postgres might not be running, the user you used to connect might not have permissions to do what you want, or you messed up the syntax for the statement you were trying to execute. For the first one, enter 'sudo service postgres start' in your command line. To fix the last two, try executing your statement in Postgres directly and see what happens. It should give you a clue as to what happened.

Once that works, you can do whatever you want with the results and pass them to whoever. Debugging Angular is just print statements with locations and variables as it doesn't ever touch the database directly.
