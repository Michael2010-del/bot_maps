# Map-Bot (Бот для отображения городов на карте)
# Telegram Bot • PyTelebotAPI • SQLite3 • Cartopy • Matplotlib
# Версия: 1.0.0 | Статус: Активен

## 📖 О проекте

Telegram-бот, который позволяет пользователям просматривать города на географической карте. Бот помогает находить города на карте мира, сохранять их в личную коллекцию и просматривать все сохраненные города на одной карте. Проект демонстрирует работу с геоданными, базами данных и географической визуализацией.

## ✨ Основные возможности

- 🗺️ Показ городов - отображение указанного города на карте мира
- ⭐ Личная коллекция - сохранение городов в избранное
- 📋 Список сохраненных - просмотр всех сохраненных городов на карте
- 📍 Географические координаты - работа с широтой и долготой городов
- 📊 Картографическая визуализация - создание карт с маркерами городов

## 🏗️ Архитектура проекта

**Структура файлов:**
```
├── 📄 bot.py           # Основной файл (обработчики команд, логика)
├── 📄 logic.py         # Логика работы с картами и БД
├── 📄 config.py        # Конфигурация (токен бота, путь к БД)
├── 📄 README.md        # Документация
│
├── 📄 database.db      # База данных с городами
│
└── 📁 venv/            # Виртуальное окружение
```

**База данных SQLite (database.db):**
```
┌─────────────────┐     ┌─────────────────┐
│     cities      │     │  users_cities   │
├─────────────────┤     ├─────────────────┤
│ id (PK)         │     │ user_id (PK)    │
│ city            │     │ city_id (PK)    │
│ lat (широта)    │     └─────────────────┘
│ lng (долгота)   │
└─────────────────┘
```

## 🤖 Команды бота

| Команда | Описание | Пример |
|---------|----------|--------|
| `/start` | Начало работы, приветствие | `/start` |
| `/help` | Показать список команд | `/help` |
| `/show_city <название>` | Показать город на карте | `/show_city London` |
| `/remember_city <название>` | Сохранить город в избранное | `/remember_city Paris` |
| `/show_my_cities` | Показать все сохраненные города | `/show_my_cities` |

## 🗺️ Процесс работы

```
1. 👤 Пользователь → /start
   🤖 Бот: Приветствие и инструкции

2. 👤 Пользователь → /show_city Moscow
   🤖 Бот: Отправляет карту с отмеченной Москвой

3. 👤 Пользователь → /remember_city Moscow
   🤖 Бот: Город Moscow успешно сохранен!

4. 👤 Пользователь → /show_my_cities
   🤖 Бот: Отправляет карту со всеми сохраненными городами
```

## 🚀 Установка и запуск

**Требования:**
- Python 3.7+
- Telegram Bot Token (получить у @BotFather)
- Файл базы данных database.db с городами

**Шаги установки:**

```bash
# 1. Клонирование репозитория
git clone https://github.com/yourusername/map-bot.git
cd map-bot

# 2. Создание виртуального окружения
python -m venv venv

# 3. Активация окружения
# Windows:
venv\Scripts\activate
# Linux/Mac:
source venv/bin/activate

# 4. Установка зависимостей
pip install pyTelegramBotAPI matplotlib cartopy

# 5. Настройка конфигурации
# Отредактируйте config.py:
TOKEN = "ВАШ_ТОКЕН_БОТА"
DATABASE = "database.db"

# 6. Инициализация базы данных
python logic.py

# 7. Запуск бота
python bot.py
```

## 📦 Зависимости

```txt
# requirements.txt
pyTelegramBotAPI==4.14.0  # Работа с Telegram API
matplotlib==3.7.1         # Построение графиков и карт
cartopy==0.22.0           # Географические проекции и карты
```

## 🔧 Технические детали

**Инициализация базы данных:**
```python
def create_user_table(self):
    conn = sqlite3.connect(self.database)
    with conn:
        conn.execute('''CREATE TABLE IF NOT EXISTS users_cities (
                            user_id INTEGER,
                            city_id TEXT,
                            FOREIGN KEY(city_id) REFERENCES cities(id)
                        )''')
        conn.commit()
```

**Добавление города в избранное:**
```python
def add_city(self, user_id, city_name):
    conn = sqlite3.connect(self.database)
    with conn:
        cursor = conn.cursor()
        cursor.execute("SELECT id FROM cities WHERE city=?", (city_name,))
        city_data = cursor.fetchone()
        if city_data:
            city_id = city_data[0]
            conn.execute('INSERT INTO users_cities VALUES (?, ?)', 
                        (user_id, city_id))
            return 1
        return 0
```

**Создание карты:**
```python
def create_graph(self, path, cities):
    ax = plt.axes(projection=ccrs.PlateCarree())
    ax.stock_img()
    for city in cities:
        coordinates = self.get_coordinates(city)
        if coordinates:
            lat, lng = coordinates
            plt.plot([lng], [lat], color='r', linewidth=1, 
                    marker='.', transform=ccrs.Geodetic())
            plt.text(lng + 3, lat + 12, city,
                    horizontalalignment='left', 
                    transform=ccrs.Geodetic())
    plt.savefig(path)
    plt.close()
```

## 🗄️ Функции работы с данными

| Функция | Описание |
|---------|----------|
| `create_user_table()` | Создание таблицы избранных городов |
| `add_city()` | Добавление города в избранное пользователя |
| `select_cities()` | Получение списка избранных городов пользователя |
| `get_coordinates()` | Получение координат города из БД |
| `create_graph()` | Создание карты с отмеченными городами |
| `draw_distance()` | Отрисовка линии между двумя городами |

## 🎯 Примеры использования

**Показ города:**
```
👤 Пользователь: /show_city Tokyo
🤖 Бот: *отправляет карту с отмеченным Токио*
```

**Сохранение города:**
```
👤 Пользователь: /remember_city Berlin
🤖 Бот: Город Berlin успешно сохранен!
```

**Показ избранных городов:**
```
👤 Пользователь: /show_my_cities
🤖 Бот: *отправляет карту со всеми сохраненными городами*
```

## 🚧 Возможные улучшения

- 🌍 Расчет и отображение расстояния между городами
- 🎨 Различные цвета и маркеры для городов
- 📍 Добавление городов по координатам
- 🗺️ Выбор разных стилей карт
- 📊 Статистика сохраненных городов
- 🔍 Нечеткий поиск по названию
- 📱 Инлайн-режим для работы в чатах
- 🌐 Поддержка нескольких языков

## 📄 Конфигурация

```python
# config.py
DATABASE = 'database.db'  # Путь к файлу базы данных
TOKEN = 'ваш_токен_здесь'  # Токен от @BotFather
```

## 💡 Дополнительные рекомендации

**Для production-версии:**

```python
# Добавьте логирование
import logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    filename='bot.log'
)

# Используйте переменные окружения
import os
TOKEN = os.getenv('BOT_TOKEN', config.TOKEN)
DATABASE = os.getenv('DATABASE_PATH', config.DATABASE)
```

**Для улучшения производительности:**

```python
# Добавьте кэширование координат
from functools import lru_cache

@lru_cache(maxsize=100)
def get_coordinates_cached(city_name):
    return self.get_coordinates(city_name)
```

---

Сделано с ❤️ для любителей географии и путешествий

⭐ Не забудьте поставить звезду, если проект полезен! ⭐
