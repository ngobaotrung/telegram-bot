# telegram-bot
import hashlib
from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, ContextTypes

# Thay bằng Token bot của bạn
BOT_TOKEN = "7385603662:AAEcmXYznue-FgS0XDAaMwpT_JaL9lpAtDk"

# Hàm xử lý MD5 để đoán Tài/Xỉu
def md5_to_taixiu(md5_hash: str) -> str:
    try:
        # Kiểm tra chuỗi MD5 có hợp lệ không
        if len(md5_hash) != 32:
            return "Chuỗi MD5 không hợp lệ. Phải đủ 32 ký tự."

        # Lấy 3 cặp hex cuối (6 ký tự cuối cùng)
        byte1 = int(md5_hash[-6:-4], 16)
        byte2 = int(md5_hash[-4:-2], 16)
        byte3 = int(md5_hash[-2:], 16)

        total = byte1 + byte2 + byte3
        ket_qua = "Tài" if 11 <= (total % 16) <= 17 else "Xỉu"
        return f"Byte cuối: {byte1}, {byte2}, {byte3} | Tổng: {total % 16} → {ket_qua}"
    except Exception as e:
        return f"Lỗi xử lý: {str(e)}"

# Lệnh /start
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("Chào bạn! Gửi chuỗi MD5 theo cú pháp:\n/md5 <chuỗi>")

# Lệnh /md5
async def md5_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    if not context.args:
        await update.message.reply_text("Bạn chưa nhập chuỗi MD5. Ví dụ:\n/md5 d41d8cd98f00b204e9800998ecf8427e")
        return
    md5_input = context.args[0]
    result = md5_to_taixiu(md5_input)
    await update.message.reply_text(result)

# Khởi tạo bot
app = ApplicationBuilder().token(BOT_TOKEN).build()
app.add_handler(CommandHandler("start", start))
app.add_handler(CommandHandler("md5", md5_command))

# Chạy bot
if __name__ == '__main__':
    print("Bot đang chạy...")
    app.run_polling()
