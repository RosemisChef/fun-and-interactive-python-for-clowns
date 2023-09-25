# Fun and Interactive Python for Clowns
A tutorial project that combines some Python basics with Youtube live chat &amp; superchat api interaction.

Intended for [Coni Confetti](https://www.youtube.com/@ConfettiConi), but anyone can use this as a starting point or example for how to work with Youtube (Innertube) livestream chat using the [Chat Downloader](https://github.com/xenova/chat-downloader/tree/master#chat-downloader) package.

I've always wanted to see a visual novel which takes input from Youtube chat, and hearing Coni talk about learning Python for a similar reason inspired me to write down some of the things I have learned.

Disclaimer: I don't actually know Python, but I know enough other programming languages (and how to Google) to hack something together. This example code probably breaks some Python best practices and glosses over some details.

## Background (the boring part)
###  A crash course in APIs
An API is a way of talking to a website with code. API's work kind of like filling out a form and handing it to someone. Imagine filling out a form to order 20 bananas:
- You write down that you want 20 bananas, sign your name, and staple a $50 bill to the center of the form
- You hand the form to the banana dealer
- They read the form, validate your signature/money, and go into the back room
- The dealer brings back 20 bananas, subtracts 20 from their banana inventory sheet, and adds $50 to their revenue sheet

API's are about filling out forms (some simple, some complicated) to give websites information or get information out of them. We are going to use a Youtube API to request information about livestream chat from Youtube. Luckily a lot of the hard parts are already handled for us.

Youtube has 2 API's (that we care about):
1) [The Public API](https://developers.google.com/youtube/v3/live/docs/liveChatMessages) - This is the API you are supposed to use, but it has limits on how many requests you can make and is generally complicated to set up
2) Innertube, the internal API - This is what Youtube uses internally to make the website work. When you go to a livestream (or any page on Youtube), it makes a bunch of API requests to get the data it needs to show you stuff on the page. It's supposed to be internal, but all the pieces are public facing. Someone smart enough could reverse engineer it, and that person is... not me, its this guy: [Xenova - Chat Downloader](https://github.com/xenova/chat-downloader/tree/master#chat-downloader). We can use his code to skip a lot of the complicated set up, authentication, and avoid the limits on how many requests we can make.

## The tools
Pretty straightforward, go download PyCharm: https://www.jetbrains.com/pycharm/download/?section=windows

PyCharm Community Edition is free software that will make it easy to run and edit Python code. Hit download, install it. The defaults should be fine. When you have PyCharm running click New Project, check the checkbox that says "Create a main.py welcome script", name it whatever you want and click Create.

Remember that Innertube API thing? First we need to get the code onto your computer. From within PyCharm, go to the Menu at the top -> View -> Tool Windows -> Python Packages.
In the section that pops up, search for "chat-downloader" and install it (hovering over the result should give an Install option, pick the newest version if it asks).


## Getting chat (the fun part)

New Concepts In This Section:  
[Print](https://www.w3schools.com/python/ref_func_print.asp)  
[Strings](https://www.w3schools.com/python/python_strings.asp)  
[Comments](https://www.w3schools.com/python/python_comments.asp)  
Functions (which we will ignore for now)  

You should be in a file called "main.py" with code that looks like this:
```python
# This is a sample Python script.

# Press Shift+F10 to execute it or replace it with your code.
# Press Double Shift to search everywhere for classes, files, tool windows, actions, and settings.


def print_hi(name):
    # Use a breakpoint in the code line below to debug your script.
    print(f'Hi, {name}')  # Press Ctrl+F8 to toggle the breakpoint.


# Press the green button in the gutter to run the script.
if __name__ == '__main__':
    print_hi('PyCharm')

# See PyCharm help at https://www.jetbrains.com/help/pycharm/
```
To test things out and see if Python is working, click the green Play button next to the line that says:
`if __name__ == '__main__':`
and click "Run 'main'". This should open a console with:
```
Hi, PyCharm

Process finished with exit code 0
```
Now that we know that Python works, delete everything in the file. Yep, just select all and backspace.

### Chat Downloader

New Concepts In This Section:  
[Variables](https://www.w3schools.com/python/python_variables.asp)  
[Indentation](https://www.w3schools.com/python/gloss_python_indentation.asp)  
Optional reading, probably better to ignore for now:  
[Modules](https://www.w3schools.com/python/python_modules.asp)  
[Creating Objects](https://www.w3schools.com/python/python_classes.asp)  

Copy paste this into the blank file:
```python
from chat_downloader import ChatDownloader
import json

if __name__ == '__main__':
    # You can copy/paste any youtube livestream URL that has a chat here
    #   If it is currently live, it will keep getting messages as they are added
    #   If it isn't live, it will get the chat history
    url = 'https://www.youtube.com/watch?v=L-jsPlH44Sg'

    # Create a chat downloader
    downloader = ChatDownloader()
    
    # Give us all the messages from this livestream
    chat_download = downloader.get_chat(url, message_groups=['messages'])
```
This code uses the chat-downloader to get the messages. Don't worry about the specifics, but pay attention to the section that says
```python
message_groups=['messages']
```
This describes what kinds of things from the chat we want to look at. For now we are just going to look at normal messages, but later we will add superchats.

### Looking through chat

New Concepts In This Section:    
[For Loops](https://www.w3schools.com/python/python_for_loops.asp)  

Running this code right now will do nothing. You can click play and it should run, but we aren't doing anything with the chat messages. Add this to the bottom of the code:
```python
    # Look at each chat message as they are added
    for chat_item in chat_download:
      chat_download.print_formatted(chat_item)
```
This is an example of a "for loop". `chat_download` is like a book with a bunch of pages. When we say `for chat_item in chat_download:`, its like looking at each page in the book one at a time. The current page we are looking at is `chat_item`, and all the information from that page is in that `chat_item`. When we flip to the next page (reach the bottom of the for loop), `chat_item` will have all the information from the next page and the code will start from the top of the for loop. Whatever we put below this section will run once for each "page".

The section inside the for loop
```python
    chat_download.print_formatted(chat_item)
```
says to print out some of the information from the chat_item and display it nicely in the console. Because it is in the for loop, it will run once for each chat_item. This function was conveniently created by the person that made the chat-downloader package, we can just assume that it works by magic.

Click the green Play Button and you should see the chat history (or live chat if you use a live stream url) in the console. You can click the red Stop button at any time to stop the code.


### Exploring the chat_item

New Concepts In This Section:  
[Variable Scope](https://www.w3schools.com/python/python_scope.asp)  

Now we can start doing interesting things. Just looking at the chat is nice, but we want to do things based on what we see in the chat.

Let's look at the contents of each message see if it contains ":_carnieCheer:". We will count how many there are, and if enough are sent we will display a message.

First, add this above the for loop:
```
    cheer_count = 0
```
Anywhere between `if __name__ == '__main__':` and `for chat_item in chat_download:` will work. If we put it inside the for loop, it would be reset every time the loop runs through an iteration. This is the variable we will use to keep track of how many times ":_carnieCheer:" was sent. 


**A quick detour to understand what a chat_item looks like inside**

New Concepts In This Section:  
[Accessing Dictionary Items](https://www.w3schools.com/python/python_dictionaries_access.asp)  
[JSON Syntax](https://www.w3schools.com/python/python_json.asp)

We want to get the "message" out of the chat_item so we can see if it has a ":_carnieCheer:" in it. Earlier we thought of chat_item as a page in a book, but what is on the page? One way to find out is to check the documentation for chat-downloader: https://chat-downloader.readthedocs.io/en/latest/items.html

But a more hands on way is to dump all the data from each message. Here we will use the "json" module to make the data easier to read. Add this within the for loop, right below the `chat_download.print_formatted(chat_item)`. As a checkpoint, the whole code should look something like this:
```python
from chat_downloader import ChatDownloader
import json

if __name__ == '__main__':
    # You can copy/paste any youtube livestream URL that has a chat here
    #   If it is currently live, it will keep getting messages as they are added
    #   If it isn't live, it will get the chat history
    url = 'https://www.youtube.com/watch?v=a_0IGYe-Clc'

    # Create a chat downloader
    downloader = ChatDownloader()

    # Give us all the messages from this livestream
    chat_download = downloader.get_chat(url, message_groups=['messages'])

    cheer_count = 0

    # Look at each chat message as they are added
    for chat_item in chat_download:
        chat_download.print_formatted(chat_item)
        print(json.dumps(chat_item, indent=2))
```

Hit the play button and it will run through all the messages again. For each message, this will write a nice formatted chat message on one line, then dump the raw data as a JSON. Take a moment to look through at all the info you can get from a single chat message:

```json
19:39 | (Member (2 months)) Ghost Clown🎉: Cute clowns can do no wrong :_carnieLfg:
{
  "time_in_seconds": 1179.098,
  "action_type": "add_chat_item",
  "message": "Cute clowns can do no wrong :_carnieLfg:",
  "emotes": [
    {
      "id": "UCUfgKgCxtFs1ZPlyiM3JcTw/U7fWZKnlMszUgwOWk5T4Cw",
      "name": ":_carnieLfg:",
      "shortcuts": [
        ":_carnieLfg:",
        ":carnieLfg:",
        ":_lfg:",
        ":lfg:"
      ],
      "search_terms": [
        "_carnieLfg",
        "carnieLfg",
        "_lfg",
        "lfg"
      ],
      "images": [
        {
          "url": "https://yt3.ggpht.com/d57SRnjIXZjTuO5BINkFGRTEBKpRI4j07hi_hoR-Hp_JTGoKYXb-nRLhnDWWv19bkSws-vEQ",
          "id": "source"
        },
        {
          "url": "https://yt3.ggpht.com/d57SRnjIXZjTuO5BINkFGRTEBKpRI4j07hi_hoR-Hp_JTGoKYXb-nRLhnDWWv19bkSws-vEQ=w24-h24-c-k-nd",
          "width": 24,
          "height": 24,
          "id": "24x24"
        },
        {
          "url": "https://yt3.ggpht.com/d57SRnjIXZjTuO5BINkFGRTEBKpRI4j07hi_hoR-Hp_JTGoKYXb-nRLhnDWWv19bkSws-vEQ=w48-h48-c-k-nd",
          "width": 48,
          "height": 48,
          "id": "48x48"
        }
      ],
      "is_custom_emoji": true
    }
  ],
  "message_id": "ChwKGkNPN25vSUNBaFlFREZmdVY1UWNkazVvSF9n",
  "timestamp": 1693419706043916,
  "time_text": "19:39",
  "author": {
    "name": "Ghost Clown\ud83c\udf89",
    "images": [
      {
        "url": "https://yt4.ggpht.com/PAeG_vqbV6RTaBT5stXZlLHZf9EjKBx01Wapaerwhp0Jd4S62IEM646CX3BHBohsEwECffeJuCg",
        "id": "source"
      },
      {
        "url": "https://yt4.ggpht.com/PAeG_vqbV6RTaBT5stXZlLHZf9EjKBx01Wapaerwhp0Jd4S62IEM646CX3BHBohsEwECffeJuCg=s32-c-k-c0x00ffffff-no-rj",
        "width": 32,
        "height": 32,
        "id": "32x32"
      },
      {
        "url": "https://yt4.ggpht.com/PAeG_vqbV6RTaBT5stXZlLHZf9EjKBx01Wapaerwhp0Jd4S62IEM646CX3BHBohsEwECffeJuCg=s64-c-k-c0x00ffffff-no-rj",
        "width": 64,
        "height": 64,
        "id": "64x64"
      }
    ],
    "badges": [
      {
        "title": "Member (2 months)",
        "icons": [
          {
            "url": "https://yt3.ggpht.com/vMmkUEzWHUoVGn8CJvxzUntSwjPTWj08kOzhXFeemeVhQ0BYe6PzTt2Iuw38M1MMOgN0Z4z3Fg",
            "id": "source"
          },
          {
            "url": "https://yt3.ggpht.com/vMmkUEzWHUoVGn8CJvxzUntSwjPTWj08kOzhXFeemeVhQ0BYe6PzTt2Iuw38M1MMOgN0Z4z3Fg=s16-c-k",
            "width": 16,
            "height": 16,
            "id": "16x16"
          },
          {
            "url": "https://yt3.ggpht.com/vMmkUEzWHUoVGn8CJvxzUntSwjPTWj08kOzhXFeemeVhQ0BYe6PzTt2Iuw38M1MMOgN0Z4z3Fg=s32-c-k",
            "width": 32,
            "height": 32,
            "id": "32x32"
          }
        ]
      }
    ],
    "id": "UCd7_suiuRJuNY8EOhMuS6dw"
  },
  "message_type": "text_message"
}
```
Note that this is from a livestream that already ended. A few fields are different between live and VOD messages, but that won't matter for this example.

Out of that JSON, we want the message. "message" is on the top level of the JSON, so we can do `chat_item["message"]` to get it. If we wanted something deeper in the JSON like the author's name, we would have to do `chat_item["author"]["name"]` because the "name" is within the "author" section.

**Tangent over, let's check that message**

New Concepts In This Section:  
[Python Operators (+=, >=)](https://www.w3schools.com/python/python_operators.asp)  
[Python "in" keyword for strings](https://www.w3schools.com/python/ref_keyword_in.asp)  
[If Statements](https://www.w3schools.com/python/python_conditions.asp)
[Multiline Strings](https://www.w3schools.com/python/gloss_python_multi_line_strings.asp)  

First let's delete the line `print(json.dumps(chat_item, indent=2))`. It was nice to look at for a minute, but it's really spamming the console. You can add it back temporarily at any time if you want to check what the `chat_item`s look like.
You can check if one string contains another string with the "in" keyword. Add this within the for loop:
```python
         if ":_carnieCheer:" in chat_item["message"]:
            cheer_count += 1
```
This says:  
"If the 'message' field from this chat_item contains the string ":_carnieCheer:" anywhere in it, add 1 to the variable cheer_count"

One per customer though, you can only increment cheer_count by 1 per chat.

Now let's make something happen when the count reaches 100. The whole for loop should look something like this:
```python
    for chat_item in chat_download:
        chat_download.print_formatted(chat_item)
        if ":_carnieCheer:" in chat_item["message"]:
            cheer_count += 1
        if cheer_count >= 100:
            print('''
            77?7!!!!^:!!!!!!!?Y!!!!!!!!?J~:!!!!!~:..^~!!!!!7!!!77777??????7!!!!!!!!!!!!7J?7!!!!!!~~~!~^~?7777??J
            77?!!!!~.~!!!!!!7?J!!7!!!!!!?J~^~!!!!~^:..:~!!!!!77!!!!!!777777??77!!!!!!!!!7JJ?7!!!!!!!!7!~~777777?
            7?7!!!!::!!!!!!!?~J!!7!!!!!~!?J7^~!!!!!!^:..:^~!!!!!!7!!!!!!!!!!!!7777777!!!!?JJ?7!!!!!!!!77!^!?7777
            7?!!!!~.^!!!!!!!!:J!!77!!!!!~!7J?!~!!!!!!!~^::.:^~!!!!!!!!!!!!77777777777?????JYJ?7!!!!!!!!77!~!?777
            ??!!!!:.^!!!!!!7^:Y!!!7!!!!!!~~!?J?!!!!!!!!!!!~~^::^^~~!!!!!!!7777??JJJJ??77!!!JY??7!!!!!!!!777~!?77
            J7!!!!..~!!!!!!?::Y!!!77!!!!!!~~~!7JJ?77!!!!!!!!77!!!!!!!!77????JJJ?777!!!!!!!!7JY?7!!!!!!!!7777!!?7
            J!!!!~..~!!!!!!7..J7!!!77!!!!!!!~~~!7?JJJJJ??7777777?J?7!!!!!7777!!!^~!!!!!!!!!!?YJ77!!!!!!!77777!!?
            ?!!!!^..~!!!!!7~..77!!!!77!!!!!!!!!~~~!~777???????7777!~^^^~~!!!!!!!~:~!!!!!!!!!?JY?77!!!!!!!!7777~!
            7!!!!^..^!!!!!!^..!7~!!!!!!!~~~~~~~~~^^:^^^^^^^^^^^^^^^~~!!!!!!!!!!!!::!!!!!!!!!7JYJ77!!!!!!!!!7777~
            7~!!!:..^!~~~^~^..:7^~~~^~~!~^^^^^^^^^^:~^^^^^^^^^^^~~!!!!!!!!!!!!!!!~:~7!!!!!!!!?JY?77!!!!!!!!!777!
            ?~~!~:..:!~^^^~^...7^~~~^^^~!~~^^^^:^^^:~^^^^^^^^~~!!!!!!!!!!!!!!!!!!!:^7!!!!!!!!?JY?7?!!!!!!!!!!!77
            J!^!!^...!!~~^~^...~~~!~^^^^~~!!~~~^^^^:^~^^~~~~!!!!!!!!!!!!!!!!!!!!!!~:!7!!!!!!!?JYJ7?7!!!!!!!!!!!!
            Y7^!!~...!7!!~!~...:7~!!~^^^^^~~^~!!!!!~~!!!!!!!!!!!!!!!!!!77!!!!!!!!!!:~7!!!!!!!?Y5J77?!!!!!!!!!!!!
            YJ~^!~...^?!!!!!....~7!!7!!!!!!!::!!!!!7777777!!!!!!!!!!!!!7777777!!777~~?!!!!!!!?YY??!?77!!!!!!!!!!
            JY7:~!:..:?7!!!7^....77!777!7777~.:~~!!!!!777777!!!!!7!!!!!777777??JJ?7~7Y!77!!!7?J7?J!7777!!!!!!!!!
            ?Y?~:!~...7?!!!!!....:J?7??777777:.^~!!7~~77777!!!!!77!!!!!77???????777~?Y!77!!!7J?~?J!!?77!!!!!!!!!
            7YY7^:!:..^?7!!!7~....:J??JJ??77?!..^~!?!^!77??7!!!!777!!!!!7??7777?7777JJ7777777J^~7J!!7777!!!!!!!!
            7J??7^^^..:!?!!!!7:....:??77??777?^..:~7?~~77???7!!!777!!!!!7??7777??77?7?77?777J!.~^7777777!!!!!!!!
            7?7.~7~::..:77!!!7!.....:?J?7??77?J~:.^!?7~~77???7!!!77!!!!!77J?777??777777?777?7:..:7?7!777!!!!!!!!
            ??7..:~~::..^7?7777!......!??????7JY!^:^!?!~!77?7?77!!77!!!!!7YY777??77!?7?J?J5BGGPP5Y?!!777!!!!!!!!
            J?7.....^~^::^777777!:..:::^7??????J?!~^~77~~!77?J?77!!7!!!!!7J577???YP5JYYY5G###&&&&BY??J??7!!!!!!!
            ?J?^....:~~^^~!JJ?????!!~~^:::^~!!~!!~~~~!7!~~777?!??7!!7!!!!!?J?JJYPB5JYPPGB#B5?!!!7Y?7?JYYJJ7!!!!!
            7JY7.....:^!YG#&&BG5YYYPGGGP5Y?!^:......::~!~~!777::!7777!!!!7YPYY5BBP5GBBBBBBBG5J^:^J!~!7YYJ7!!!!!!
            !7YY^.:~75B&&&BGGP5PBGGP55PB##&##GY~:........:^~!7~..:^!777!7?YJ?Y?!?GBBBBBBBBBBB#J.:?~~!!YJ7!!!!!!~
            77?YYYP#@B5J7!~^^^JY?7JGBGGGBBBB#BGPY7^..........::......:~~!!^:Y~  .YBPPGGGGGGGGGG^:?~!!!J?!~~~~~~!
            !!!?5#@&B~:::::::JG:   7BBBBBBBBBBJ^.::....................... ~B5?7J5GP5Y???????JG!~7~!!7?!~~~~~~!!
            !!!!7YGBB7.......PB57!?YPGGP55GGGGGY.......................... ^GJ77!J5PY!~!77777?P^!!!!^!?!~~~~!!!!
            ~~!!!!?YGP^      5GYJJJJ???5PP5?7?YG~...........................JJ!~^^^^::^~!7!!7Y7:7~!~^77~~~~!!!7~
            !!~~~~!!?YY~     7Y?7777!~^!JJ7~~!7Y^...........................:7~^:..::^^~!!^.~7:77!~^~?!~~~~~~~^^
            ~!!~~~~~~~!7~^^^^!YY777!~^:...::^^:^...........  .................:::::^^^^^~^^^^:~?!~^^!7^^^^^^^^^^
            ~~~~~~~~~~~~~!!!~^:7J7!~~^^^^::::::...........   .................:::::::^!!!7^::^?~^^^~7~^::^^^^^^^
            777!!!!~~~!!!~^::^:^^^~~~^^^:::::::................................:::::::7YY7:::7!^^^~77^:::^^^^^^^
            7777??JJ?7^::.:::::^:::~!!!^::::::..................................:::::::?J^::!~^^^~^7^:..:^:^^^^^
            !!!~~~~!!7~^::::^^^~:::~JY?::::::..........................................^^:^~~^^~~:!~:...::::::^~
            ^^^^^^^^^^~~~^^^^:^~::::~J!::::....................................:.......:^~~^^^^:.~^.....::^^~!!!
            ^^^::::::::::::..:~:::::.^^...............................::..  ...^~^:::^^^^^^^:..:~:.....:.:^^^:::
            ::::::::::......:!:................... ..::..:~..^!^:^7!:^?J7??7.....:::^^^:::...:^^......^^::::::::
            ::::::::......::!^....................7J?Y5?7Y5JJYYYJJYYYY5PPPPP7.....:^::.....:::......^~^::::::...
            ^:::::::::::::^7~....:~............. ^5P55555YJ?7!!~~~~~~~!7?JY5J......^~^^:........::~!!^:::::.::.:
            ^~~^:::::::::~77~:::^~^..............:Y555Y?!~~~~~~~~~~~~~~~~~!?~.........:::^^^^^~~!!~^::::::::::^~
            ::^~~~~~~^^~~~^::.:^^.................^YY?!~~~~~~~~~~~~~~~~~~~~^.............:~!!~~^^:::^:::^^~~!777
            !~^::^^::::..:::^??^.................. :~~~~~~~~~~~~~~~~~~~~~^:...................:!????!~!!!!!!!!!~
            ~~!~~~~~^^^^~!7^ :!7~:....................^^~~~~~~~~~~~~~~^:....................:~7J??J?!!!!!!!!!~^:
            :^~~~~~~~!!!!!!!^: .~7~:.....................::^^~~~~~^^:....................:^!?????77!!!!~^^::....
            ::::^~~!!!~~~~~~!!^. .~!!^:.....^:.......................................:^~7??????7~::~~::.........
            ::......:...:::::::::^!JYYJ7!~~!^....................................:^~7???????7!^..^^:............
            :::::::...............:^^~~~~!JJ!~^:.............................:~!J5YYYJ???7!^:...::..............
            ''')
            cheer_count = 0
```
The new section says "If the number of cheers is greater than or equal to 100, print a Coni ASCII art and reset the count to 0". Hit the green Play button and watch it run. If it is a past broadcast it might move too quick to catch the ASCII. You can CTRL + F a chunk of the ASCII within the console to find it. In the included stream url it should show up exactly once.

### Chat draws a picture

New Concepts In This Section:  
[If Statements Using "elif"](https://www.w3schools.com/python/python_conditions.asp)  
[String methods - .lower()](https://www.w3schools.com/python/ref_string_lower.asp)  

We can use a built in library called [turtle](https://realpython.com/beginners-guide-python-turtle) to demonstrate drawing pictures with chat messages. A turtle can move forwards, backwards, rotate left and right, draw a circle, and change colors. Wherever it walks it draws. It has a bunch more settings but let's just stick with something basic for now.

At the top of the file, add the following line to import the turtle library:
```python
import turtle
```
We also need to do a bit of setup to make the turtle and screen work, but it should be pretty self explanatory. Add this section above the for loop:
```python
    # Make a screen so we can watch it draw
    screen = turtle.getscreen()
    # Make a turtle so we can actually draw things
    turtle = turtle.getturtle()
    # Optional: Make the pen size bigger, default is a bit small
    turtle.pensize(3)
    # Optional: Set a starting color, let's pick green
    turtle.color("green")
```

Using the same trick as before, we can check if each chat message contains "left", "right", "forward", "backward", or "circle". If it has one of those, we will make the turtle rotate or move correspondingly. Note that we use `if` and `elif` here to limit each chat message to one command:
```python
        if "left" in chat_item["message"].lower():
            turtle.left(90)
        elif "right" in chat_item["message"].lower():
            turtle.right(90)
        elif "forward" in chat_item["message"].lower():
            turtle.forward(100)
        elif "backward" in chat_item["message"].lower():
            turtle.backward(100)
        elif "circle" in chat_item["message"].lower():
            turtle.circle(50)
```
And running this on the VOD, it draws... well what are the odds. I swear I didn't plan this.

### Dynamic inputs from chat

New Concepts In This Section:  
[Try/Except](https://www.w3schools.com/python/python_try_except.asp)  
[String .index() function](https://www.w3schools.com/python/ref_string_index.asp)  
[String Slicing](https://www.w3schools.com/python/python_strings_slicing.asp)
[String .strip() function](https://www.w3schools.com/python/ref_string_strip.asp)

Next let's give chat the power to change the color of the line. If the turtle gets an invalid color it crashes the whole program, so we have to be careful. Crashed program = no more picture. Apparently Python has a [bafflingly long and specific list of named colors](https://www.discogcodingacademy.com/turtle-colours). Instead of adding them all manually, we can let chat try a color and recover if it fails. As another checkpoint, here is the whole code with a new section for color at the bottom: 
```python
from chat_downloader import ChatDownloader
import json
import turtle

if __name__ == '__main__':
    # You can copy/paste any youtube livestream URL that has a chat here
    #   If it is currently live, it will keep getting messages as they are added
    #   If it isn't live, it will get the chat history
    url = 'https://www.youtube.com/watch?v=L-jsPlH44Sg'

    # Create a chat downloader
    downloader = ChatDownloader()

    # Give us all the messages from this livestream
    chat_download = downloader.get_chat(url, message_groups=['messages'])

    cheer_count = 0

    screen = turtle.getscreen()
    turtle = turtle.getturtle()
    turtle.pensize(3)
    turtle.color("green")

    # Look at each chat message as they are added
    for chat_item in chat_download:
        chat_download.print_formatted(chat_item)
        if ":_carnieCheer:" in chat_item["message"]:
            cheer_count += 1
        if cheer_count >= 100:
            print('''
            77?7!!!!^:!!!!!!!?Y!!!!!!!!?J~:!!!!!~:..^~!!!!!7!!!77777??????7!!!!!!!!!!!!7J?7!!!!!!~~~!~^~?7777??J
            77?!!!!~.~!!!!!!7?J!!7!!!!!!?J~^~!!!!~^:..:~!!!!!77!!!!!!777777??77!!!!!!!!!7JJ?7!!!!!!!!7!~~777777?
            7?7!!!!::!!!!!!!?~J!!7!!!!!~!?J7^~!!!!!!^:..:^~!!!!!!7!!!!!!!!!!!!7777777!!!!?JJ?7!!!!!!!!77!^!?7777
            7?!!!!~.^!!!!!!!!:J!!77!!!!!~!7J?!~!!!!!!!~^::.:^~!!!!!!!!!!!!77777777777?????JYJ?7!!!!!!!!77!~!?777
            ??!!!!:.^!!!!!!7^:Y!!!7!!!!!!~~!?J?!!!!!!!!!!!~~^::^^~~!!!!!!!7777??JJJJ??77!!!JY??7!!!!!!!!777~!?77
            J7!!!!..~!!!!!!?::Y!!!77!!!!!!~~~!7JJ?77!!!!!!!!77!!!!!!!!77????JJJ?777!!!!!!!!7JY?7!!!!!!!!7777!!?7
            J!!!!~..~!!!!!!7..J7!!!77!!!!!!!~~~!7?JJJJJ??7777777?J?7!!!!!7777!!!^~!!!!!!!!!!?YJ77!!!!!!!77777!!?
            ?!!!!^..~!!!!!7~..77!!!!77!!!!!!!!!~~~!~777???????7777!~^^^~~!!!!!!!~:~!!!!!!!!!?JY?77!!!!!!!!7777~!
            7!!!!^..^!!!!!!^..!7~!!!!!!!~~~~~~~~~^^:^^^^^^^^^^^^^^^~~!!!!!!!!!!!!::!!!!!!!!!7JYJ77!!!!!!!!!7777~
            7~!!!:..^!~~~^~^..:7^~~~^~~!~^^^^^^^^^^:~^^^^^^^^^^^~~!!!!!!!!!!!!!!!~:~7!!!!!!!!?JY?77!!!!!!!!!777!
            ?~~!~:..:!~^^^~^...7^~~~^^^~!~~^^^^:^^^:~^^^^^^^^~~!!!!!!!!!!!!!!!!!!!:^7!!!!!!!!?JY?7?!!!!!!!!!!!77
            J!^!!^...!!~~^~^...~~~!~^^^^~~!!~~~^^^^:^~^^~~~~!!!!!!!!!!!!!!!!!!!!!!~:!7!!!!!!!?JYJ7?7!!!!!!!!!!!!
            Y7^!!~...!7!!~!~...:7~!!~^^^^^~~^~!!!!!~~!!!!!!!!!!!!!!!!!!77!!!!!!!!!!:~7!!!!!!!?Y5J77?!!!!!!!!!!!!
            YJ~^!~...^?!!!!!....~7!!7!!!!!!!::!!!!!7777777!!!!!!!!!!!!!7777777!!777~~?!!!!!!!?YY??!?77!!!!!!!!!!
            JY7:~!:..:?7!!!7^....77!777!7777~.:~~!!!!!777777!!!!!7!!!!!777777??JJ?7~7Y!77!!!7?J7?J!7777!!!!!!!!!
            ?Y?~:!~...7?!!!!!....:J?7??777777:.^~!!7~~77777!!!!!77!!!!!77???????777~?Y!77!!!7J?~?J!!?77!!!!!!!!!
            7YY7^:!:..^?7!!!7~....:J??JJ??77?!..^~!?!^!77??7!!!!777!!!!!7??7777?7777JJ7777777J^~7J!!7777!!!!!!!!
            7J??7^^^..:!?!!!!7:....:??77??777?^..:~7?~~77???7!!!777!!!!!7??7777??77?7?77?777J!.~^7777777!!!!!!!!
            7?7.~7~::..:77!!!7!.....:?J?7??77?J~:.^!?7~~77???7!!!77!!!!!77J?777??777777?777?7:..:7?7!777!!!!!!!!
            ??7..:~~::..^7?7777!......!??????7JY!^:^!?!~!77?7?77!!77!!!!!7YY777??77!?7?J?J5BGGPP5Y?!!777!!!!!!!!
            J?7.....^~^::^777777!:..:::^7??????J?!~^~77~~!77?J?77!!7!!!!!7J577???YP5JYYY5G###&&&&BY??J??7!!!!!!!
            ?J?^....:~~^^~!JJ?????!!~~^:::^~!!~!!~~~~!7!~~777?!??7!!7!!!!!?J?JJYPB5JYPPGB#B5?!!!7Y?7?JYYJJ7!!!!!
            7JY7.....:^!YG#&&BG5YYYPGGGP5Y?!^:......::~!~~!777::!7777!!!!7YPYY5BBP5GBBBBBBBG5J^:^J!~!7YYJ7!!!!!!
            !7YY^.:~75B&&&BGGP5PBGGP55PB##&##GY~:........:^~!7~..:^!777!7?YJ?Y?!?GBBBBBBBBBBB#J.:?~~!!YJ7!!!!!!~
            77?YYYP#@B5J7!~^^^JY?7JGBGGGBBBB#BGPY7^..........::......:~~!!^:Y~  .YBPPGGGGGGGGGG^:?~!!!J?!~~~~~~!
            !!!?5#@&B~:::::::JG:   7BBBBBBBBBBJ^.::....................... ~B5?7J5GP5Y???????JG!~7~!!7?!~~~~~~!!
            !!!!7YGBB7.......PB57!?YPGGP55GGGGGY.......................... ^GJ77!J5PY!~!77777?P^!!!!^!?!~~~~!!!!
            ~~!!!!?YGP^      5GYJJJJ???5PP5?7?YG~...........................JJ!~^^^^::^~!7!!7Y7:7~!~^77~~~~!!!7~
            !!~~~~!!?YY~     7Y?7777!~^!JJ7~~!7Y^...........................:7~^:..::^^~!!^.~7:77!~^~?!~~~~~~~^^
            ~!!~~~~~~~!7~^^^^!YY777!~^:...::^^:^...........  .................:::::^^^^^~^^^^:~?!~^^!7^^^^^^^^^^
            ~~~~~~~~~~~~~!!!~^:7J7!~~^^^^::::::...........   .................:::::::^!!!7^::^?~^^^~7~^::^^^^^^^
            777!!!!~~~!!!~^::^:^^^~~~^^^:::::::................................:::::::7YY7:::7!^^^~77^:::^^^^^^^
            7777??JJ?7^::.:::::^:::~!!!^::::::..................................:::::::?J^::!~^^^~^7^:..:^:^^^^^
            !!!~~~~!!7~^::::^^^~:::~JY?::::::..........................................^^:^~~^^~~:!~:...::::::^~
            ^^^^^^^^^^~~~^^^^:^~::::~J!::::....................................:.......:^~~^^^^:.~^.....::^^~!!!
            ^^^::::::::::::..:~:::::.^^...............................::..  ...^~^:::^^^^^^^:..:~:.....:.:^^^:::
            ::::::::::......:!:................... ..::..:~..^!^:^7!:^?J7??7.....:::^^^:::...:^^......^^::::::::
            ::::::::......::!^....................7J?Y5?7Y5JJYYYJJYYYY5PPPPP7.....:^::.....:::......^~^::::::...
            ^:::::::::::::^7~....:~............. ^5P55555YJ?7!!~~~~~~~!7?JY5J......^~^^:........::~!!^:::::.::.:
            ^~~^:::::::::~77~:::^~^..............:Y555Y?!~~~~~~~~~~~~~~~~~!?~.........:::^^^^^~~!!~^::::::::::^~
            ::^~~~~~~^^~~~^::.:^^.................^YY?!~~~~~~~~~~~~~~~~~~~~^.............:~!!~~^^:::^:::^^~~!777
            !~^::^^::::..:::^??^.................. :~~~~~~~~~~~~~~~~~~~~~^:...................:!????!~!!!!!!!!!~
            ~~!~~~~~^^^^~!7^ :!7~:....................^^~~~~~~~~~~~~~~^:....................:~7J??J?!!!!!!!!!~^:
            :^~~~~~~~!!!!!!!^: .~7~:.....................::^^~~~~~^^:....................:^!?????77!!!!~^^::....
            ::::^~~!!!~~~~~~!!^. .~!!^:.....^:.......................................:^~7??????7~::~~::.........
            ::......:...:::::::::^!JYYJ7!~~!^....................................:^~7???????7!^..^^:............
            :::::::...............:^^~~~~!JJ!~^:.............................:~!J5YYYJ???7!^:...::..............
            ''')
            cheer_count = 0

        if "left" in chat_item["message"].lower():
            turtle.left(90)
        elif "right" in chat_item["message"].lower():
            turtle.right(90)
        elif "forward" in chat_item["message"].lower():
            turtle.forward(100)
        elif "backward" in chat_item["message"].lower():
            turtle.backward(100)
        elif "circle" in chat_item["message"].lower():
            turtle.circle(50)

        if "color:" in chat_item["message"].lower():
            try:
                index = chat_item["message"].index("color:") + 6
                color = chat_item["message"][index:].strip()
                print(color)
                turtle.pencolor(color)
            except:
                print("hey thats not a color")

```
The new section uses a "try/catch" (or in Python I guess it's a "try/except"):
```python
        if "color:" in chat_item["message"].lower():
            try:
                index = chat_item["message"].index("color:") + 6
                color = chat_item["message"][index:].strip()
                print(color)
                turtle.pencolor(color)
            except:
                print("hey thats not a color")

```
This code says "If a chat message has `color:` in it, pull out everything after `color:`, remove all the spaces at the beginning and end and assume it's a color. If anything goes wrong, give up and print 'hey thats not a color' instead of crashing".

Let's break down these two lines:
```python
                index = chat_item["message"].index("color:") + 6
                color = chat_item["message"][index:].strip()
```
`index = chat_item["message"].index("color:") + 6` -> Find the spot in the string where "color:" starts. Then add 6 because we want to know where "color:" ends. The number 6 is used because we know the string "color:" is 6 characters long.

`color = chat_item["message"][index:].strip()` -> Since we remembered where the string "color:" ends in the last line as the variable `index`, we can do `chat_item["message"][index:]` to get everything from that point to the end of the string. Then `.strip()` removes all the spaces at the beginning and end.

The logic here is that someone might type in `deaughghghh color: red    `. If we don't remove all those spaces and extra characters, people in the chat are going to complain that it doesn't work even though thEY AREN'T READING THE INSTRUCTIONS.

### More custom values from the chat

New Concepts In This Section:  
[Creating and Using Functions](https://www.w3schools.com/python/python_functions.asp)  
[Floating Point Numbers (floats)](https://www.w3schools.com/python/gloss_python_float.asp)
[String to Float using float()](https://www.w3schools.com/python/ref_func_float.asp)  

Ok now chat can draw and change colors, but they can only rotate the turtle 90 degrees and draw lines/circles of the same length. This will always draw a boring grid with circles on it. The code we just wrote would work perfectly for making the other sections adjustable, but it would be annoying to copy/paste/adjust it 5 times.

Let's make a reusable function that can work for all these cases. Add this at the top of the file, it just needs to be above `if __name__ == '__main__':` because Python needs functions to be created on a line above where they are used:
```python
# target is the string we are looking for
# message is the string we are searching through
def substring_after(target: str, message: str):
    # Find the start of target in the message
    # Add the length of the target string, this is the same as when we did +6 earlier, but it works for any case 
    index = message.index(target) + len(target)
    # Get the section of the string from the end of the target to the end of the message, then remove extra spaces on either end
    substring = message[index:].strip()
    # Return that substring to whoever called this function
    return substring
```
Now we can refactor the color section to:
```python
        if "color:" in chat_item["message"].lower():
            try:
                color = substring_after("color:", chat_item["message"])
                print(color)
                turtle.pencolor(color)
            except:
                print("hey thats not a color")
```
And we can refactor the whole "chat controls the turtle" section into something like:
```python
        message = chat_item["message"]

        try:
            if "left:" in message.lower():
                amount = substring_after("left:", message)
                turtle.left(float(amount))
            elif "right:" in message.lower():
                amount = substring_after("right:", message)
                turtle.right(float(amount))
            elif "forward:" in message.lower():
                amount = substring_after("forward:", message)
                turtle.forward(float(amount))
            elif "backward:" in message.lower():
                amount = substring_after("backward:", message)
                turtle.backward(float(amount))
            elif "circle:" in message.lower():
                amount = substring_after("circle:", message)
                turtle.circle(float(amount))
            elif "color:" in message.lower():
                color = substring_after("color:", message)
                turtle.pencolor(color)
        except:
            print("invalid input")
```
Here I changed all the `chat_item["message"]`s into a single variable called `message`. If you see your code spammed with the same thing over and over, it's generally a sign that a variable or function would make your life easier (or at least make it easier to read).  
Another difference is that all the sections besides `color:` use `float(amount)`. This is because the turtle functions want a number, not a string. Specifically they want a float, and the `float()` function can convert a string like `"123"` to a floating point number `123.0`.

With this change, chat can type things like "left:5", "forward: 25", "circle:10", or "color: magenta" and it will try to process them. If any of them fail it will print "invalid input", and only one command is allowed per chat message.

Here is the complete code up to this point:
```python
from chat_downloader import ChatDownloader
import json
import turtle

def substring_after(target: str, message: str):
    index = message.index(target) + len(target)
    substring = message[index:].strip()
    return substring

if __name__ == '__main__':
    # You can copy/paste any youtube livestream URL that has a chat here
    #   If it is currently live, it will keep getting messages as they are added
    #   If it isn't live, it will get the chat history
    url = 'https://www.youtube.com/watch?v=L-jsPlH44Sg'

    # Create a chat downloader
    downloader = ChatDownloader()

    # Give us all the messages from this livestream
    chat_download = downloader.get_chat(url, message_groups=['messages'])

    cheer_count = 0

    screen = turtle.getscreen()
    turtle = turtle.getturtle()
    turtle.pensize(3)
    turtle.color("green")

    # Look at each chat message as they are added
    for chat_item in chat_download:
        chat_download.print_formatted(chat_item)
        if ":_carnieCheer:" in chat_item["message"]:
            cheer_count += 1
        if cheer_count >= 100:
            print('''
            77?7!!!!^:!!!!!!!?Y!!!!!!!!?J~:!!!!!~:..^~!!!!!7!!!77777??????7!!!!!!!!!!!!7J?7!!!!!!~~~!~^~?7777??J
            77?!!!!~.~!!!!!!7?J!!7!!!!!!?J~^~!!!!~^:..:~!!!!!77!!!!!!777777??77!!!!!!!!!7JJ?7!!!!!!!!7!~~777777?
            7?7!!!!::!!!!!!!?~J!!7!!!!!~!?J7^~!!!!!!^:..:^~!!!!!!7!!!!!!!!!!!!7777777!!!!?JJ?7!!!!!!!!77!^!?7777
            7?!!!!~.^!!!!!!!!:J!!77!!!!!~!7J?!~!!!!!!!~^::.:^~!!!!!!!!!!!!77777777777?????JYJ?7!!!!!!!!77!~!?777
            ??!!!!:.^!!!!!!7^:Y!!!7!!!!!!~~!?J?!!!!!!!!!!!~~^::^^~~!!!!!!!7777??JJJJ??77!!!JY??7!!!!!!!!777~!?77
            J7!!!!..~!!!!!!?::Y!!!77!!!!!!~~~!7JJ?77!!!!!!!!77!!!!!!!!77????JJJ?777!!!!!!!!7JY?7!!!!!!!!7777!!?7
            J!!!!~..~!!!!!!7..J7!!!77!!!!!!!~~~!7?JJJJJ??7777777?J?7!!!!!7777!!!^~!!!!!!!!!!?YJ77!!!!!!!77777!!?
            ?!!!!^..~!!!!!7~..77!!!!77!!!!!!!!!~~~!~777???????7777!~^^^~~!!!!!!!~:~!!!!!!!!!?JY?77!!!!!!!!7777~!
            7!!!!^..^!!!!!!^..!7~!!!!!!!~~~~~~~~~^^:^^^^^^^^^^^^^^^~~!!!!!!!!!!!!::!!!!!!!!!7JYJ77!!!!!!!!!7777~
            7~!!!:..^!~~~^~^..:7^~~~^~~!~^^^^^^^^^^:~^^^^^^^^^^^~~!!!!!!!!!!!!!!!~:~7!!!!!!!!?JY?77!!!!!!!!!777!
            ?~~!~:..:!~^^^~^...7^~~~^^^~!~~^^^^:^^^:~^^^^^^^^~~!!!!!!!!!!!!!!!!!!!:^7!!!!!!!!?JY?7?!!!!!!!!!!!77
            J!^!!^...!!~~^~^...~~~!~^^^^~~!!~~~^^^^:^~^^~~~~!!!!!!!!!!!!!!!!!!!!!!~:!7!!!!!!!?JYJ7?7!!!!!!!!!!!!
            Y7^!!~...!7!!~!~...:7~!!~^^^^^~~^~!!!!!~~!!!!!!!!!!!!!!!!!!77!!!!!!!!!!:~7!!!!!!!?Y5J77?!!!!!!!!!!!!
            YJ~^!~...^?!!!!!....~7!!7!!!!!!!::!!!!!7777777!!!!!!!!!!!!!7777777!!777~~?!!!!!!!?YY??!?77!!!!!!!!!!
            JY7:~!:..:?7!!!7^....77!777!7777~.:~~!!!!!777777!!!!!7!!!!!777777??JJ?7~7Y!77!!!7?J7?J!7777!!!!!!!!!
            ?Y?~:!~...7?!!!!!....:J?7??777777:.^~!!7~~77777!!!!!77!!!!!77???????777~?Y!77!!!7J?~?J!!?77!!!!!!!!!
            7YY7^:!:..^?7!!!7~....:J??JJ??77?!..^~!?!^!77??7!!!!777!!!!!7??7777?7777JJ7777777J^~7J!!7777!!!!!!!!
            7J??7^^^..:!?!!!!7:....:??77??777?^..:~7?~~77???7!!!777!!!!!7??7777??77?7?77?777J!.~^7777777!!!!!!!!
            7?7.~7~::..:77!!!7!.....:?J?7??77?J~:.^!?7~~77???7!!!77!!!!!77J?777??777777?777?7:..:7?7!777!!!!!!!!
            ??7..:~~::..^7?7777!......!??????7JY!^:^!?!~!77?7?77!!77!!!!!7YY777??77!?7?J?J5BGGPP5Y?!!777!!!!!!!!
            J?7.....^~^::^777777!:..:::^7??????J?!~^~77~~!77?J?77!!7!!!!!7J577???YP5JYYY5G###&&&&BY??J??7!!!!!!!
            ?J?^....:~~^^~!JJ?????!!~~^:::^~!!~!!~~~~!7!~~777?!??7!!7!!!!!?J?JJYPB5JYPPGB#B5?!!!7Y?7?JYYJJ7!!!!!
            7JY7.....:^!YG#&&BG5YYYPGGGP5Y?!^:......::~!~~!777::!7777!!!!7YPYY5BBP5GBBBBBBBG5J^:^J!~!7YYJ7!!!!!!
            !7YY^.:~75B&&&BGGP5PBGGP55PB##&##GY~:........:^~!7~..:^!777!7?YJ?Y?!?GBBBBBBBBBBB#J.:?~~!!YJ7!!!!!!~
            77?YYYP#@B5J7!~^^^JY?7JGBGGGBBBB#BGPY7^..........::......:~~!!^:Y~  .YBPPGGGGGGGGGG^:?~!!!J?!~~~~~~!
            !!!?5#@&B~:::::::JG:   7BBBBBBBBBBJ^.::....................... ~B5?7J5GP5Y???????JG!~7~!!7?!~~~~~~!!
            !!!!7YGBB7.......PB57!?YPGGP55GGGGGY.......................... ^GJ77!J5PY!~!77777?P^!!!!^!?!~~~~!!!!
            ~~!!!!?YGP^      5GYJJJJ???5PP5?7?YG~...........................JJ!~^^^^::^~!7!!7Y7:7~!~^77~~~~!!!7~
            !!~~~~!!?YY~     7Y?7777!~^!JJ7~~!7Y^...........................:7~^:..::^^~!!^.~7:77!~^~?!~~~~~~~^^
            ~!!~~~~~~~!7~^^^^!YY777!~^:...::^^:^...........  .................:::::^^^^^~^^^^:~?!~^^!7^^^^^^^^^^
            ~~~~~~~~~~~~~!!!~^:7J7!~~^^^^::::::...........   .................:::::::^!!!7^::^?~^^^~7~^::^^^^^^^
            777!!!!~~~!!!~^::^:^^^~~~^^^:::::::................................:::::::7YY7:::7!^^^~77^:::^^^^^^^
            7777??JJ?7^::.:::::^:::~!!!^::::::..................................:::::::?J^::!~^^^~^7^:..:^:^^^^^
            !!!~~~~!!7~^::::^^^~:::~JY?::::::..........................................^^:^~~^^~~:!~:...::::::^~
            ^^^^^^^^^^~~~^^^^:^~::::~J!::::....................................:.......:^~~^^^^:.~^.....::^^~!!!
            ^^^::::::::::::..:~:::::.^^...............................::..  ...^~^:::^^^^^^^:..:~:.....:.:^^^:::
            ::::::::::......:!:................... ..::..:~..^!^:^7!:^?J7??7.....:::^^^:::...:^^......^^::::::::
            ::::::::......::!^....................7J?Y5?7Y5JJYYYJJYYYY5PPPPP7.....:^::.....:::......^~^::::::...
            ^:::::::::::::^7~....:~............. ^5P55555YJ?7!!~~~~~~~!7?JY5J......^~^^:........::~!!^:::::.::.:
            ^~~^:::::::::~77~:::^~^..............:Y555Y?!~~~~~~~~~~~~~~~~~!?~.........:::^^^^^~~!!~^::::::::::^~
            ::^~~~~~~^^~~~^::.:^^.................^YY?!~~~~~~~~~~~~~~~~~~~~^.............:~!!~~^^:::^:::^^~~!777
            !~^::^^::::..:::^??^.................. :~~~~~~~~~~~~~~~~~~~~~^:...................:!????!~!!!!!!!!!~
            ~~!~~~~~^^^^~!7^ :!7~:....................^^~~~~~~~~~~~~~~^:....................:~7J??J?!!!!!!!!!~^:
            :^~~~~~~~!!!!!!!^: .~7~:.....................::^^~~~~~^^:....................:^!?????77!!!!~^^::....
            ::::^~~!!!~~~~~~!!^. .~!!^:.....^:.......................................:^~7??????7~::~~::.........
            ::......:...:::::::::^!JYYJ7!~~!^....................................:^~7???????7!^..^^:............
            :::::::...............:^^~~~~!JJ!~^:.............................:~!J5YYYJ???7!^:...::..............
            ''')
            cheer_count = 0

        message = chat_item["message"]

        try:
            if "left:" in message.lower():
                amount = substring_after("left:", message)
                turtle.left(float(amount))
            elif "right:" in message.lower():
                amount = substring_after("right:", message)
                turtle.right(float(amount))
            elif "forward:" in message.lower():
                amount = substring_after("forward:", message)
                turtle.forward(float(amount))
            elif "backward:" in message.lower():
                amount = substring_after("backward:", message)
                turtle.backward(float(amount))
            elif "circle:" in message.lower():
                amount = substring_after("circle:", message)
                turtle.circle(float(amount))
            elif "color:" in message.lower():
                color = substring_after("color:", message)
                turtle.pencolor(color)
        except:
            print("invalid input")

```

