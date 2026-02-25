from telegram import Update, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import ApplicationBuilder, CommandHandler, CallbackQueryHandler, MessageHandler, ContextTypes, filters
from datetime import datetime, timedelta

TOKEN = "8549118561:AAE7PbALK4DqqG-3vBJk4zFNu-iw1Uggbq4"

ADMINS = [7686314998, 823428354]

USDT_ADDRESS = "TEPKgukbD8CswerwySvCj31nFwT7yeybKW"
SHAMCASH_ID = "9a3802ce2094691c43931820c51bd08f"

GIFT_50 = "https://www.eneba.com/razer-razer-gold-gift-card-50-usd-key-global"
GIFT_100 = "https://en.likecard.com/game-cards/razer-/razer-global/razer-global-100.html"

COUNTRIES = [
    "السعودية", "الإمارات", "قطر", "سوريا",
    "البحرين", "إندونيسيا", "تركيا", "ألمانيا", "لبنان"
]


# ================= توليد الأيام =================
def generate_days():
    today = datetime.now()

    start_month = today.month + 3
    start_year = today.year
    if start_month > 12:
        start_month -= 12
        start_year += 1

    end_month = today.month + 5
    end_year = today.year
    if end_month > 12:
        end_month -= 12
        end_year += 1

    start_date = datetime(start_year, start_month, 1)

    if end_month == 12:
        end_date = datetime(end_year + 1, 1, 1) - timedelta(days=1)
    else:
        end_date = datetime(end_year, end_month + 1, 1) - timedelta(days=1)

    days = []
    current = start_date
    while current <= end_date:
        days.append(current.strftime("%d/%m/%Y"))
        current += timedelta(days=7)

    return days


# ================= القوائم =================

def language_menu():
    return InlineKeyboardMarkup([
        [
            InlineKeyboardButton("🇸🇦 العربية", callback_data="lang_ar"),
            InlineKeyboardButton("🇬🇧 English", callback_data="lang_en")
        ]
    ])

def main_menu():
    return InlineKeyboardMarkup([
        [InlineKeyboardButton("🎬 رؤية فيديوهاتي", callback_data="videos")],
        [InlineKeyboardButton("💬 تواصل مباشر", callback_data="chat")],
        [InlineKeyboardButton("📅 حجز موعد", callback_data="booking")]
    ])

def control_buttons():
    return [
        [InlineKeyboardButton("🏠 الرئيسية", callback_data="home")],
        [InlineKeyboardButton("🔄 Restart", callback_data="restart")]
    ]

def payment_menu():
    return InlineKeyboardMarkup([
        [InlineKeyboardButton("💰 Crypto USDT", callback_data="pay_crypto")],
        [InlineKeyboardButton("💳 ShamCash", callback_data="pay_sham")],
        [InlineKeyboardButton("🎁 Gift Card", callback_data="pay_gift")],
        [InlineKeyboardButton("🅿️ PayPal", callback_data="pay_paypal")],
        *control_buttons()
    ])


# ================= START =================

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text(
        "🌸 مرحباً بك\nاختر اللغة:",
        reply_markup=language_menu()
    )


# ================= BUTTON HANDLER =================

async def button_handler(update: Update, context: ContextTypes.DEFAULT_TYPE):
    query = update.callback_query
    data = query.data
    await query.answer()

    try:
        await query.message.delete()
    except:
        pass

    # Restart
    if data == "restart":
        context.user_data.clear()
        await query.message.reply_text("🔄 إعادة التشغيل", reply_markup=language_menu())
        return

    # لغة
    if data.startswith("lang"):
        context.user_data["lang"] = data
        await query.message.reply_text("اختر الخدمة:", reply_markup=main_menu())
        return

    # الرئيسية
    if data == "home":
        await query.message.reply_text("اختر الخدمة:", reply_markup=main_menu())
        return

    # تواصل مباشر
    if data == "chat":
        await query.message.reply_text(
            "💬 تواصل مباشر:\nhttps://t.me/Nayakhery",
            reply_markup=InlineKeyboardMarkup(control_buttons())
        )
        return

    # فيديوهات
    if data == "videos":
        context.user_data["service_name"] = "فيديوهات"
        context.user_data["gift_link"] = GIFT_50

        await query.message.reply_text(
            "🎬 تكلفة الخدمة: 50$\nاختر طريقة الدفع:",
            reply_markup=payment_menu()
        )
        return

    # حجز → الدول
    if data == "booking":
        context.user_data["service_name"] = "حجز موعد"
        context.user_data["gift_link"] = GIFT_100

        keyboard = []
        for country in COUNTRIES:
            keyboard.append([InlineKeyboardButton(country, callback_data=f"country_{country}")])

        keyboard += control_buttons()

        await query.message.reply_text(
            "اختر الدولة:",
            reply_markup=InlineKeyboardMarkup(keyboard)
        )
        return

    # اختيار دولة → أيام
    if data.startswith("country_"):
        country = data.replace("country_", "")
        context.user_data["country"] = country

        days = generate_days()
        keyboard = []

        for day in days:
            keyboard.append([InlineKeyboardButton(day, callback_data=f"day_{day}")])

        keyboard += control_buttons()

        await query.message.reply_text(
            f"📍 {country}\nاختر اليوم:",
            reply_markup=InlineKeyboardMarkup(keyboard)
        )
        return

    # اختيار يوم → الدفع
    if data.startswith("day_"):
        day = data.replace("day_", "")
        context.user_data["day"] = day

        await query.message.reply_text(
            f"📅 اليوم: {day}\n💰 تكلفة الحجز: 100$\nاختر طريقة الدفع:",
            reply_markup=payment_menu()
        )
        return

    # الدفع
    if data == "pay_crypto":
        text = f"USDT TRC20:\n{USDT_ADDRESS}"

    elif data == "pay_sham":
        text = f"ShamCash:\n{SHAMCASH_ID}"

    elif data == "pay_gift":
        text = context.user_data.get("gift_link")

    elif data == "pay_paypal":
        text = "🚫 PayPal متوقف حالياً"

    else:
        return

    await query.message.reply_text(text, reply_markup=InlineKeyboardMarkup(control_buttons()))


# ================= استقبال الرسائل =================

async def user_message_handler(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user = update.effective_user
    service = context.user_data.get("service_name")
    country = context.user_data.get("country", "")
    day = context.user_data.get("day", "")

    for admin in ADMINS:
        await context.bot.forward_message(
            chat_id=admin,
            from_chat_id=update.effective_chat.id,
            message_id=update.message.message_id
        )

    if service:
        await update.message.reply_text("تم استلام الإثبات ⏳")

        for admin in ADMINS:
            await context.bot.send_message(
                chat_id=admin,
                text=f"طلب جديد\n@{user.username}\nID: {user.id}\nالخدمة: {service}\nالدولة: {country}\nاليوم: {day}"
            )
    else:
        await update.message.reply_text("يرجى استخدام الأزرار فقط.")


# ================= تشغيل =================

app = ApplicationBuilder().token(TOKEN).build()

app.add_handler(CommandHandler("start", start))
app.add_handler(CallbackQueryHandler(button_handler))
app.add_handler(MessageHandler(filters.ALL, user_message_handler))

print("Bot is running...")
app.run_polling()
