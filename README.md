# meta developer: @modwini
import asyncio
from telethon import events
from .. import loader, utils

@loader.tds
class FarmMonacoMod(loader.Module):
    """Модуль для автоматического отправления 'го' каждые 15 секунд в @MonacoGamebot"""

    strings = {"name": "MonacoFarm"}

    def __init__(self):
        self.task = None

    async def go_run(self, client):
        while True:
            await client.send_message('@monacogamebot', "го")
            await asyncio.sleep(15)

    @loader.unrestricted
    @loader.ratelimit
    async def farmcmd(self, message):
        """Запустить автоматическое отправление 'го'"""
        if self.task:
            return await message.edit("Автоматическое отправление 'го' уже запущено.")
        await message.edit("Автоматическое отправление 'го' запущено.")
        client = message.client
        self.task = asyncio.create_task(self.go_run(client))

    @loader.unrestricted
    @loader.ratelimit
    async def stopcmd(self, message):
        """Остановить автоматическое отправление 'го'"""
        if not self.task:
            return await message.edit("Автоматическое отправление 'го' не запущено.")
        self.task.cancel()
        self.task = None
        await message.edit("Автоматическое отправление 'го' остановлено.")
