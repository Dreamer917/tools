```bash
import discord
import random
import asyncio

# Токен вашего бота Discord
TOKEN = 'YOUR_DISCORD_BOT_TOKEN'

# ID канала, в который вы хотите отправить сообщения
channel_id = 1234567890  # Замените на фактический ID вашего канала

# Функция для отправки сообщений в канал
async def send_messages(channel):
    messages = [
        "Привет!",
        "Как дела?",
        "Это случайное сообщение!",
        "Еще одно случайное сообщение."
    ]
    for _ in range(5):  # Отправляем 5 случайных сообщений из списка
        message = random.choice(messages)
        await channel.send(message)
        await asyncio.sleep(3)  # Подождите 3 секунды между сообщениями

# Создаем клиент Discord
client = discord.Client()

# Событие запуска бота
@client.event
async def on_ready():
    print(f'Бот {client.user} подключился к Discord!')
    channel = client.get_channel(channel_id)
    if channel:
        await send_messages(channel)
    else:
        print('Канал не найден.')

# Запускаем бота
client.run(TOKEN)
```
