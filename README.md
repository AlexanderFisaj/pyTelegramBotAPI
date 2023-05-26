
[![PyPi Package Version](https://img.shields.io/pypi/v/pyTelegramBotAPI.svg)](https://pypi.python.org/pypi/pyTelegramBotAPI)
[![Supported Python versions](https://img.shields.io/pypi/pyversions/pyTelegramBotAPI.svg)](https://pypi.python.org/pypi/pyTelegramBotAPI)
[![Documentation Status](https://readthedocs.org/projects/pytba/badge/?version=latest)](https://pytba.readthedocs.io/en/latest/?badge=latest)
[![PyPi downloads](https://img.shields.io/pypi/dm/pyTelegramBotAPI.svg)](https://pypi.org/project/pyTelegramBotAPI/)
[![PyPi status](https://img.shields.io/pypi/status/pytelegrambotapi.svg?style=flat-square)](https://pypi.python.org/pypi/pytelegrambotapi)

# <p align="center">pyTelegramBotAPI

<p align="center">A simple, but extensible Python implementation for the <a href="https://core.telegram.org/bots/api">Telegram Bot API</a>.</p>
<p align="center">Both synchronous and asynchronous.</p>

## <p align="center">Supported Bot API version: <a href="https://core.telegram.org/bots/api#april-21-2023">6.7</a>!

<h2><a href='https://pytba.readthedocs.io/en/latest/index.html'>Official documentation</a></h2>
<h2><a href='https://pytba.readthedocs.io/ru/latest/index.html'>Official ru documentation</a></h2>
	
## Contents

  * [Приступая к работе](#приступая-к-работе)
  * [Writing your first bot](#writing-your-first-bot)
    * [Prerequisites](#prerequisites)
    * [A simple echo bot](#a-simple-echo-bot)
  * [General API Documentation](#general-api-documentation)
    * [Types](#types)
    * [Methods](#methods)
    * [General use of the API](#general-use-of-the-api)
      * [Message handlers](#message-handlers)
      * [Edited Message handler](#edited-message-handler)
      * [Channel Post handler](#channel-post-handler)
      * [Edited Channel Post handler](#edited-channel-post-handler)
      * [Callback Query handlers](#callback-query-handler)
      * [Shipping Query Handler](#shipping-query-handler)
      * [Pre Checkout Query Handler](#pre-checkout-query-handler)
      * [Poll Handler](#poll-handler)
      * [Poll Answer Handler](#poll-answer-handler)
      * [My Chat Member Handler](#my-chat-member-handler)
      * [Chat Member Handler](#chat-member-handler)
      * [Chat Join request handler](#chat-join-request-handler)
    * [Inline Mode](#inline-mode)
      * [Inline handler](#inline-handler)
      * [Chosen Inline handler](#chosen-inline-handler)
      * [Answer Inline Query](#answer-inline-query)
    * [Additional API features](#additional-api-features)
      * [Middleware handlers](#middleware-handlers)
      * [Custom filters](#custom-filters)
      * [TeleBot](#telebot)
      * [Reply markup](#reply-markup)
  * [Advanced use of the API](#advanced-use-of-the-api)
    * [Using local Bot API Server](#using-local-bot-api-sever)
    * [Asynchronous TeleBot](#asynchronous-telebot)
    * [Sending large text messages](#sending-large-text-messages)
    * [Controlling the amount of Threads used by TeleBot](#controlling-the-amount-of-threads-used-by-telebot)
    * [The listener mechanism](#the-listener-mechanism)
    * [Using web hooks](#using-web-hooks)
    * [Logging](#logging)
    * [Proxy](#proxy)
    * [Testing](#testing)
  * [API conformance limitations](#api-conformance-limitations)
  * [AsyncTeleBot](#asynctelebot)
  * [F.A.Q.](#faq)
    * [How can I distinguish a User and a GroupChat in message.chat?](#how-can-i-distinguish-a-user-and-a-groupchat-in-messagechat)
    * [How can I handle reocurring ConnectionResetErrors?](#how-can-i-handle-reocurring-connectionreseterrors)
  * [The Telegram Chat Group](#the-telegram-chat-group)
  * [Telegram Channel](#telegram-channel)
  * [More examples](#more-examples)
  * [Code Template](#code-template)
  * [Bots using this library](#bots-using-this-library)

## Приступая к работе

Этот API протестирован с Python 3.7-3.11 и Pypy 3.
Существует два способа установки библиотеки:

* Installation using pip (для менеджера пакетов Python):

```
$ pip install pyTelegramBotAPI
```
* Installation from source (requires git):

```
$ git clone https://github.com/eternnoir/pyTelegramBotAPI.git
$ cd pyTelegramBotAPI
$ python setup.py install
```
или:
```
$ pip install git+https://github.com/eternnoir/pyTelegramBotAPI.git
```

Как правило, рекомендуется использовать первый вариант.

*Хотя API готов к работе, он все еще находится в стадии разработки и регулярно обновляется, не забывайте регулярно обновлять его, вызывая:*
```
pip install pytelegrambotapi --upgrade
```

## Написание своего первого бота

### Предварительные условия

Предполагается, что вы [получили токен API с помощью @BotFather](https://core.telegram.org/bots#botfather ). Мы будем называть этот токен `TOKEN`.
Кроме того, у вас есть базовые знания языка программирования Python и, что более важно, [API Telegram Bot](https://core.telegram.org/bots/api ).

### Простой эхо-бот

Класс TeleBot (определенный в \__init__.py ) инкапсулирует все вызовы API в один класс. Он предоставляет такие функции, как `send_xyz` (`send_message`, `send_document` и т.д.) и несколько способов прослушивания входящих сообщений.

Создайте файл с именем `echo_bot.py `.
Затем откройте файл и создайте экземпляр класса TeleBot.
```python
import telebot

bot = telebot.TeleBot("TOKEN", parse_mode=None) # Вы можете установить parse_mode по умолчанию. HTML или MARKDOWN
```
*Примечание: Убедитесь, что вы действительно заменили TOKEN своим собственным токеном API.*

После этого объявления нам нужно зарегистрировать некоторые так называемые обработчики сообщений. Обработчики сообщений определяют фильтры, которые должно пройти сообщение. Если сообщение проходит фильтр, вызывается оформленная функция и входящее сообщение передается в качестве аргумента.

Давайте определим обработчик сообщений, который обрабатывает входящие команды "/start" и "/help".
```python
@bot.message_handler(commands=['start', 'help'])
def send_welcome(message):
	bot.reply_to(message, "Привет, как у тебя дела?")
```
Функция, которая оформляется обработчиком сообщений __, может иметь произвольное имя, однако у нее должен быть только один параметр (the message)__.

Давайте добавим еще один обработчик:
```python
@bot.message_handler(func=lambda m: True)
def echo_all(message):
	bot.reply_to(message, message.text)
```
Этот отправитель возвращает все входящие текстовые сообщения обратно отправителю. Он использует лямбда-функцию для проверки сообщения. Если лямбда-выражение возвращает значение True, сообщение обрабатывается оформленной функцией. Поскольку мы хотим, чтобы все сообщения обрабатывались этой функцией, мы просто всегда возвращаем значение True.

*Примечание: все обработчики тестируются в том порядке, в котором они были объявлены*

Теперь у нас есть базовый бот, который отвечает статическим сообщением на команды "/start" и "/help" и который повторяет остальные отправленные сообщения. Чтобы запустить бота, добавьте следующее в наш исходный файл:
```python
bot.infinity_polling()
```
Готово, вот и все! Наш исходный файл теперь выглядит следующим образом:
```python
import telebot

bot = telebot.TeleBot("YOUR_BOT_TOKEN")

@bot.message_handler(commands=['start', 'help'])
def send_welcome(message):
	bot.reply_to(message, "Howdy, how are you doing?")

@bot.message_handler(func=lambda message: True)
def echo_all(message):
	bot.reply_to(message, message.text)

bot.infinity_polling()
```
Чтобы запустить бота, просто откройте терминал и введите `python echo_bot.py ` чтобы запустить бота! Протестируйте это, отправив команды ('/start' и '/help') и произвольные текстовые сообщения.

## Общая документация по API

### Типы

Все типы определены в types.py . Все они полностью соответствуют [определению типов в Telegram API](https://core.telegram.org/bots/api#available-types ), за исключением поля "from" сообщения, которое переименовано в "from_user" (потому что "from" - это зарезервированный токен Python). Таким образом, к таким атрибутам, как `message_id`, можно получить прямой доступ с помощью `message.message_id`. Обратите внимание, что `message.chat` может быть либо экземпляром `User`, либо `GroupChat` (см. [Как я могу отличить пользователя от GroupChat в message.chat?](#how-can-i-distinguish-a-user-and-a-groupchat-in-messagechat)).

Объект Message также имеет атрибут `content_type', который определяет тип сообщения. `content_type` может быть одной из следующих строк:
`text`, `audio`, `document`, `photo`, `sticker`, `video`, `video_note`, `voice`, `location`, `contact`, `new_chat_members`, `left_chat_member`, `new_chat_title`, `new_chat_photo`, `delete_chat_photo`, `group_chat_created`, `supergroup_chat_created`, `channel_chat_created`, `migrate_to_chat_id`, `migrate_from_chat_id`, `pinned_message`, `web_app_data`.

Вы можете использовать несколько типов в одной функции. Пример:

```content_types=["text", "sticker", "pinned_message", "photo", "audio"]```

### Методы

Все [методы API](https://core.telegram.org/bots/api#available-methods ) находятся в классе TeleBot. Они переименованы в соответствии с общепринятыми соглашениями об именовании Python. Например, `get Me` переименован в `get_me`, а `send Message` - в `send_message`.

### Общее использование API

Ниже приведены некоторые общие варианты использования API.

#### Message handlers/Обработчики сообщений
Обработчик сообщений - это функция, которая оформлена с помощью декоратора `message_handler` экземпляра TeleBot. Обработчики сообщений состоят из одного или нескольких фильтров.
Каждый фильтр должен возвращать значение True для определенного сообщения, чтобы обработчик сообщений получил право обрабатывать это сообщение. Обработчик сообщений объявляется следующим образом (при условии, что `bot` является экземпляром TeleBot):
```python
@bot.message_handler(filters)
def function_name(message):
	bot.reply_to(message, "This is a message handler")
```
`function_name` не связано никакими ограничениями. В обработчиках сообщений допускается любое имя функции. Функция должна принимать не более одного аргумента, который будет сообщением, которое функция должна обработать.
`filters` это список аргументов ключевого слова.
Фильтр объявляется следующим образом: `name=argument`. Один обработчик может иметь несколько фильтров.
TeleBot поддерживает следующие фильтры:

|имя|аргумент(ы)|состояние|
|:---:|---| ---|
|content_types|список строк (default `['text']`)|`True` if message.content_type is in the list of strings.|
|regexp|регулярное выражение в виде строки|`True` если `re.search(regexp_arg)` возвращается `True` и `message.content_type == 'text'` (Смотререть [Регулярные выражения Python](https://docs.python.org/2/library/re.html))|
|commands|список строк|`True` если `message.content_type == 'text'` и `message.text` начинается с команды, которая находится в списке строк.|
|chat_types|список типов чатов|`True` если `message.chat.type` в вашем фильтре|
|func|функция (ссылка на лямбду или функцию)|`True`, если ссылка на лямбду или функцию возвращает `True`|
	
Вот несколько примеров использования фильтров и обработчиков сообщений:

```python
import telebot
bot = telebot.TeleBot("TOKEN")

# Обрабатывает все текстовые сообщения, содержащие команды '/start' или '/help'.
@bot.message_handler(commands=['start', 'help'])
def handle_start_help(message):
	pass

# Обрабатывает все отправленные документы и аудиофайлы
@bot.message_handler(content_types=['document', 'audio'])
def handle_docs_audio(message):
	pass

# Обрабатывает все текстовые сообщения, соответствующие регулярному выражению
@bot.message_handler(regexp="SOME_REGEXP")
def handle_message(message):
	pass

# Обрабатывает все сообщения, для которых лямбда-выражение возвращает значение True
@bot.message_handler(func=lambda message: message.document.mime_type == 'text/plain', content_types=['document'])
def handle_text_doc(message):
	pass

# Который также может быть определен как:
def test_message(message):
	return message.document.mime_type == 'text/plain'

@bot.message_handler(func=test_message, content_types=['document'])
def handle_text_doc(message):
	pass

# Обработчики могут быть объединены для создания функции, которая будет вызвана, если подходит любой из message_handler
# Этот обработчик будет вызван, если сообщение начинается с '/hello' или является каким-либо эмодзи
@bot.message_handler(commands=['hello'])
@bot.message_handler(func=lambda msg: msg.text.encode("utf-8") == SOME_FANCY_EMOJI)
def send_something(message):
    pass
```
**Важно: все обработчики тестируются в том порядке, в котором они были объявлены**

#### Обработчик отредактированных сообщений
Handle edited messages
`@bot.edited_message_handler(filters) # <- передает объект типа сообщения вашей функции`

#### Обработчик сообщений канала
Handle channel post messages
`@bot.channel_post_handler(filters) # <- passes a Message type object to your function`

#### Обработчик сообщений отредактированного канала
Handle edited channel post messages
`@bot.edited_channel_post_handler(filters) # <- passes a Message type object to your function`

#### Обработчик запроса обратного вызова
Handle callback queries
```python
@bot.callback_query_handler(func=lambda call: True)
def test_callback(call): # <- passes a CallbackQuery type object to your function
    logger.info(call)
```

#### Обработчик запроса на отправку
Handle shipping queries
`@bot.shipping_query_handler() # <- passes a ShippingQuery type object to your function`

#### Обработчик запросов перед оформлением заказа
Handle pre checkoupt queries
`@bot.pre_checkout_query_handler() # <- passes a PreCheckoutQuery type object to your function`

#### Обработчик опроса
Handle poll updates
`@bot.poll_handler() # <- passes a Poll type object to your function`

#### Обработчик ответов на опрос
Handle poll answers
`@bot.poll_answer_handler() # <- passes a PollAnswer type object to your function`

#### Обработчик моего участника чата
Handle updates of a the bot's member status in a chat
`@bot.my_chat_member_handler() # <- passes a ChatMemberUpdated type object to your function`

#### Обработчик участников чата
Handle updates of a chat member's status in a chat
`@bot.chat_member_handler() # <- passes a ChatMemberUpdated type object to your function`
*Note: "chat_member" updates are not requested by default. If you want to allow all update types, set `allowed_updates` in `bot.polling()` / `bot.infinity_polling()` to `util.update_types`*

#### Обработчик запроса на присоединение к чату
Handle chat join requests using:
`@bot.chat_join_request_handler() # <- passes ChatInviteLink type object to your function`

### Встроенный режим

More information about [Inline mode](https://core.telegram.org/bots/inline).

#### Встроенный обработчик

Now, you can use inline_handler to get inline queries in telebot.

```python

@bot.inline_handler(lambda query: query.query == 'text')
def query_text(inline_query):
    # Query message is text
```

#### Выбранный встроенный обработчик

Use chosen_inline_handler to get chosen_inline_result in telebot. Don't forgot add the /setinlinefeedback
command for @Botfather.

More information : [collecting-feedback](https://core.telegram.org/bots/inline#collecting-feedback)

```python
@bot.chosen_inline_handler(func=lambda chosen_inline_result: True)
def test_chosen(chosen_inline_result):
    # Process all chosen_inline_result.
```

#### Ответить на встроенный запрос

```python
@bot.inline_handler(lambda query: query.query == 'text')
def query_text(inline_query):
    try:
        r = types.InlineQueryResultArticle('1', 'Result', types.InputTextMessageContent('Result message.'))
        r2 = types.InlineQueryResultArticle('2', 'Result2', types.InputTextMessageContent('Result message2.'))
        bot.answer_inline_query(inline_query.id, [r, r2])
    except Exception as e:
        print(e)

```

### Дополнительные функции API

#### Обработчики промежуточного программного обеспечения

Обработчик промежуточного программного обеспечения - это функция, которая позволяет вам изменять запросы или контекст бота по мере их передачи через Telegram боту. 
Вы можете представить промежуточное программное обеспечение как цепочку логических подключений, обрабатываемых до выполнения любых других обработчиков. 
Обработка промежуточного программного обеспечения по умолчанию отключена, включите ее, установив `api helper.ENABLE_MIDDLEWARE = True`. 

```python
apihelper.ENABLE_MIDDLEWARE = True

@bot.middleware_handler(update_types=['message'])
def modify_message(bot_instance, message):
    # modifying the message before it reaches any other handler 
    message.another_text = message.text + ':changed'

@bot.message_handler(commands=['start'])
def start(message):
    # the message is already modified when it reaches message handler
    assert message.another_text == message.text + ':changed'
```
There are other examples using middleware handler in the [examples/middleware](examples/middleware) directory.

#### Промежуточное программное обеспечение на основе классов
Существует промежуточное программное обеспечение, основанное на классах.
Базовое промежуточное программное обеспечение на основе классов выглядит следующим образом:
```python
class Middleware(BaseMiddleware):
    def __init__(self):
        self.update_types = ['message']
    def pre_process(self, message, data):
        data['foo'] = 'Hello' # just for example
        # we edited the data. now, this data is passed to handler.
        # return SkipHandler() -> this will skip handler
        # return CancelUpdate() -> this will cancel update
    def post_process(self, message, data, exception=None):
        print(data['foo'])
        if exception: # check for exception
            print(exception)
```
Промежуточное программное обеспечение на основе классов должно выполнять следующие функции: post и pre process.
Итак, как вы можете видеть, промежуточные программы на основе классов работают до и после выполнения обработчика.
Для получения дополнительной информации ознакомьтесь с разделом [examples](https://github.com/eternnoir/pyTelegramBotAPI/tree/master/examples/middleware/class_based)	
	
#### Пользовательские фильтры
Кроме того, вы можете использовать встроенные пользовательские фильтры. Или вы можете создать свой собственный фильтр.

[Пример пользовательского filter](https://github.com/eternnoir/pyTelegramBotAPI/blob/master/examples/custom_filters/general_custom_filters.py)
	
Кроме того, у нас есть примеры по ним. Проверьте эти ссылки:
	
Вы можете проверить некоторые встроенные фильтры в исходном коде [code](https://github.com/eternnoir/pyTelegramBotAPI/blob/master/telebot/custom_filters.py )
	
Пример [фильтрация по id](https://github.com/eternnoir/pyTelegramBotAPI/blob/master/examples/custom_filters/id_filter_example.py)
	
Пример [фильтрация по text](https://github.com/eternnoir/pyTelegramBotAPI/blob/master/examples/custom_filters/text_filter_example.py)
	
Если вы хотите добавить какой-либо встроенный фильтр, вы можете добавить его в custom_filters.py файл.
	
Вот пример создания filter-класса:
	
```python
class IsAdmin(telebot.custom_filters.SimpleCustomFilter):
    # Class will check whether the user is admin or creator in group or not
    key='is_chat_admin'
    @staticmethod
    def check(message: telebot.types.Message):
        return bot.get_chat_member(message.chat.id,message.from_user.id).status in ['administrator','creator']
	
# Чтобы зарегистрировать фильтр, вам нужно использовать метод add_custom_filter.
bot.add_custom_filter(IsAdmin())
	
# Теперь вы можете использовать его в обработчике.
@bot.message_handler(is_chat_admin=True)
def admin_of_group(message):
	bot.send_message(message.chat.id, 'You are admin of this group!')

```
	

#### TeleBot
```python
import telebot

TOKEN = '<token_string>'
tb = telebot.TeleBot(TOKEN)	#create a new Telegram Bot object

# При вызове этой функции TeleBot начинает опрашивать серверы Telegram на наличие новых сообщений.
# - interval: int (default 0) - Интервал между запросами на опрос
# - timeout: integer (default 20) - Тайм-аут в секундах для длительного опроса.
# - allowed_updates: List of Strings (default None) - Список типов обновлений для запроса
tb.infinity_polling(interval=0, timeout=20)

# getMe
user = tb.get_me()

# setWebhook
tb.set_webhook(url="http://example.com", certificate=open('mycert.pem'))
# unset webhook
tb.remove_webhook()

# getUpdates
updates = tb.get_updates()
# or
updates = tb.get_updates(1234,100,20) #get_Updates(offset, limit, timeout):

# sendMessage
tb.send_message(chat_id, text)

# editMessageText
tb.edit_message_text(new_text, chat_id, message_id)

# forwardMessage
tb.forward_message(to_chat_id, from_chat_id, message_id)

# Все функции send_xyz, которые могут принимать файл в качестве аргумента, также могут принимать file_id вместо файла.
# sendPhoto
photo = open('/tmp/photo.png', 'rb')
tb.send_photo(chat_id, photo)
tb.send_photo(chat_id, "FILEID")

# sendAudio
audio = open('/tmp/audio.mp3', 'rb')
tb.send_audio(chat_id, audio)
tb.send_audio(chat_id, "FILEID")

## sendAudio with duration, performer and title.
tb.send_audio(CHAT_ID, file_data, 1, 'eternnoir', 'pyTelegram')

# sendVoice
voice = open('/tmp/voice.ogg', 'rb')
tb.send_voice(chat_id, voice)
tb.send_voice(chat_id, "FILEID")

# sendDocument
doc = open('/tmp/file.txt', 'rb')
tb.send_document(chat_id, doc)
tb.send_document(chat_id, "FILEID")

# sendSticker
sti = open('/tmp/sti.webp', 'rb')
tb.send_sticker(chat_id, sti)
tb.send_sticker(chat_id, "FILEID")

# sendVideo
video = open('/tmp/video.mp4', 'rb')
tb.send_video(chat_id, video)
tb.send_video(chat_id, "FILEID")

# sendVideoNote
videonote = open('/tmp/videonote.mp4', 'rb')
tb.send_video_note(chat_id, videonote)
tb.send_video_note(chat_id, "FILEID")

# sendLocation
tb.send_location(chat_id, lat, lon)

# sendChatAction
# action_string can be one of the following strings: 'typing', 'upload_photo', 'record_video', 'upload_video',
# 'record_audio', 'upload_audio', 'upload_document' or 'find_location'.
tb.send_chat_action(chat_id, action_string)

# getFile
# Downloading a file is straightforward
# Returns a File object
import requests
file_info = tb.get_file(file_id)

file = requests.get('https://api.telegram.org/file/bot{0}/{1}'.format(API_TOKEN, file_info.file_path))


```
#### Разметка ответа
Все функции TeleBot `send_xyz` принимают необязательный аргумент `reply_markup`. Этот аргумент должен быть экземпляром `ReplyKeyboardMarkup`, `Reply Keyboard Remove` или `Force Reply`, которые определены в types.py .

```python
from telebot import types

# Используя класс ReplyKeyboardMarkup
# Его конструктор может принимать следующие необязательные аргументы:
# - resize_keyboard: True/False (default False)
# - one_time_keyboard: True/False (default False)
# - selective: True/False (default False)
# - row_width: integer (default 3)
# row_width используется в сочетании с функцией add().
# Он определяет, сколько кнопок помещается в каждой строке, прежде чем переходить к следующей строке.
markup = types.ReplyKeyboardMarkup(row_width=2)
itembtn1 = types.KeyboardButton('a')
itembtn2 = types.KeyboardButton('v')
itembtn3 = types.KeyboardButton('d')
markup.add(itembtn1, itembtn2, itembtn3)
tb.send_message(chat_id, "Choose one letter:", reply_markup=markup)

# или добавляйте кнопки клавиатуры по одной строке за раз:
markup = types.ReplyKeyboardMarkup()
itembtna = types.KeyboardButton('a')
itembtnv = types.KeyboardButton('v')
itembtnc = types.KeyboardButton('c')
itembtnd = types.KeyboardButton('d')
itembtne = types.KeyboardButton('e')
markup.row(itembtna, itembtnv)
markup.row(itembtnc, itembtnd, itembtne)
tb.send_message(chat_id, "Choose one letter:", reply_markup=markup)
```
The last example yields this result:

![ReplyKeyboardMarkup](https://farm3.staticflickr.com/2933/32418726704_9ef76093cf_o_d.jpg "ReplyKeyboardMarkup")

```python
# ReplyKeyboardRemove: скрывает ранее отправленную разметку Replykeyboard
# Принимает необязательный выборочный аргумент (True/False, default False)
markup = types.ReplyKeyboardRemove(selective=False)
tb.send_message(chat_id, message, reply_markup=markup)
```

```python
# ForceReply: заставляет пользователя ответить на сообщение
# Принимает необязательный выборочный аргумент (True/False, default False)
markup = types.ForceReply(selective=False)
tb.send_message(chat_id, `Пришли мне еще одно слово:`, reply_markup=markup)
```
ForceReply:

![ForceReply](https://farm4.staticflickr.com/3809/32418726814_d1baec0fc2_o_d.jpg "ForceReply")


### Работа с сущностями
Этот объект представляет собой одну специальную сущность в текстовом сообщении. Например, хэштеги, имена пользователей, URL-адреса и т.д.
Атрибуты:
* `type`
* `url`
* `offset`
* `length`
* `user`


**Вот пример:**`message.entities[num].<attribute>`<br>
Здесь `num` - это номер объекта или порядок расположения объектов в ответе, например, в случае, если в ответе/сообщении содержится несколько объектов.<br>
`message.entities` returns a list of entities object. <br>
`message.entities[0].type` would give the type of the first entity<br>
Refer [Bot Api](https://core.telegram.org/bots/api#messageentity) for extra details

## Расширенное использование API

### Использование локального сервера Bot API
Начиная с версии 5.0 Bot API, у вас есть возможность запускать свой собственный [локальный сервер Bot API](https://core.telegram.org/bots/api#using-a-local-bot-api-server ).
pyTelegramBotAPI также поддерживает эту функцию.
```python
from telebot import apihelper

apihelper.API_URL = "http://localhost:4200/bot{0}/{1}"
```
**Важно: Как описано [здесь](https://core.telegram.org/bots/api#logout ), вы должны выйти из своего бота с сервера Telegram, прежде чем переключаться на свой локальный API-сервер. в pyTelegramBotAPI используйте `bot.log_out()`**

*Примечание: 4200 - это пример порта*

### Асинхронный TeleBot
Новое: Существует асинхронная реализация telebot.
Чтобы включить такое поведение, создайте экземпляр AsyncTeleBot вместо TeleBot.
```python
tb = telebot.AsyncTeleBot("TOKEN")
```
Теперь каждая функция, вызывающая Telegram API, выполняется в отдельной асинхронной задаче.
Использование AsyncTeleBot позволяет вам выполнять следующие действия:
```python
import telebot

tb = telebot.AsyncTeleBot("TOKEN")

@tb.message_handler(commands=['start'])
async def start_message(message):
	await bot.send_message(message.chat.id, 'Hello!')

```

Смотрите больше в [примерах](https://github.com/eternnoir/pyTelegramBotAPI/tree/master/examples/asynchronous_telebot )

### Отправка больших текстовых сообщений
Иногда вам необходимо отправлять сообщения, объем которых превышает 5000 символов. API Telegram не может обработать такое количество символов в одном запросе, поэтому нам нужно разделить сообщение на несколько частей. Вот как это сделать с помощью API:
```python
from telebot import util
large_text = open("large_text.txt", "rb").read()

# Разделите текст на 3000 символов.
# split_string возвращает список с разделенным текстом.
splitted_text = util.split_string(large_text, 3000)

for text in splitted_text:
	tb.send_message(chat_id, text)
```

Или вы можете использовать новую функцию `smart_split`, чтобы получить более значимые подстроки:
```python
from telebot import util
large_text = open("large_text.txt", "rb").read()
# Разбивает одну строку на несколько строк с максимальным количеством `chars_per_string` (макс. 4096)
# Разбивается на последние '\n', '. ' или ' ' именно в этом приоритете.
# smart_split возвращает список с разделенным текстом.
splitted_text = util.smart_split(large_text, chars_per_string=3000)
for text in splitted_text:
	tb.send_message(chat_id, text)
```
### Управление количеством потоков, используемых TeleBot
Конструктор TeleBot принимает следующие необязательные аргументы:

 - threaded: True/False (default True). Флаг, указывающий, должен ли 
   TeleBot выполнять обработчики сообщений в своем потоке опроса.

### Механизм прослушивания
В качестве альтернативы обработчикам сообщений можно также зарегистрировать функцию в качестве прослушивателя TeleBot.

ОБРАТИТЕ внимание: обработчики не исчезнут! Ваше сообщение будет обработано как обработчиками, так и слушателями. Кроме того, невозможно предсказать, что сработает сначала из-за многопоточности. Если вы используете threaded=False, пользовательские прослушиватели будут работать раньше, после них будут вызваны обработчики.
Пример:
```python
def handle_messages(messages):
	for message in messages:
		# Сделайте что-нибудь с сообщением
		bot.reply_to(message, 'Hi')

bot.set_update_listener(handle_messages)
bot.infinity_polling()
```

### Использование веб-крючков
При использовании webhooks telegram отправляет одно обновление за вызов, для его обработки вы должны вызвать process_new_messages([update.message]), когда получите его.

В каталоге [examples/webhook_examples](examples/webhook_examples) есть несколько примеров использования webhooks.

### Регистрация
Вы можете использовать регистратор модуля Telebot для регистрации debuginfo о Telebot. Используйте `telebot.logger`, чтобы получить регистратор модуля TeleBot.
В регистратор можно добавить пользовательские обработчики ведения журнала. Обратитесь к [странице модуля ведения журнала Python](https://docs.python.org/2/library/logging.html ) для получения дополнительной информации.

```python
import logging

logger = telebot.logger
telebot.logger.setLevel(logging.DEBUG) # Outputs debug messages to console.
```

### Полномочие
For sync:

Вы можете использовать прокси-сервер для запроса. объект `api helper.proxy` будет использовать при вызове аргумент прокси-сервера `requests`.

```python
from telebot import apihelper

apihelper.proxy = {'http':'http://127.0.0.1:3128'}
```

If you want to use socket5 proxy you need install dependency `pip install requests[socks]` and make sure, that you have the latest version of `gunicorn`, `PySocks`, `pyTelegramBotAPI`, `requests` and `urllib3`.

```python
apihelper.proxy = {'https':'socks5://userproxy:password@proxy_address:port'}
```

For async:
```python
from telebot import asyncio_helper

asyncio_helper.proxy = 'http://127.0.0.1:3128' #url
```


### Testing
You can disable or change the interaction with real Telegram server by using
```python
apihelper.CUSTOM_REQUEST_SENDER = your_handler
```
parameter. You can pass there your own function that will be called instead of _requests.request_.

For example:
```python
def custom_sender(method, url, **kwargs):
    print("custom_sender. method: {}, url: {}, params: {}".format(method, url, kwargs.get("params")))
    result = util.CustomRequestResponse('{"ok":true,"result":{"message_id": 1, "date": 1, "chat": {"id": 1, "type": "private"}}}')
    return result
```

Then you can use API and proceed requests in your handler code.
```python
apihelper.CUSTOM_REQUEST_SENDER = custom_sender
tb = TeleBot("test")
res = tb.send_message(123, "Test")
```

Result will be:

`custom_sender. method: post, url: https://api.telegram.org/botololo/sendMessage, params: {'chat_id': '123', 'text': 'Test'}`



## Ограничения на соответствие API
* ➕ [Bot API 4.5](https://core.telegram.org/bots/api-changelog#december-31-2019) - No nested MessageEntities and Markdown2 support
* ➕ [Bot API 4.1](https://core.telegram.org/bots/api-changelog#august-27-2018)   - No Passport support
* ➕ [Bot API 4.0](https://core.telegram.org/bots/api-changelog#july-26-2018)     - No Passport support


## AsyncTeleBot
### Асинхронная версия telebot
У нас есть полностью асинхронная версия TeleBot.
Этот класс не управляется потоками. Задачи Asyncio создаются для выполнения всего этого.

### EchoBot
Echo Bot example on AsyncTeleBot:
	
```python
# This is a simple echo bot using the decorator mechanism.
# It echoes any incoming text messages.

from telebot.async_telebot import AsyncTeleBot
import asyncio
bot = AsyncTeleBot('TOKEN')



# Handle '/start' and '/help'
@bot.message_handler(commands=['help', 'start'])
async def send_welcome(message):
    await bot.reply_to(message, """\
Hi there, I am EchoBot.
I am here to echo your kind words back to you. Just say anything nice and I'll say the exact same thing to you!\
""")


# Handle all other messages with content_type 'text' (content_types defaults to ['text'])
@bot.message_handler(func=lambda message: True)
async def echo_message(message):
    await bot.reply_to(message, message.text)


asyncio.run(bot.polling())
```
As you can see here, keywords are await and async. 
	
### Why should I use async?
Asynchronous tasks depend on processor performance. Many asynchronous tasks can run parallelly, while thread tasks will block each other.

### Differences in AsyncTeleBot
AsyncTeleBot is asynchronous. It uses aiohttp instead of requests module.
	
### Examples
See more examples in our [examples](https://github.com/eternnoir/pyTelegramBotAPI/tree/master/examples/asynchronous_telebot) folder
	

## F.A.Q.

### How can I distinguish a User and a GroupChat in message.chat?
Telegram Bot API support new type Chat for message.chat.

- Check the ```type``` attribute in ```Chat``` object:
```python
if message.chat.type == "private":
    # private chat message

if message.chat.type == "group":
	# group chat message

if message.chat.type == "supergroup":
	# supergroup chat message

if message.chat.type == "channel":
	# channel message

```

### How can I handle reocurring ConnectionResetErrors?

Bot instances that were idle for a long time might be rejected by the server when sending a message due to a timeout of the last used session. Add `apihelper.SESSION_TIME_TO_LIVE = 5 * 60` to your initialisation to force recreation after 5 minutes without any activity. 

## Группа чата Telegram

Get help. Discuss. Chat.

* Присоединяйтесь к [группе Telegram-чата pyTelegramBotAPI](https://telegram.me/joinchat/Bn4ixj84FIZVkwhk2jag6A )
	
## Telegram Channel

Присоединяйтесь к [новостному каналу](https://t.me/pyTelegramBotAPI ). Здесь мы будем публиковать релизы и обновления.
	
## Еще примеры

* [Echo Bot](https://github.com/eternnoir/pyTelegramBotAPI/blob/master/examples/echo_bot.py)
* [Deep Linking](https://github.com/eternnoir/pyTelegramBotAPI/blob/master/examples/deep_linking.py)
* [next_step_handler Example](https://github.com/eternnoir/pyTelegramBotAPI/blob/master/examples/step_example.py)

## Шаблон кода
Шаблон - это готовая папка, содержащая архитектуру базового проекта.
Вот несколько примеров шаблона:
	
* [AsyncTeleBot template](https://github.com/coder2020official/asynctelebot_template)
* [TeleBot template](https://github.com/coder2020official/telebot_template)
	
	
## Боты, использующие эту библиотеку
* [SiteAlert bot](https://telegram.me/SiteAlert_bot) ([source](https://github.com/ilteoood/SiteAlert-Python)) by *ilteoood* - Отслеживает веб-сайты и отправляет уведомления об изменениях
* [TelegramLoggingBot](https://github.com/aRandomStranger/TelegramLoggingBot) by *aRandomStranger*
* [Telegram LMGTFY_bot](https://github.com/GabrielRF/telegram-lmgtfy_bot) by *GabrielRF* - Позвольте мне погуглить это для вас.
* [Telegram Proxy Bot](https://github.com/mrgigabyte/proxybot) by *mrgigabyte* 
* [RadRetroRobot](https://github.com/Tronikart/RadRetroRobot) by *Tronikart* - Multifunctional Telegram Bot RadRetroRobot.
* [League of Legends bot](https://telegram.me/League_of_Legends_bot) ([source](https://github.com/i32ropie/lol)) by *i32ropie*
* [NeoBot](https://github.com/neoranger/NeoBot) by [@NeoRanger](https://github.com/neoranger)
* [ColorCodeBot](https://t.me/colorcodebot) ([source](https://github.com/andydecleyre/colorcodebot)) - Share code snippets as beautifully syntax-highlighted HTML and/or images.
* [ComedoresUGRbot](http://telegram.me/ComedoresUGRbot) ([source](https://github.com/alejandrocq/ComedoresUGRbot)) by [*alejandrocq*](https://github.com/alejandrocq) - Telegram bot to check the menu of Universidad de Granada dining hall.
* [proxybot](https://github.com/p-hash/proxybot) - Simple Proxy Bot for Telegram. by p-hash
* [DonantesMalagaBot](https://github.com/vfranch/DonantesMalagaBot) - DonantesMalagaBot facilitates information to Malaga blood donors about the places where they can donate today or in the incoming days. It also records the date of the last donation so that it helps the donors to know when they can donate again. - by vfranch
* [DuttyBot](https://github.com/DmytryiStriletskyi/DuttyBot) by *Dmytryi Striletskyi* - Timetable for one university in Kiev.
* [wat-bridge](https://github.com/rmed/wat-bridge) by [*rmed*](https://github.com/rmed) - Send and receive messages to/from WhatsApp through Telegram
* [filmratingbot](http://t.me/filmratingbot)([source](https://github.com/jcolladosp/film-rating-bot)) by [*jcolladosp*](https://github.com/jcolladosp) - Telegram bot using the Python API that gets films rating from IMDb and metacritic
* [Send2Kindlebot](http://t.me/Send2KindleBot) ([source](https://github.com/GabrielRF/Send2KindleBot)) by *GabrielRF* - Send to Kindle service.
* [RastreioBot](http://t.me/RastreioBot) ([source](https://github.com/GabrielRF/RastreioBot)) by *GabrielRF* - Bot used to track packages on the Brazilian Mail Service.
* [Spbu4UBot](http://t.me/Spbu4UBot)([link](https://github.com/EeOneDown/spbu4u)) by *EeOneDown* - Bot with timetables for SPbU students.
* [SmartySBot](http://t.me/ZDU_bot)([link](https://github.com/0xVK/SmartySBot)) by *0xVK* - Telegram timetable bot, for Zhytomyr Ivan Franko State University students.
* [LearnIt](https://t.me/LearnItbot)([link](https://github.com/tiagonapoli/LearnIt)) - A Telegram Bot created to help people to memorize other languages’ vocabulary.
* [Bot-Telegram-Shodan ](https://github.com/rubenleon/Bot-Telegram-Shodan) by [rubenleon](https://github.com/rubenleon)
* [VigoBusTelegramBot](https://t.me/vigobusbot) ([GitHub](https://github.com/Pythoneiro/VigoBus-TelegramBot)) - Bot that provides buses coming to a certain stop and their remaining time for the city of Vigo (Galicia - Spain)
* [kaishnik-bot](https://t.me/kaishnik_bot) ([source](https://github.com/airatk/kaishnik-bot)) by *airatk* - bot which shows all the necessary information to KNTRU-KAI students.
* [Robbie](https://t.me/romdeliverybot) ([source](https://github.com/FacuM/romdeliverybot_support)) by @FacuM - Support Telegram bot for developers and maintainers.
* [AsadovBot](https://t.me/asadov_bot) ([source](https://github.com/desexcile/BotApi)) by @DesExcile - Сatalog of poems by Eduard Asadov.
* [thesaurus_com_bot](https://t.me/thesaurus_com_bot) ([source](https://github.com/LeoSvalov/words-i-learn-bot)) by @LeoSvalov - words and synonyms from [dictionary.com](https://www.dictionary.com) and [thesaurus.com](https://www.thesaurus.com) in the telegram.
* [InfoBot](https://t.me/info2019_bot) ([source](https://github.com/irevenko/info-bot)) by @irevenko - An all-round bot that displays some statistics (weather, time, crypto etc...)
* [FoodBot](https://t.me/ChensonUz_bot) ([source](https://github.com/Fliego/old_restaurant_telegram_chatbot)) by @Fliego - a simple bot for food ordering
* [Sporty](https://t.me/SportydBot) ([source](https://github.com/0xnu/sporty)) by @0xnu - Telegram bot for displaying the latest news, sports schedules and injury updates.
* [JoinGroup Silencer Bot](https://t.me/joingroup_silencer_bot) ([source](https://github.com/zeph1997/Telegram-Group-Silencer-Bot)) by [@zeph1997](https://github.com/zeph1997) - A Telegram Bot to remove "join group" and "removed from group" notifications.
* [TasksListsBot](https://t.me/TasksListsBot) ([source](https://github.com/Pablo-Davila/TasksListsBot)) by [@Pablo-Davila](https://github.com/Pablo-Davila) - A (tasks) lists manager bot for Telegram.
* [MyElizaPsychologistBot](https://t.me/TasksListsBot) ([source](https://github.com/Pablo-Davila/MyElizaPsychologistBot)) by [@Pablo-Davila](https://github.com/Pablo-Davila) - An implementation of the famous Eliza psychologist chatbot.
* [Frcstbot](https://t.me/frcstbot) ([source](https://github.com/Mrsqd/frcstbot_public)) by [Mrsqd](https://github.com/Mrsqd). A Telegram bot that will always be happy to show you the weather forecast.
* [MineGramBot](https://github.com/ModischFabrications/MineGramBot) by [ModischFabrications](https://github.com/ModischFabrications). This bot can start, stop and monitor a minecraft server.
* [Tabletop DiceBot](https://github.com/dexpiper/tabletopdicebot) by [dexpiper](https://github.com/dexpiper). This bot can roll multiple dices for RPG-like games, add positive and negative modifiers and show short descriptions to the rolls.
* [BarnameKon](https://t.me/BarnameKonBot) by [Anvaari](https://github.com/anvaari). This Bot make "Add to google calendar" link for your events. It give information about event and return link. It work for Jalali calendar and in Tehran Time. [Source code](https://github.com/anvaari/BarnameKon)
* [Translator bot](https://github.com/AREEG94FAHAD/translate_text_bot) by Areeg Fahad. This bot can be used to translate texts. 
* [Digital Cryptocurrency bot](https://github.com/AREEG94FAHAD/currencies_bot) by Areeg Fahad. With this bot, you can now monitor the prices of more than 12 digital Cryptocurrency. 
* [Anti-Tracking Bot](https://t.me/AntiTrackingBot) by Leon Heess [(source)](https://github.com/leonheess/AntiTrackingBot). Send any link, and the bot tries its best to remove all tracking from the link you sent.
* [Developer Bot](https://t.me/IndDeveloper_bot) by [Vishal Singh](https://github.com/vishal2376) [(source code)](https://github.com/vishal2376/telegram-bot) This telegram bot can do tasks like GitHub search & clone,provide c++ learning resources ,Stackoverflow search, Codeforces(profile visualizer,random problems)
* [oneIPO bot](https://github.com/aaditya2200/IPO-proj) by [Aadithya](https://github.com/aaditya2200) & [Amol Soans](https://github.com/AmolDerickSoans) This Telegram bot provides live updates , data and documents on current and upcoming IPOs(Initial Public Offerings) 
* [CoronaGraphsBot](https://t.me/CovidGraph_bot) ([source](https://github.com/TrevorWinstral/CoronaGraphsBot)) by *TrevorWinstral* - Gets live COVID Country data, plots it, and briefs the user
* [ETHLectureBot](https://t.me/ETHLectureBot) ([source](https://github.com/TrevorWinstral/ETHLectureBot)) by *TrevorWinstral* - Notifies ETH students when their lectures have been uploaded
* [Vlun Finder Bot](https://github.com/resinprotein2333/Vlun-Finder-bot) by [Resinprotein2333](https://github.com/resinprotein2333). This bot can help you to find The information of CVE vulnerabilities.
* [ETHGasFeeTrackerBot](https://t.me/ETHGasFeeTrackerBot) ([Source](https://github.com/DevAdvik/ETHGasFeeTrackerBot]) by *DevAdvik* - Get Live Ethereum Gas Fees in GWEI
* [Google Sheet Bot](https://github.com/JoachimStanislaus/Tele_Sheet_bot) by [JoachimStanislaus](https://github.com/JoachimStanislaus). This bot can help you to track your expenses by uploading your bot entries to your google sheet.
* [GrandQuiz Bot](https://github.com/Carlosma7/TFM-GrandQuiz) by [Carlosma7](https://github.com/Carlosma7). This bot is a trivia game that allows you to play with people from different ages. This project addresses the use of a system through chatbots to carry out a social and intergenerational game as an alternative to traditional game development.
* [Diccionario de la RAE](https://t.me/dleraebot) ([source](https://github.com/studentenherz/dleraebot)) This bot lets you find difinitions of words in Spanish using [RAE's dictionary](https://dle.rae.es/). It features direct message and inline search.
* [remoteTelegramShell](https://github.com/EnriqueMoran/remoteTelegramShell) by [EnriqueMoran](https://github.com/EnriqueMoran). Control your LinuxOS computer through Telegram.
* [Commerce Telegram Bot](https://github.com/ayitinya/commerce-telegram-bot/). Make purchases of items in a store with an Admin panel for data control and notifications.
* [Pyfram-telegram-bot](https://github.com/skelly37/pyfram-telegram-bot) Query wolframalpha.com and make use of its API through Telegram.
* [TranslateThisVideoBot](https://gitlab.com/WuerfelDev/translatethisvideo) This Bot can understand spoken text in videos and translate it to English
* [Zyprexa](https://t.me/mathemathicsBot) ([source](https://github.com/atif5/zyprexa)) Zyprexa can solve, help you solve any mathematical problem you encounter and convert your regular mathematical expressions into beautiful imagery using LaTeX.
* [Bincode-telegram-bot](https://github.com/tusharhero/bincode-telegram-bot) by [tusharhero](https://github.com/tusharhero) - Makes [bincodes](https://github.com/tusharhero/bincode) from text provides and also converts them back to text.
* [hydrolib_bot](https://github.com/Mayson90/hydrolib_bot) Toolset for Hydrophilia tabletop game (game cards, rules, structure...).
* [Gugumoe-bot](http://t.me/gugumoe_bot) ([source](https://github.com/GooGuJiang/Gugumoe-bot)) by [咕谷酱](https://gmoe.cc) GuXiaoJiang is a multi-functional robot, such as OSU game information query, IP test, animation screenshot search and other functions.
* [Feedback-bot](https://github.com/coder2020official/feedbackbot) A feedback bot for user-admin communication. Made on AsyncTeleBot, using [template](https://github.com/coder2020official/asynctelebot_template).
* [TeleServ](https://github.com/ablakely/TeleServ) by [ablakely](https://github.com/ablakely) Это мост Telegram к IRC, который подключается как IRC-сервер и позволяет пользователям Telegram выглядеть как обычные пользователи IRC.
* [Simple Store Bot](https://github.com/AntonGlyzin/myshopbot) by [Anton Glyzin](https://github.com/AntonGlyzin) Это простой telegram-магазин с админ-панелью. Разработан в соответствии с шаблоном.
* [Media Rating Bot](https://t.me/mediaratingbot) ([source](https://github.com/CommanderCRM/MediaRatingBot))by [CommanderCRM](https://github.com/CommanderCRM). This bot aggregates media (movies, TV series, etc.) ratings from IMDb, Rotten Tomatoes, Metacritic, TheMovieDB, FilmAffinity and also provides number of votes of said media on IMDb. 
* [Spot Seek Bot](https://t.me/SpotSeekBot) ([source](https://github.com/arashnm80/spot-seek-bot)) by [Arashnm80](https://github.com/arashnm80). This is a free & open source telegram bot for downloading tracks, albums or playlists from spotify.
* [CalendarIT Bot](https://t.me/calendarit_bot) ([source](https://github.com/codebyzen/CalendarIT_Telegram_Bot))by [CodeByZen](https://github.com/codebyzen). A simple, but extensible Python Telegram bot, can post acquainted with what is happening today, tomorrow or what happened 20 years ago to channel. 
* [DownloadMusicBOT](https://github.com/fcoagz/DownloadMusicBOT) by *Francisco Griman* - It is a simple bot that downloads audio from YouTube videos on Telegram.

**Хотите, чтобы ваш бот был указан здесь? Просто сделайте запрос на извлечение. Принимаются только боты с общедоступным исходным кодом.**
