+++
title= "Building something for someone you love"
date= 2024-10-18
draft= false 
+++

![feels good pepe the frog](/feels-good-man.avif)

I just build a simple program for automating the small business of my mom,
it is just a telegram bot, that updates the following image.

![screenshot of image before running program](/content/images/2024-10-18_17-49.png)

{{ img(src="/content/images/2024-10-18_17-49.png", width=400, height=400, op="fit", caption="feels good pepe the frog") }}

Based on the fee inserted, it generates the example values in the 2 currencies.

This used to take 10-20 minutes each day and now it is done automatically
by the script in less than a second.

<video width="420" height="420" controls>
  <source src="/telegram.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

Being able to see the impact of your code, in someone a loved one, it hits
different than writing the most impressive piece of code, but with
faceless users, that you will never meet.

The code for generating the image:

```python

import xml.etree.ElementTree as ET

svg_file_path = "input.svg"
# Parse the SVG file
tree = ET.parse(svg_file_path)
root = tree.getroot()


def find_element(id):
    xpath_expression = f".//*[@id='{id}']"
    element = root.find(xpath_expression)
    if element is not None:
        return element
    else:
        return None


def generate_img(new_fee: str):
    fee_id = (
        "tspan171"  # this is the ID that was set on the SVG file, inspect the svg file
    )
    fee_element = find_element(fee_id)
    if fee_element is not None:
        fee_element.text = new_fee

    new_fee = float(new_fee)
    results = [
        round(value / new_fee) for value in range(10000, 110000, 10000)
    ]  # From 10.000 to 100.000

    # I set 0..10 the text of the bolivares values in the SVG file
    for i in range(0, 10):
        bs_id = str(i + 1)
        bs_element = find_element(bs_id)
        if bs_element is not None:
            bs_element.text = str(results[i])

    tree.write("output.svg")
```

the code for the telegram bot:

```python
#!/usr/bin/env python3 import os import telebot
from telebot.types import Message
import fee_img
BOT_TOKEN = os.environ.get('BOT_TOKEN')

bot = telebot.TeleBot(BOT_TOKEN)

@bot.message_handler(commands=['start','hola','tasa'])
def send_welcome(message):
    bot.reply_to(message, "escribe la tasa, por ejemplo 106.5")

@bot.message_handler(func=lambda msg: True)
def send_message(message):
    try:
       user_new_fee = message.text
    except ValueError:
       bot.reply_to(message, "tasa no valida, escribe la tasa en numero separando decimales con .")
    fee_img.generate_img(user_new_fee)
    os.system("convert output.svg output.png")
    bot.send_photo(photo=open("output.png","rb"),chat_id = message.chat.id)

bot.infinity_polling()
```

Like this I have written multiple programs for close-friends, or family
members, they solve issues like saving them time or myself(doing these
things for them), for things that are manually tedious, and a simple
program/script would solve the problem. The UI most of the time it's just
a telegram bot, or just an email sent, sometimes they are one-time
scripts.
