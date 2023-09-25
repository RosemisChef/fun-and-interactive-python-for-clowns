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

API's are about filling out forms (some simple, some complicated) to give websites information or get information out of them

Youtube has 2 API's (that we care about):
1) [The Public API](https://developers.google.com/youtube/v3/live/docs/liveChatMessages) - This is the API you are supposed to use, but it has limits on how many requests you can make and is generally complicated to set up
2) Innertube, the internal API - This is what Youtube uses internally to make the website work. When you go to a livestream (or any page on Youtube), it makes a bunch of API requests to get the data it needs to show you stuff on the page. It's supposed to be internal, but all the pieces are public facing. Someone smart enough could reverse engineer it, and that person is... not me, its this guy: [Xenova - Chat Downloader](https://github.com/xenova/chat-downloader/tree/master#chat-downloader). We can use his code to skip a lot of the complicated set up, authentication, and avoid the limits on how many requests we can make.

## The tools
Pretty straightforward, go download PyCharm: https://www.jetbrains.com/pycharm/download/?section=windows

PyCharm Community Edition is free software that will make it easy to run and edit Python code. Hit download, install it. The defaults should be fine. When you have PyCharm running click New Project, check the checkbox that says "Create a main.py welcome script", name it whatever you want and click Create.

Remember that Innertube API thing? First we need to get the code onto your computer. From within PyCharm, go to the Menu at the top -> View -> Tool Windows -> Python Packages.
In the section that pops up, search for "chat-downloader" and install it (hovering over the result should give an Install option, pick the newest version).


## Getting chat (the fun part)

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

Now we can start doing interesting things. Just looking at the chat is nice, but we want to do things based on what we see in the chat.

Let's look at the contents of each message see if it contains ":_carnieCheer:". We will count how many there are, and if enough are sent we will display a message.

First, add this above the for loop:
```
    cheer_count = 0
```
Anywhere between `if __name__ == '__main__':` and `for chat_item in chat_download:` will work. This is the variable we will use to keep track of how many times ":_carnieCheer:" was sent. 


**A quick detour to understand what a chat_item looks like inside**

We want to get the "message" out of the chat_item so we can see if it has a ":_carnieCheer:" in it. Earlier we thought of chat_item as a page in a book, but what is on the page? One way to find out is to check the documentation: https://chat-downloader.readthedocs.io/en/latest/items.html

But a more hands on way is to dump all the data from each message. Add this within the for loop, right below the `chat_download.print_formatted(chat_item)`. As a checkpoint, the whole code should look something like this:
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

First let's delete the `print(json.dumps(chat_item, indent=2))`, that's just too much spam. You can add it back temporarily at any time if you want to check what the `chat_item`s look like.
You can check if one string contains another string with the "in" keyword. Add this within the for loop:
```python
         if ":_carnieCheer:" in chat_item["message"]:
            cheer_count += 1
```
This says:

"If the 'message' field from this chat_item contains the string ":_carnieCheer:" anywhere in it, add 1 to the variable cheer_count"

One per customer though, you can only increment it by 1 per chat.

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
The new section says "If the number of cheers is greater than or equal to 100, print a Coni ASCII art and reset the count to 0"



