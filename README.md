# babibuk-signal
mybabibuk
import os, re, asyncio, aiohttp, feedparser
from aiogram import Bot, Dispatcher, types
from aiogram.filters import Command
aiogram==3.1.1
aiohttp>=3.8
feedparser==6.0.10


BOT_TOKEN = os.getenv("BOT_TOKEN")          # Heroku Config dan o‘qiydi
USER_ID   = 7269565779                      # Sizning telegram ID
bot = Bot(BOT_TOKEN)
dp  = Dispatcher()

@dp.message(Command("start"))
async def start(m: types.Message):
    await m.answer("🇺🇿 Babipips+Bukmap signallari tayyor!")

async def check_signals():
    while True:
        feed = feedparser.parse("https://feeds.babypips.com/signals/rss")
        for e in feed.entries:
            txt = e.title + " " + e.description
            m = re.search(r'(EURUSD|GBPUSD|USDJPY|XAUUSD).*(Buy|Sell).*SL[:]*([\d.]+).*TP[:]*([\d.]+)', txt, re.I)
            if m:
                pair, direc, sl, tp = m.groups()
                msg = f"{pair} ➜ {direc.upper()}\nSL: {sl} | TP: {tp}"
                await bot.send_message(USER_ID, msg)
        await asyncio.sleep(1800)   # 30 daqiqada bir

async def main():
    asyncio.create_task(check_signals())
    await dp.start_polling(bot)

if __name__ == "__main__":
    asyncio.run(main())
worker: python main.py
