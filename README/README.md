# Telegram Translator Bot

Это телеграм-бот, разработанный на Python, который выполняет автоматический перевод текста между русским и английским языками. Бот также предлагает синонимы для отдельных слов в зависимости от языка. Пользователь может выбирать направление перевода с помощью команд.

## Основной функционал

- Перевод с русского на английский и обратно
- Поддержка двух команд:
  - `/ru` — перевод с русского на английский
  - /en — перевод с английского на русский
- Поиск синонимов:
  - Английские синонимы через библиотеку WordNet
  - Русские синонимы — реализованы на примерах
- Работа с несколькими пользователями
- Обработка как отдельных слов, так и предложений

## Используемые библиотеки

-нглийским языками. Бот — взаимодействие с Telegram API
- deep-translator — перевод текста
- nltk — работа с английскими синонимами
- langdetect — определение языка текста

## Установка и запуск

### Шаг 1. Установка зависимостей

```bash
pip install python-telegram-bot deep-translator nltk langdetect

Шаг 2. Загрузка данных для NLTK

python3 -m nltk.downloader wordnet omw-1.4

Шаг 3. Настройка токена

В файле bot_with_lang_choice.py необходимо указать свой токен Telegram-бота:

TOKEN = '7866119149:AAHRCZub7EfYRB9BhSc45VN7lN1rZojRRVY'

Шаг 4. Запуск бота

python3 bot_with_lang_choice.py

Команды бота
 • /start — выводит инструкцию
 • /ru — устанавливает перевод с русского на английский
 • /en — устанавливает перевод с английского на русский
 • Просто отправьте слово или фразу после выбора языка — бот автоматически переведет сообщение и при необходимости предложит синонимы

Пример использования
 1. Отправить команду /en
 2. Отправить текст: apple
 3. Бот переведет на русский язык и покажет синонимы на английском

код бота

from telegram import Update
from telegram.ext import ApplicationBuilder, MessageHandler, CommandHandler, ContextTypes, filters
from deep_translator import GoogleTranslator
from langdetect import detect
from nltk.corpus import wordnet
import nltk
import random

nltk.download('wordnet')
nltk.download('omw-1.4')

# Определение направления перевода
def detect_language(text):
    lang = detect(text)
    if lang == 'ru':
        return 'russian', 'english'
    else:
        return 'english', 'russian'

# Получение синонимов для английского (через WordNet)
def get_english_synonyms(word):
    synonyms = set()
    for syn in wordnet.synsets(word):
        for lemma in syn.lemmas():
            name = lemma.name().replace('_', ' ')
            if name.lower() != word.lower():
                synonyms.add(name)
    return list(synonyms)

# Заглушка: синонимы на русском (можно подключить Yandex Dictionary API)
def get_russian_synonyms(word):
    # Для полноценных синонимов можно использовать Яндекс Словарь API
    # Здесь просто примеры
    simple_synonyms = {
        "большой": ["огромный", "громадный", "великий"],
        "маленький": ["крошечный", "небольшой", "миниатюрный"]
    }
    return simple_synonyms.get(word.lower(), [])

# Обработка текста: перевод и синонимы
async def process(update: Update, context: ContextTypes.DEFAULT_TYPE):
    text = update.message.text
    from_lang, to_lang = detect_language(text)

    try:
        translated = GoogleTranslator(source=from_lang, target=to_lang).translate(text)
        reply = f"**Перевод** ({from_lang} → {to_lang}):\n{translated}"

        # Если короткое сообщение (одно слово) — предложим синонимы
        if len(text.split()) == 1:
            word = text.strip()
            if from_lang == 'english':
                synonyms = get_english_synonyms(word)
            else:
                synonyms = get_russian_synonyms(word)
            if synonyms:
                reply += f"\n\nСинонимы для «{word}»:\n• " + "\n• ".join(synonyms)
            else:
                reply += "\n\nСинонимы не найдены."

        await update.message.reply_text(reply)

    except Exception as e:
        await update.message.reply_text(f"Ошибка: {e}")

# Команда /start
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text(
        "Привет! Я — переводчик и помощник по синонимам.\n"
        "Просто отправь слово или фразу, и я:\n"
        "- переведу с русского на английский и наоборот\n"
        "- покажу синонимы, если это одно слово"
    )

# Запуск бота
def main():
    TOKEN = '7866119149:AAHRCZub7EfYRB9BhSc45VN7lN1rZojRRVY'
    app = ApplicationBuilder().token(TOKEN).build()

    app.add_handler(CommandHandler("start", start))
    app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, process))

    print("Бот запущен. Ожидаю сообщения...")
    app.run_polling()

if name == "__main__":
    main()

Автор проекта

ФИО: (Чанышева Зарина, Дамбаева Валерия, Мироненко Карина,Евлантьева Анастасия, Капапперо Аллета)
Группа: (10.1-102)
Дата выполнения: Май 2025
Предмет:цифровой переводчик PRO 
