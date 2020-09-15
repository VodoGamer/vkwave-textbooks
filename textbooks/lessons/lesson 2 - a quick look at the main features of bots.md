# Основные функции бота

![vkwave](https://user-images.githubusercontent.com/28061158/75329873-7f738200-5891-11ea-9565-fd117ea4fc9e.jpg)

В этом уроке мы сделаем следующее:
+ Расмотрим [фильтры vkwave](#Фильтры-vkwave)
+ Поработаем с методами [Api ВК](#Api-Vk)
+ Создадим [простую клавиатуру](#Клавиатуры)

# Фильтры vkwave
Фильтры это одна из самых удобных частей vkwave.
Фильтры нужны для _фильтрации_ событий.
Фильтры необходимо вписывать в декоратор:

```python
from vkwave.bots import SimpleLongPollBot

bot = SimpleLongPollBot(tokens="MyToken", group_id=123456789)

@bot.message_handler(bot.command_filter("начало"))
def handle(event: bot.SimpleBotEvent) -> str:
    return "Фильтр на команду сработал!"

bot.run_forever()
```

Итак, если мы напишем боту "!начало" или "/начало", то он ответит "Фильтр на команду сработал!" И всё это из-за простой строки:

```python
@bot.message_handler(bot.command_filter("начало"))
```

Давайте посмотрим на все `основные` фильтры:
```python
bot.text_filter("начало") # Если пользователь отправит это сообщение, фильтр сработает
bot.command_filter(commands=("магазин", "помощь"), prefixes=("!", "/")) # Если пользователь отправит сообщение !магазин или /магазин или /помощь и так далее, фильтр сработает
bot.conversation_type_filter(from_what="from_pm") # Проверяет, откуда пришло сообщение
"""
bot.conversation_type_filter(from_what="xxx")
Вместо xxx может быть:
from_pm - личные сообщения
from_chat - беседа
"""
bot.text_contains_filter("+") # Проверяет сообщение на присутствие слова
```

Также можно использовать несколько фильтров в одном декораторе:
```python
@bot.message_handler(bot.command_filter("начало"), bot.conversation_type_filter(from_what="from_chat"))
```

Также можно создавать свои фильтры, почитать побробнее о фильтрах можно [тут](https://github.com/fscdev/vkwave/blob/master/docs/bots/filters.md#создание-своих-фильтров)

# Api Vk

# Клавиатуры