---
layout: default
title: News Bot
---

```python
import os
import feedparser
import telebot
from dotenv import load_dotenv

load_dotenv()

BOT_TOKEN = os.getenv('TELEGRAM_BOT_TOKEN')

if not BOT_TOKEN:
    raise ValueError("The TELEGRAM_BOT_TOKEN environment variable is not set.")

bot = telebot.TeleBot(BOT_TOKEN)
RSS_FEEDS = [
    "https://arstechnica.com/feed/",
    "https://www.wired.com/feed/rss",
    "https://gizmodo.com/feed"
]

def fetch_rss_feed(url):
    feed = feedparser.parse(url)
    return feed.entries

def send_news_to_telegram(chat_id, entries):
    for entry in entries:
        title = entry.title
        link = entry.link
        message_text = f"{title}\n\n{link}"
        bot.send_message(chat_id, message_text)

@bot.message_handler(commands=['start', 'news'])
def handle_start(message):
    chat_id = message.chat.id
    for feed_url in RSS_FEEDS:
        entries = fetch_rss_feed(feed_url)
        top_entries = entries[:10]
        send_news_to_telegram(chat_id, top_entries)

if __name__ == "__main__":
    bot.polling()
```

