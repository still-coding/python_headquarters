[[09_0_intro|← 📦 Модули и пакеты]] | [[09_2_how_import_works|🔍 Как работает импорт →]]

---
# 📦 Модули и импорт
```toc
minLevel: 2
```

## Что такое модуль?

**Модуль** — это просто файл с расширением `.py`, содержащий код Python. Модули позволяют разбивать программу на логические части и переиспользовать код.

```python
# math_utils.py — это модуль
def add(a, b):
    return a + b

def multiply(a, b):
    return a * b

PI = 3.14159
```

## Импорт модулей

### `import` — импорт целого модуля

```python
import math

math.sqrt(16)      # 4.0
math.pi            # 3.141592653589793
math.factorial(5)  # 120
```

### `from ... import` — импорт конкретных имён

```python
from math import sqrt, pi

sqrt(16)   # 4.0 — без префикса math.
pi         # 3.141592653589793
```

### `from ... import *` — импорт всего

```python
from math import *   # Импортирует все имена из модуля

sqrt(16)   # 4.0
sin(pi)    # ~0.0

# ⚠️ Не рекомендуется! Засоряет пространство имён
# и непонятно, откуда взялась функция
```

### `import ... as` — псевдоним

```python
import datetime as dt

now = dt.datetime.now()
print(now)

from collections import defaultdict as dd

counts = dd(int)
```

## Стандартная библиотека — полезные модули

Python поставляется с богатой стандартной библиотекой. Вот самые полезные модули для начинающих:

### `math` — математика

```python
import math

math.sqrt(25)        # 5.0 — квадратный корень
math.ceil(3.2)       # 4 — округление вверх
math.floor(3.8)      # 3 — округление вниз
math.pi              # 3.141592653589793
math.e               # 2.718281828459045
math.log(100, 10)    # 2.0 — логарифм
```

### `random` — случайные числа

```python
import random

random.randint(1, 10)          # Случайное целое от 1 до 10 включительно
random.random()                # Случайное float от 0.0 до 1.0
random.choice(["a", "b", "c"]) # Случайный элемент из списка
random.shuffle(my_list)        # Перемешивает список на месте
random.sample(range(100), 5)   # 5 случайных уникальных чисел из 0-99
```

### `datetime` — дата и время

```python
from datetime import datetime, date, timedelta

# Текущая дата и время
now = datetime.now()
print(now)                     # 2025-03-29 14:30:00.123456

# Текущая дата
today = date.today()
print(today)                   # 2025-03-29

# Форматирование
print(now.strftime("%d.%m.%Y %H:%M"))  # 29.03.2025 14:30

# Арифметика с датами
tomorrow = today + timedelta(days=1)
week_ago = today - timedelta(weeks=1)
```

### `os` — операционная система

```python
import os

os.getcwd()                    # Текущая рабочая директория
os.listdir(".")                # Список файлов в директории
os.path.exists("file.txt")     # Существует ли файл
os.path.join("data", "file.txt")  # Корректное соединение пути
os.makedirs("a/b/c", exist_ok=True)  # Создать директории
```

> 💡 Для работы с путями лучше использовать [[05_2_paths|pathlib]] — более современный подход.

### `json` — работа с JSON

```python
import json

# Из Python в JSON (сериализация)
data = {"name": "Иван", "age": 25, "hobbies": ["код", "музыка"]}
json_string = json.dumps(data, ensure_ascii=False, indent=2)
print(json_string)

# Из JSON в Python (десериализация)
parsed = json.loads('{"name": "Иван", "age": 25}')
print(parsed["name"])  # Иван

# Запись в файл
with open("data.json", "w", encoding="utf-8") as f:
    json.dump(data, f, ensure_ascii=False, indent=2)

# Чтение из файла
with open("data.json", "r", encoding="utf-8") as f:
    loaded = json.load(f)
```

## Создание своих модулей

### Простой модуль

```python
# utils.py
def greet(name):
    """Приветствует пользователя"""
    return f"Привет, {name}!"

def is_even(n):
    """Проверяет, чётное ли число"""
    return n % 2 == 0

VERSION = "1.0"
```

```python
# main.py (в той же папке)
import utils

print(utils.greet("Иван"))     # Привет, Иван!
print(utils.is_even(4))        # True
print(utils.VERSION)            # 1.0

# Или импортируем конкретные функции
from utils import greet, is_even
print(greet("Анна"))
```

### Защита от случайного выполнения

```python
# my_module.py
def main():
    """Основная логика модуля"""
    print("Запущено как программа")

# Этот блок выполнится только при прямом запуске:
#   python my_module.py        → выполнится
#   import my_module           → НЕ выполнится
if __name__ == "__main__":
    main()
```

> 💡 `__name__` — специальная переменная. Если модуль запущен напрямую, она равна `"__main__"`. Если импортирован — содержит имя модуля. Подробнее — в файле [[09_5_dunder_name|`__name__ == "__main__"` и `python -m`]].

## 🔎 Проверка на ходу

```python
# Как узнать, что есть в модуле?
>>> import math
>>> dir(math)    # Список всех имён в модуле
>>> help(math.sqrt)  # Документация по конкретной функции

# Где находится модуль?
>>> import os
>>> os.__file__
'/usr/lib/python3.13/os.py'
```

## 💡 Главное

```python
# Импорт модуля целиком
import math
math.sqrt(16)

# Импорт конкретных имён
from math import sqrt, pi

# Псевдоним
import datetime as dt

# Не используй from module import * — неявно и опасно

# Свой модуль — просто .py файл
# if __name__ == "__main__": — защита от случайного выполнения

# Полезные стандартные модули:
# math, random, datetime, os, json, pathlib
```

---

[[09_0_intro|← 📦 Модули и пакеты]] | [[09_2_how_import_works|🔍 Как работает импорт →]]
