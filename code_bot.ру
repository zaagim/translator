from telegram import Update
from telegram.ext import ApplicationBuilder, MessageHandler, CommandHandler, ContextTypes, filters
from deep_translator import GoogleTranslator
from langdetect import detect
from nltk.corpus import wordnet

import random

# Определение языка и направление перевода
def detect_language(text):
    lang = detect(text)
    if lang == 'ru':
        return 'russian', 'english'
    else:
        return 'english', 'russian'

# Функция синонимической замены (только для английского языка)
def generate_synonyms(text, from_lang, to_lang):
    if from_lang != 'english':
        return []

    words = text.split()
    variants = []
    for _ in range(2):  # две попытки
        new_sentence = []
        for word in words:
            synonyms = wordnet.synsets(word)
            lemmas = [lemma.name().replace('_', ' ') for s in synonyms for lemma in s.lemmas()]
            if lemmas:
                new_word = random.choice(lemmas)
                new_sentence.append(new_word)
            else:
                new_sentence.append(word)
        sentence = " ".join(new_sentence)
        try:
            translated = GoogleTranslator(source='english', target='russian').translate(sentence)
            variants.append(translated)
        except:
            continue
    return variants

# Основная функция перевода
async def translate(update: Update, context: ContextTypes.DEFAULT_TYPE):
    original_text = update.message.text
    from_lang, to_lang = detect_language(original_text)

    try:
        base_translation = GoogleTranslator(source=from_lang, target=to_lang).translate(original_text)
        response = f"**Перевод** ({from_lang} → {to_lang}):\n1) {base_translation}"

        variations = []

        # Вариант 2: перестановка слов
        words = original_text.split()
        if len(words) > 2:
            random.shuffle(words)
            shuffled = " ".join(words)
            shuffled_translation = GoogleTranslator(source=from_lang, target=to_lang).translate(shuffled)
            variations.append(shuffled_translation)

        # Вариант 3–4: синонимы (только если с английского)
        synonyms_variants = generate_synonyms(original_text, from_lang, to_lang)
        variations.extend(synonyms_variants)

        for i, v in enumerate(variations, start=2):
            response += f"\n{i}) {v}"

        await update.message.reply_text(response)

    except Exception as e:
        await update.message.reply_text(f"Ошибка перевода: {e}")

# Команда /start
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("Привет! Отправь текст, и я переведу его с русского на английский и обратно, включая несколько вариантов.")

# Запуск бота
def main():
    TOKEN = '7866119149:AAHRCZub7EfYRB9BhSc45VN7lN1rZojRRVY' 
    app = ApplicationBuilder().token(TOKEN).build()

    app.add_handler(CommandHandler("start", start))
    app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, translate))

    print("Бот запущен...")
    app.run_polling()

if name == "__main__":
    main()
