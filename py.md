# JARVIS-open-AI 

# bot.py
import random
import logging
from telegram import Update
from telegram.ext import Application, CommandHandler, ContextTypes
from config import BOT_TOKEN  # импортируем токен из config.py

Настраиваем логирование, чтобы видеть ошибки
logging.basicConfig(format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO)
logger = logging.getLogger(name)

 Команды  БОТА 

async def start_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Обработчик команды /start"""
    user = update.effective_user
    await update.message.reply_text(
        f"Привет, {user.first_name}! 👋\n"
        "Я Карманный Джарвис — твой личный генератор чисел!\n\n"
        "Доступные команды:\n"
        "/dice — число от 0 до 5000\n"
        "/cube — бросить кубик (1-6)\n" 
        "/coin — подбросить монетку\n"
        "/random — случайное число от 1 до 100\n"
        "/custom min max — свой диапазон\n"
        "/help — помощь\n"
        "/rates — тарифы"
    )

async def help_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Обработчик команды /help"""
    help_text = """
🎲 *Доступные команды:*

/start - Начать работу
/dice - Случайное число от 0 до 5000
/cube - Бросить кубик (1-6)
/coin - Подбросить монетку
/random - Число от 1 до 100
/custom min max - Свой диапазон (например: /custom 10 50)
/rates - Тарифы и возможности
/help - Эта справка

💡 *Примеры:*
• /dice
• /cube  
• /coin
• /custom 1 1000
    """
    await update.message.reply_text(help_text, parse_mode='Markdown')

async def dice_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Обработчик команды /dice - число от 0 до 5000"""
    number = random.randint(0, 5000)
    await update.message.reply_text(f"🎲 *Гигантский кубик*\n\nДиапазон: 0 — 5000\nВыпало: *{number}*", parse_mode='Markdown')

async def cube_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Обработчик команды /cube - классический кубик 1-6"""
    roll = random.randint(1, 6)
    # Красивые символы кубика
    dice_faces = ["⚀", "⚁", "⚂", "⚃", "⚄", "⚅"]
    await update.message.reply_text(f"🎯 *Классический кубик*\n\n{dice_faces[roll-1]} Выпало: *{roll}*", parse_mode='Markdown')

async def coin_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Обработчик команды /coin - орёл или решка"""
    side = random.choice(["Орёл", "Решка"])
    emoji = "🦅" if side == "Орёл" else "🪙"
    await update.message.reply_text(f"{emoji} *{side}*!", parse_mode='Markdown')

async def random_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Обработчик команды /random - число от 1 до 100"""
    number = random.randint(1, 100)
    await update.message.reply_text(f"🔢 *Случайное число*\n\nДиапазон: 1 — 100\nРезультат: *{number}*", parse_mode='Markdown')

async def custom_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Обработчик команды /custom - свой диапазон"""
    try:
        if len(context.args) != 2:
            await update.message.reply_text("❌ *Используй:* /custom min max\n*Например:* /custom 10 50", parse_mode='Markdown')
            return
        
        min_val = int(context.args[0])
        max_val = int(context.args[1])
        
        # Проверяем, чтобы min был меньше max
        if min_val > max_val:
            min_val, max_val = max_val, min_val
            
        number = random.randint(min_val, max_val)
        await update.message.reply_text(f"🎰 *Ваш диапазон*\n\nОт {min_val} до {max_val}\nРезультат: *{number}*", parse_mode='Markdown')
        
    except ValueError:
        await update.message.reply_text("❌ Вводи только целые числа!")

async def rates_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Обработчик команды /rates - тарифы"""
    rates_text = """
 *ТАРИФЫ*

 *БЕСПЛАТНЫЙ* (0₽)
• 20 генераций в день
• Числа до 10 000
• Базовые команды

 *ПРЕМИУМ* (149₽/месяц)
• Безлимитная генерация
• Все команды (/dice, /cube, /coin)
• Числа до 100 000
• История 100 чисел


 *PRO* (299₽/месяц)
1) Всё из Премиум
2) Пакетная генерация
3) Настройка диапазонов
 4) API доступ

*Для покупки:* пиши @whosayacab
    """
    await update.message.reply_text(rates_text, parse_mode='Markdown')

async def error_handler(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Обработчик ошибок"""
    logger.error(f"Ошибка: {context.error}")
    await update.message.reply_text("😬 Что-то пошло не так... Попробуй ещё раз")

 ЗАПУСК БОТА 

def main():
    """Главная функция запуска бота"""
    print("🤖 Запускаю бота...")
    
    # Создаём приложение бота
    app = Application.builder().token(BOT_TOKEN).build()
    
    # Регистрируем команды
    app.add_handler(CommandHandler("start", start_command))
    app.add_handler(CommandHandler("help", help_command))
    app.add_handler(CommandHandler("dice", dice_command))
    app.add_handler(CommandHandler("cube", cube_command))
    app.add_handler(CommandHandler("coin", coin_command))
    app.add_handler(CommandHandler("random", random_command))
    app.add_handler(CommandHandler("custom", custom_command))
    app.add_handler(CommandHandler("rates", rates_command))
    
    # Обработчик ошибок
    app.add_error_handler(error_handler)
    
    # Запускаем бота
    print("✅ Бот запущен! Для остановки нажми Ctrl+C")
    app.run_polling(allowed_updates=Update.ALL_UPDATES)

if name == 'main':
    main()
