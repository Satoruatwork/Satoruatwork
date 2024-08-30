
import os
from telegram import Update
from telegram.ext import Updater, CommandHandler, CallbackContext
import youtube_dl

# Function to start the bot
def start(update: Update, context: CallbackContext) -> None:
    update.message.reply_text("Hello! I'm your music bot. Use /play <song name> to play music.")

# Function to play music
def play(update: Update, context: CallbackContext) -> None:
    query = ' '.join(context.args)
    if not query:
        update.message.reply_text("Please provide a song name.")
        return
    
    update.message.reply_text(f"Searching for {query}...")

    # YouTube search and download
    ydl_opts = {
        'format': 'bestaudio/best',
        'postprocessors': [{
            'key': 'FFmpegExtractAudio',
            'preferredcodec': 'mp3',
            'preferredquality': '192',
        }],
        'outtmpl': '/tmp/%(title)s.%(ext)s',
    }

    with youtube_dl.YoutubeDL(ydl_opts) as ydl:
        info_dict = ydl.extract_info(f"ytsearch:{query}", download=True)
        video = info_dict['entries'][0]
        title = video.get('title', None)
        file_path = f"/tmp/{title}.mp3"
    
    update.message.reply_text(f"Playing {title}...")
    # Here you would typically send the audio file to the user
    # update.message.reply_audio(audio=open(file_path, 'rb'))

def main():
    # Insert your bot token here
    updater = Updater("YOUR_BOT_TOKEN")

    dispatcher = updater.dispatcher

    # Register the commands
    dispatcher.add_handler(CommandHandler("start", start))
    dispatcher.add_handler(CommandHandler("play", play))

    # Start the bot
    updater.start_polling()
    updater.idle()

if __name__ == '__main__':
    main()
git clone https://github.com/yourusername/telegram-music-bot.git
cd telegram-music-bot
git add bot.py
git commit -m "Initial commit with bot code"
git push origin main
