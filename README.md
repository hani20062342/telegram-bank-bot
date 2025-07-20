import logging
from telegram import Update
from telegram.ext import Updater, CommandHandler, MessageHandler, Filters, CallbackContext
import time

# -------------------
# -- الإعدادات --
TOKEN = "8168303879:AAFd_YLM3e0j9PctWvr0RcShomPpsYux4ss"
GROUP_ID = -1002795537801  # معرف المجموعة

# -------------------
# إعداد اللوج للتصحيح
logging.basicConfig(format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO)

# -------------------
# وظيفة الإرسال الدوري للراتب والبخشيش
def send_salary_tip(context: CallbackContext):
    context.bot.send_message(chat_id=GROUP_ID, text="/راتب")
    time.sleep(2)
    context.bot.send_message(chat_id=GROUP_ID, text="/بخشيش")

# -------------------
# استقبال الرسائل الخاصة وإعادة إرسالها للمجموعة
def private_message_handler(update: Update, context: CallbackContext):
    msg = update.message
    context.bot.send_message(chat_id=GROUP_ID, text=f"{msg.from_user.first_name} قال:\n{msg.text}")

# -------------------
# بدء التشغيل
def main():
    updater = Updater(TOKEN, use_context=True)
    dp = updater.dispatcher

    # التعامل مع الرسائل الخاصة
    dp.add_handler(MessageHandler(Filters.private & ~Filters.command, private_message_handler))

    # جدولة الراتب والبخشيش كل 10 دقائق
    updater.job_queue.run_repeating(send_salary_tip, interval=600, first=10)

    updater.start_polling()
    updater.idle()

if __name__ == '__main__':
    main()
