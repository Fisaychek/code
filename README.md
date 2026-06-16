import asyncio
import discord
from discord.ext import commands
import sympy

intents = discord.Intents.default()
intents.message_content = True

bot = commands.Bot(command_prefix="!", intents=intents)

is_active = False


@bot.event
async def on_ready():
    print(f"Бот {bot.user.name} успешно запущен и готов к работе!")


@bot.command(name="start")
async def wake_up(ctx):
    global is_active
    if is_active:
        await ctx.send("Так точно, хозяин! Я уже не сплю и готов к работе.")
    else:
        is_active = True
        await ctx.send("🤖 *Зевает...* Так точно, хозяин! Бот Валера проснулся и готов считать.")


@bot.command(name="sleep")
async def go_to_sleep(ctx):
    global is_active
    if not is_active:
        await ctx.send("Отвали.")
    else:
        is_active = False
        await ctx.send("💤 Так точно, хозяин! Ухожу в режим сна. Команды вычисления временно отключены.")


@bot.command(name="destroy")
async def self_destruct(ctx):
    await ctx.send("🚨 **НАЧАЛСЯ ПРОТОКОЛ САМОУНИЧТОЖЕНИЯ! ВРЕМЯ ДО ВЗРЫВА 10 СЕКУНД!** 🚨")
    
    await asyncio.sleep(10)
    
    await ctx.send("💥💥💥 BOOM!!! 💥💥💥\n💥💥💥💥💥💥💥💥💥💥💥💥💥💥💥💥💥")
    await ctx.send("💀 Бот Валера успешно уничтожен. Связь разорвана.")
    
    print("Бот был уничтожен через команду !destroy")
    await bot.close()


@bot.command(name="calc")
async def calculate(ctx, *, expression: str):
    global is_active

    if not is_active:
        await ctx.send("💤 Бот сейчас спит. Разбудите меня командой `!start`")
        return

    if expression.strip().lower() == "сосал?":
        await ctx.send("какой же ты кожаный идиот... умнее слов не знаешь?")
        return

    try:
        parsed_expr = sympy.sympify(expression)

        raw_result = parsed_expr.evalf()

        if float(raw_result).is_integer():
            result = int(float(raw_result))
        else:
            result = round(float(raw_result), 4)

        await ctx.send(
            f"Так точно, хозяин!\n**Пример:** {expression}\n**Ответ:** {result}"
        )

    except Exception:
        await ctx.send(
            "Дурачек, что за херню ты написал? Убедись, что ввел знаки корректно.\n"
            "Используй знаки: `+` (сложение), `-` (вычитание), `*` (умножение), `/` (деление), `**` (степень)."
        )


bot.run
