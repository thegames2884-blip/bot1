from aiogram import Bot, Dispatcher, types
from aiogram.types import Message, ReplyKeyboardMarkup, KeyboardButton
from aiogram.filters import Command
import asyncio

# 🔐 Tokenni bu yerga yozing
TOKEN = "8589712650:AAF0-dnyBSlXbbdwRPL3ZcfJTfhhAD1vD2c"  # ← bu yerga o'z tokeningizni yozing

# 🔧 Bot va dispatcher
bot = Bot(token=TOKEN)
dp = Dispatcher()
print("🤖 Bot ishga tushmoqda...")

# 📁 Foydalanuvchilarni log qilish uchun funksiyalar
def log_xabar(message: Message):
    user_id = message.from_user.id
    username = message.from_user.username
    full_name = message.from_user.full_name
    text = message.text

    print(f"📩 {full_name} (@{username}) [{user_id}] → {text}")  # terminalda ko‘rsatish
    with open("log.txt", "a", encoding="utf-8") as file:
        file.write(f"{full_name} (@{username}) [{user_id}] → {text}\n")

# 🎛️ Menyu tugmalari
menu = ReplyKeyboardMarkup(
    keyboard=[
        [KeyboardButton(text="🧒 Yoshni aniqlash")],
        [KeyboardButton(text="ℹ️ Ma'lumot"), KeyboardButton(text="📞 Aloqa")],
        [KeyboardButton(text="📚 Kurslar"), KeyboardButton(text="❓ Yordam")],
        [KeyboardButton(text="📈 Statistikalar")]
    ],
    resize_keyboard=True
)

# 📊 Statistikani saqlash
foydalanuvchilar = set()

# 🚀 /start komandasi
@dp.message(Command("start"))
async def start_handler(message: Message):
    log_xabar(message)
    foydalanuvchilar.add(message.from_user.id)
    await message.answer("👋 Salom! Menyudan tanlang:", reply_markup=menu)

# 💬 Xabarlar uchun universal handler
@dp.message()
async def universal_handler(message: Message):
    log_xabar(message)
    foydalanuvchilar.add(message.from_user.id)
    text = message.text

    if text == "🧒 Yoshni aniqlash":
        await message.answer("📥 Iltimos, yoshingizni kiriting (masalan: 18):")

    elif text.isdigit():
        yosh = int(text)
        if yosh < 13:
            await message.answer("🧒 Siz bola ekansiz.")
        elif yosh < 20:
            await message.answer("🧑 Siz o'smir ekansiz.")
        else:
            await message.answer("🧔 Siz katta yoshdagisiz.")

    elif text == "ℹ️ Ma'lumot":
        await message.answer(
            "🐍 *Python dasturlash tili haqida:*\n\n"
            "Python — bu yangi boshlovchilar uchun eng qulay va o'qilishi oson dasturlash tilidir.\n"
            "U 1991-yilda Guido van Rossum tomonidan yaratilgan.\n\n"
            "📚 O'rganish uchun manbalar:\n"
            "- [python.org](https://python.org)\n"
            "- [mohirdev.uz](https://mohirdev.uz)\n"
            "- YouTube: 'Python darslari o'zbek tilida'",
            parse_mode="Markdown"
        )

    elif text == "📞 Aloqa":
        await message.answer("📞 Biz bilan bog'lanish: +998 77 274 60 47")

    elif text == "📚 Kurslar":
        await message.answer("🎓 Python, Telegram bot, va boshqa kurslar mavjud.")

    elif text == "❓ Yordam":
        await message.answer("❔ Savollaringizni yozing, biz yordam beramiz.")

    elif text == "📈 Statistikalar":
        await message.answer(f"📊 Botdan foydalanganlar soni: {len(foydalanuvchilar)} ta")

    else:
        await message.answer(f"🗣 Siz yozdingiz: {text}")

# 🏁 Botni ishga tushirish
async def main():
    await dp.start_polling(bot)

if __name__ == "__main__":
    asyncio.run(main())
