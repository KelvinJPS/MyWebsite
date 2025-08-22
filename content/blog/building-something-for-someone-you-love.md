+++
title="making scripts for family and friends"
Date=2025-08-22
draft=false
share=true
+++

title= "Simple small scripts for family and friends"
date= 2024-10-21
draft= false 



I just built a simple program for automating my mom's small business.,
It is just a telegram bot that updates the following image, which is
a SVG file.

### Before running the program

![image before running the program](/input.svg)

### after running the program

![image after running the program](/output.jpg)

Based on the fee inserted, it generates the example values in the
2 currencies.

This used to take 10-20 minutes each day, and now it is done automatically
by the script in less than a second.

<video width="420" height="420" controls>
  <source src="/telegram.mp4" type="video/mp4">
</video>

Being able to see the impact of your code, in a loved one, hits
different than writing the most impressive piece of code, but with
faceless users, that you will never meet.

When I'm building these kind of programs, the only thing, I think, is
being able to solve the program of that family-member, friend.

The code just uses xpath expressions to find the id of the element I want to change.
(xpath expressions)[https://en.wikipedia.org/wiki/XPath]

And as a svg it's just text a file, editing it prgonmmatically, it is easy.

The code for generating the image:

`````python

import xml.etree.ElementTree as ET

svg_file_path = "input.svg"
# Parse the SVG file
tree = ET.parse
root = tree.getroot()


def find_element(id):
    xpath_expression = f".//*[@id='']"
    element = root.find(xpath_expression)
    If element is not None:
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

    tree.write("output.svg") ``` The code for the telegram bot:


````python
#!/usr/bin/env python3 import os import telebot from telebot.types import
Message import fee_img BOT_TOKEN = os.environ.get('BOT_TOKEN') bot
= telebot.TeleBot(BOT_TOKEN)
@bot.message_handler(commands=['start','hola','tasa']) def
    send_welcome(message): bot.reply_to(message, "escribe la tasa, por
        ejemplo 106.5")

@bot.message_handler(func=lambda msg: True)
def send_message(message):
try:
user_new_fee = message.text
except ValueError:
bot.reply_to(message, "tasa no valida, escribe la tasa en numero separando decimales con .")
fee_img.generate_img(user_new_fee)
os.system("convert output.svg output.png")
bot.send_photo(photo=open("output.png","rb"),chat_id = message.chat.id)

bot.infinity_polling()```

`````

Then it's hosted in my personal VPS along with other simple programs.

Like this, I have written multiple programs for close friends, or family
members, they solve issues like saving them time or myself(doing these
things for them), for things that are manually tedious, and a simple program would do the job, the "UI" for these kinds of programs that I do
For them, it's just a telegram bot, an email sent, sometimes they are
a one-time program/script, and maybe I might have to build a website
when needed.
