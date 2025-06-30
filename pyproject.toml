import os
import logging
from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, MessageHandler, filters, ContextTypes
from googleapiclient.discovery import build

# Логирование
logging.basicConfig(
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    level=logging.INFO
)

# Получаем ключи из переменных окружения
TOKEN = os.getenv("TOKEN")
YOUTUBE_API_KEY = os.getenv("YOUTUBE_API_KEY")

# Инициализируем клиент YouTube API
youtube = build('youtube', 'v3', developerKey=YOUTUBE_API_KEY)

# Функция поиска видео на YouTube
async def search_youtube(query):
    request = youtube.search().list(
        q=query,
        part='snippet',
        type='video',
        maxResults=5
    )
    response = request.execute()
    results = []
    for item in response.get('items', []):
        title = item['snippet']['title']
        video_id = item['id']['videoId']
        url = f"https://youtu.be/{video_id}"
        results.append((title, url))
    return results

# Команда /start
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("Привет! Напиши название песни или исполнителя для поиска на YouTube.")

# Обработчик сообщений для поиска
async def search_handler(update: Update, context: ContextTypes.DEFAULT_TYPE):
    query = update.message.text
    results = await search_youtube(query)
    if not results:
        await update.message.reply_text("Результаты не найдены.")
    else:
        for title, url in results:
            await update.message.reply_text(f"{title}\n{url}")

# Основная функция запуска
async def main():
    app = ApplicationBuilder().token(TOKEN).build()

    app.add_handler(CommandHandler("start", start))
    app.add_handler(MessageHandler(filters.TEXT & (~filters.COMMAND), search_handler))

    await app.run_polling()

if __name__ == "__main__":
    import asyncio
    asyncio.run(main())
