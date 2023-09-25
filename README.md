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

if __name__ == '__main__':
    # You can copy/paste any youtube livestream URL that has a chat here
    #   If it is currently live, it will keep getting messages as they are added
    #   If it isn't live, it will get the chat history
    url = 'https://www.youtube.com/watch?v=a_0IGYe-Clc'

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
`chat_download` is like a book with a bunch of pages. When we say `for chat_item in chat_download:`, its like looking at each page in the book one at a time. The current page we are looking at is `chat_item`. Whatever we put below this section will run once for each "page".








