# meta developer: @modwini
import asyncio
from telethon import events
from telethon.tl import types
from .. import loader, utils

@loader.tds
class GoSpamMod(loader.Module):
    """Модуль для автоматического отправления 'го' каждые 15 секунд в @MonacoGamebot"""

    strings = {"name": "GoSpam"}

    def __init__(self):
        self.task = None

    async def go_run(self, client, chat_id):
        while True:
            await client.send_message(chat_id, "го")
            await asyncio.sleep(15)

    @loader.unrestricted
    @loader.ratelimit
    async def farmcmd(self, message: types.Message):
        """Запустить автоматическое отправление 'го'"""
        if self.task:
            return await utils.answer(message, "Автоматическое отправление 'го' уже запущено.")
        await utils.answer(message, "Автоматическое отправление 'го' запущено.")
        client = message.client
        self.task = asyncio.create_task(self.go_run(client, message.to_id))

    @loader.unrestricted
    @loader.ratelimit
    async def stopcmd(self, message: types.Message):
        """Остановить автоматическое отправление 'го'"""
        if not self.task:
            return await utils.answer(message, "Автоматическое отправление 'го' не запущено.")
        self.task.cancel()
        self.task = None
        await utils.answer(message, "Автоматическое отправление 'го' остановлено.")
