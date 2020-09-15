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

Для работы с Api Vk необходимо получить событие.
Давйте приведём пример:
```python
from vkwave.bots import SimpleLongPollBot

bot = SimpleLongPollBot(tokens="MyToken", group_id=123456789)

@bot.message_handler(bot.text_contains_filter("начало"))
def handle(event: bot.SimpleBotEvent) -> str:
    user_data = (await event.api_ctx.users.get(user_ids=event.object.object.message.peer_id)).response[0] # обращаемся к апи
    await event.answer(message=f"Привет, {user_data.first_name}") # отправляем сообщение

bot.run_forever()
```

В данном примере, был использован метод vk api - users.get, вы же можете использовать любые методы [отсюда](https://vk.com/dev/methods)

# Клавиатуры

В Вк Апи есть клавиатуры, для удобства в vkwave встроен генератор клавиатур

Для начала импортируем всё необходимое:

```python
from vkwave.bots.utils.keyboards import Keyboard
from vkwave.bots.utils.keyboards.keyboard import ButtonColor
```

Затем, "зарегистрируем" клавиатуру:

```python
kb = Keyboard(one_time=True)
# kb = Keyboard(one_time=False, inline=True) # Inline клавиатура
```

Далее, добавим кнопки:
```python
kb.add_text_button("blue hello", color=ButtonColor.PRIMARY)

kb.add_row()
kb.add_text_button("white hello", color=ButtonColor.SECONDARY)
```
И затем надо отправить сообщение с клавиатурой. Давайте расмотрим готовый код:

```python
from vkwave.bots.utils.keyboards import Keyboard
from vkwave.bots.utils.keyboards.keyboard import ButtonColor
from vkwave.bots import SimpleLongPollBot

bot = SimpleLongPollBot(tokens="MyToken", group_id=123456789)

@bot.message_handler(bot.text_contains_filter("начало"))
def handle(event: bot.SimpleBotEvent) -> str:
    kb = Keyboard(one_time=True)
    kb.add_text_button("Тест кнопки!", color=ButtonColor.PRIMARY)

    user_data = (await event.api_ctx.users.get(user_ids=event.object.object.message.peer_id)).response[0] # обращаемся к апи
    await event.answer(message=f"Привет, {user_data.first_name}", keyboard=kb.get_keyboard()) # отправляем сообщение

bot.run_forever()
```