# bot.py (русская версия для MorningQuoteBot)
# Читает английские цитаты из файла quotes.txt (300 строк) и отвечает на команды.
# Требования: python-telegram-bot==20.7
# Запуск: установи переменную окружения BOT_TOKEN и запусти `python bot.py`

import os
import random
from pathlib import Path
from telegram import Update
from telegram.ext import Application, CommandHandler, ContextTypes

BOT_TOKEN = os.environ.get("BOT_TOKEN") or "ВСТАВЬ_ТОКЕН_ЕСЛИ_ТЕСТИРУЕШЬ_ЛОКАЛЬНО"
QUOTES_FILE = Path("quotes.txt")

def load_quotes() -> list[str]:
    if not QUOTES_FILE.exists():
        print(f"[WARN] Файл {QUOTES_FILE} не найден. Помести quotes.txt рядом с bot.py")
        return [
            "Start where you are. Use what you have. Do what you can.",
            "Small steps every day lead to big change.",
            "You are enough. You are becoming.",
            "Discipline is destiny. Do one thing well this morning.",
            "Choose one brave act today."
        ]
    with QUOTES_FILE.open("r", encoding="utf-8") as f:
        quotes = [line.strip() for line in f if line.strip()]
    return quotes or ["A fresh start begins now."]

QUOTES = load_quotes()

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    text = (
        "☀️ Привет! Я MorningQuoteBot (RU).\n\n"
        "Команды:\n"
        "/quote — прислать вдохновляющую цитату (EN)\n"
        "/help — справка\n"
    )
    await update.message.reply_text(text)

async def help_cmd(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("Напиши /quote — пришлю короткую цитату на английском из твоего файла quotes.txt.")

async def quote(update: Update, context: ContextTypes.DEFAULT_TYPE):
    if not QUOTES:
        await update.message.reply_text("Цитаты не загружены. Добавь файл quotes.txt и перезапусти бота.")
        return
    line = random.choice(QUOTES)
    await update.message.reply_text(f"💡 {line}")

def main():
    app = Application.builder().token(BOT_TOKEN).build()
    app.add_handler(CommandHandler("start", start))
    app.add_handler(CommandHandler("help", help_cmd))
    app.add_handler(CommandHandler("quote", quote))
    app.run_polling()

if __name__ == "__main__":
    main()
