- 👋 Hi, I’m @Linorine and this my first project on Python
- 🌱 I’m currently learning Python
- 📫 How to reach me cughtajm@gmail.com

<!---
Linorine/Linorine is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
import speech_recognition as sr
from gtts import gTTS
import random
import time
import playsound
from datetime import datetime
from datetime import datetime as dt
import requests

# скриптовые фразы
opts = {
    'call': ('ленонрин', 'рин', 'привет рин', 'рин привет',
             'привет ленорин', 'ленорин привет', 'лейнорин', 'лейн', 'привет'),
    'goodbye_rin': ('прощай рин', 'пока рин', 'досвидания рин', 'спокойной ночи рин',
                    'прощай', 'пока', 'досвидания', 'спокойной ночи')
}
script_command = {
        'time': ('времяни', 'время', 'который час', 'сколько сейчас времени', 'сейчас времяни', 'сколько времени'),
        'date': ('число', 'дата', 'какое сегодня число', 'какое сегодня число', 'какая сегодня дата', 'скажи дату'),
        'weather': ('какая сейчас погода', 'скажи погоду',
                    'сколько сейчас градусов', 'как за окном', 'как на улице', 'а что по погоде')
}

# функции


def listen_command():
    r = sr.Recognizer()
    with sr.Microphone() as source:
        audio = r.listen(source)
    try:
        our_speech = r.recognize_google(audio, language='ru-RU')
        print("Вы сказали: "+our_speech)
        return our_speech
    except sr.UnknownValueError:
        return "Error"
    except sr.RequestError:
        return "Error"


def do_command(message):
    message = message.lower()
    if message in opts['call']:
        say_message("Здраствуйте")  # костыль, который помогает gTTs не съедать первую букву во время произношения
    elif message in script_command['date']:
        now = datetime.now().strftime("%d-%m-%Y")  # день, месяц, год
        say_message(f'Сегодня: {get_date(now)}')
    elif message in script_command['time']:
        say_message(f'{declension_time(dt.now())}')
    elif message in script_command['weather']:
        real, feels_like = get_weather()
        say_message(f'Сейчас в городе Москва {real}')
        say_message(f'Ощущается как {feels_like}')
    elif message == 'а как у тебя дела':
        say_message('К сожаленю мой создатель не смог добавить мне искусственный' +
                    ' интеллект, чтоб я могла поддерживать такие разговоры')
        say_message('думаю со времинем он сможет это сделать')

    elif message in opts['goodbye_rin']:
        say_message('Досвидания')
        exit()
    else:
        print('Дайте команду')


def say_message(message):
    pass
    voice = gTTS(message, lang="ru")
    file_voice = "_audio_" + str(time.time()) + "_" + str(random.randint(0, 100_000))+".mp3"
    voice.save(file_voice)
    playsound.playsound(file_voice)
    print("Рин: " + message)

def get_time(n, form_0, form_1, form_2):
    units = n % 10
    tens = (n // 10) % 10
    if tens == 1:
        return form_0
    if units in [0, 5, 6, 7, 8, 9]:
        return form_0
    if units == 1:
        return form_1
    if units in [2, 3, 4]:
        return form_2


def declension_time(t):
    hours = get_time(t.hour, 'часов', 'час', 'часа')
    minutes = get_time(t.minute, 'минут', 'минута', 'минуты')
    return f'{t.hour} {hours} {t.minute} {minutes}'


def get_date(date, split='-'):
    day_list = ['первое', 'второе', 'третье', 'четвёртое',
                'пятое', 'шестое', 'седьмое', 'восьмое',
                'девятое', 'десятое', 'одиннадцатое', 'двенадцатое',
                'тринадцатое', 'четырнадцатое', 'пятнадцатое', 'шестнадцатое',
                'семнадцатое', 'восемнадцатое', 'девятнадцатое', 'двадцатое',
                'двадцать первое', 'двадцать второе', 'двадцать третье',
                'двадацать четвёртое', 'двадцать пятое', 'двадцать шестое',
                'двадцать седьмое', 'двадцать восьмое', 'двадцать девятое',
                'тридцатое', 'тридцать первое']
    month_list = ['января', 'февраля', 'марта', 'апреля', 'мая', 'июня',
                  'июля', 'августа', 'сентября', 'октября', 'ноября', 'декабря']
    date_list = date.split(split)
    return day_list[int(date_list[0]) - 1] + ' ' + month_list[int(date_list[1]) - 1] + ' ' + date_list[2] + ' года'


def get_weather():
    url = ('https://api.openweathermap.org/data/2.5/weather?q=Москва&units' +
           '=metric&lang=ru&appid=79d1ca96933b0328e1c7e3e7a26cb347')
    weather_c = requests.get(url).json()
    temperature = round(weather_c['main']['temp'])
    temperature_feels_like = round(weather_c['main']['feels_like'])
    return temperature, temperature_feels_like


if __name__ == '__main__':
    while True:
        command = listen_command()
        do_command(command)
