[[03_2_3_none|← 🚫 None]] | [[03_2_5_str_methods|🔤 Методы строк →]]

---
# 🔤 Строки
```toc
minLevel: 2
```
Строка — это последовательность символов, но отдельного символьного типа в Python нет.

```python
>>> "spam"[0]  
's'  
>>> "spam"[0][0]  
's'  
>>> type("spam"[0][0][0])  
<class 'str'>
```

## Создание строк

```python
name = "Питон"
type(name)  # <class 'str'>
also_name = 'Питон'  # одинарные и двойные кавычки равнозначны
multiline = """Многострочный
текст можно писать
используя тройные кавычки"""
also_multiline = '''Или
так'''
```

## Операции со строками

```python
# Конкатенация (сложение строк)
"Hello, " + "world!"  # "Hello, world!"

# Повторение
"Python " * 3  # "Python Python Python "

# Длина строки
len("Python")  # 6

# Индексация (начинается с 0)
text = "Python"
text[0]    # 'P'
text[1]    # 'y'
text[-1]   # 'n' (индексация с конца)

# Срезы [начало:конец:шаг]
text[1:4]   # 'yth' (символы с индексами 1, 2, 3)
text[:3]    # 'Pyt' (с начала до индекса 2)
text[2:]    # 'thon' (с индекса 2 до конца)
text[::2]   # 'Pto' (каждый второй символ)
text[::-1]  # 'nohtyP' (строка в обратном порядке)
```

## Основные методы строк

```python
s = "  Python это круто!  "

# Убираем пробелы
s.strip()      # "Python это круто!"
s.lstrip()     # "Python это круто!  "
s.rstrip()     # "  Python это круто!"

# Замена
s.replace("Python", "С")  # "  С это круто!  "

# Разделение и объединение
"1,2,3,4".split(",")     # ['1', '2', '3', '4']

" | ".join(["A", "B", "C"])  # "A | B | C"

# Регистр
"python".upper()      # "PYTHON"
"PYTHON".lower()      # "python"
"python".capitalize() # "Python"

# Проверки
"python".startswith("py")   # True
"python".endswith("on")     # True
"123".isdigit()             # True
"Python".isalpha()          # True
```

Выполни, чтобы посмотреть, какие ещё есть методы у строк:
```python
help(str)
```

## f-строки

```python
name = "Иван"
age = 25
f"Привет, меня зовут {name} и мне {age} лет!"  # "Привет, меня зовут Иван и мне 25 лет!"

# Форматирование чисел
pi = 3.14159
f"Число π ≈ {pi:.2f}"  # "Число π ≈ 3.14"
```

> Больше про форматирование:
> [Форматирование строк в Python](https://habr.com/ru/articles/830282/)

## 🔤 Escape-последовательности в строках

Escape-последовательности позволяют включать в строки специальные символы, которые нельзя напечатать обычным способом.

### Основные escape-последовательности

```python
# Перевод строки
print("Первая строка\nВторая строка")
# Первая строка
# Вторая строка

# Табуляция
print("Имя:\tИван\nВозраст:\t25")
# Имя:	Иван
# Возраст:	25

# Кавычки внутри строк
print("Он сказал: \"Привет!\"")          # Он сказал: "Привет!"
print('Она ответила: \'Как дела?\'')     # Она ответила: 'Как дела?'

# Обратный слэш
print("Путь к файлу: C:\\Users\\Ivan")   # Путь к файлу: C:\Users\Ivan

# Возврат каретки (редко используется)
print("Загрузка...\rГотово!")            # Готово! (перезаписывает строку)

# Звуковой сигнал (работает в некоторых терминалах)
print("Ошибка!\a")

# Забой (удаляет предыдущий символ)
print("Опечатk\bа")                     # Опечата

# Нулевой символ
print("До нуля\x00После нуля")          # До нуляПосле нуля

# Unicode символы
print("Сердечко: \u2764")               # Сердечко: ❤
print("Смайл: \U0001F600")              # Смайл: 😀
```

### Таблица escape-последовательностей

|Последовательность|Описание|Пример|
|---|---|---|
|`\\`|Обратный слэш|`"C:\\path"`|
|`\'`|Одинарная кавычка|`'Don\'t'`|
|`\"`|Двойная кавычка|`"Он сказал: \"Да\""`|
|`\n`|Новая строка|`"строка1\nстрока2"`|
|`\r`|Возврат каретки|`"loading\rdone"`|
|`\t`|Табуляция|`"имя\tзначение"`|
|`\b`|Забой|`"test\b123"`|
|`\f`|Перевод страницы|`"page1\fpage2"`|
|`\v`|Вертикальная табуляция|`"line1\vline2"`|
|`\a`|Звуковой сигнал|`"error\a"`|
|`\0`|Нулевой символ|`"text\0"`|
|`\xHH`|Символ по hex-коду|`"\x41"` → `"A"`|
|`\uHHHH`|Unicode (4 цифры)|`"\u0041"` → `"A"`|
|`\UHHHHHHHH`|Unicode (8 цифр)|`"\U00000041"` → `"A"`|

### Практические примеры

```python
# Форматирование таблиц
header = "Товар\t\tЦена\t\tКоличество"
row1 = "Яблоки\t\t50 руб\t\t10 кг"
row2 = "Бананы\t\t80 руб\t\t5 кг"
table = f"{header}\n{'-' * 30}\n{row1}\n{row2}"
print(table)

# Пути к файлам (Windows)
path = "C:\\Users\\Ivan\\Documents\\file.txt"
print(f"Файл находится: {path}")

# Многострочные сообщения об ошибках
error_msg = "ОШИБКА:\n\tНе удалось подключиться к серверу\n\tПроверьте интернет-соединение"
print(error_msg)

# Работа с CSV данными
csv_line = "Иван,25,\"Программист, Senior\",Москва"
print(f"CSV строка: {csv_line}")

# Эмодзи и специальные символы
symbols = "Статус: ✓ Готово | ✗ Ошибка | ⚠ Внимание"
print(symbols)  # Статус: ✓ Готово | ✗ Ошибка | ⚠ Внимание
```

## 🔍 Сырые строки (Raw Strings)

Сырые строки позволяют игнорировать escape-последовательности, что особенно полезно для регулярных выражений и путей к файлам.

### Синтаксис сырых строк

```python
# Обычная строка
regular = "C:\\Users\\Ivan\\Documents"
print(regular)  # C:\Users\Ivan\Documents

# Сырая строка (префикс r или R)
raw = r"C:\Users\Ivan\Documents"
print(raw)      # C:\Users\Ivan\Documents

# Разница становится заметна при сложных путях
complex_path = r"C:\new_folder\text_files\data.txt"
# Без r нужно было бы: "C:\\new_folder\\text_files\\data.txt"
```

### Когда использовать сырые строки

```python
# ✅ Регулярные выражения
import re

# Без сырой строки (много экранирования)
pattern1 = "\\d+\\.\\d+"  # Паттерн для чисел вида 123.45

# С сырой строкой (читаемо)
pattern2 = r"\d+\.\d+"    # Тот же паттерн

print(pattern1 == pattern2)  # True

# ✅ Пути к файлам Windows
file_path = r"D:\Projects\Python\data\input.csv"

# ✅ SQL запросы
sql_query = r"""
SELECT * FROM users 
WHERE name LIKE '%\_%' 
  AND email REGEXP '\.com$'
"""

# ✅ LaTeX формулы
latex = r"\frac{d}{dx}\left( \int_{0}^{x} f(u)\,du\right) = f(x)"
```

### Ограничения сырых строк

```python
# ❌ Нельзя заканчивать на одинарный обратный слэш
# Это происходит потому, что Python всё равно обрабатывает `\"` 
# как попытку экранирования кавычки, даже в сырых строках
# invalid = r"path\"  # SyntaxError!

# ✅ Решения:
valid1 = r"path" + "\\"
valid2 = r"path\\"[:-1]  # Убираем последний символ
valid3 = "path\\"        # Используем обычную строку

# ❌ Нельзя использовать те же кавычки внутри
# invalid = r"Он сказал: "Привет""  # SyntaxError!

# ✅ Решение:
valid = r'Он сказал: "Привет"'
```

### Практические примеры

```python
# Работа с регулярными выражениями
import re

# Проверка email (сырая строка читаемее)
email_pattern = r"^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$"
email = "user@example.com"
is_valid = re.match(email_pattern, email)
print(f"Email '{email}' корректен: {bool(is_valid)}")

# Поиск телефонных номеров
phone_pattern = r"\+7\s?\(\d{3}\)\s?\d{3}-?\d{2}-?\d{2}"
text = "Мой телефон: +7 (999) 123-45-67"
phone = re.search(phone_pattern, text)
if phone:
    print(f"Найден телефон: {phone.group()}")

# Работа с путями
import os
project_path = r"C:\Users\Ivan\Desktop\PythonProject"
data_file = os.path.join(project_path, "data", "input.txt")
print(f"Полный путь к файлу: {data_file}")
```

## 📄 Многострочные строки

Многострочные строки позволяют создавать текст, который занимает несколько строк, сохраняя форматирование.

### Способы создания многострочных строк

```python
# Способ 1: Тройные кавычки
multiline1 = """Это первая строка
Это вторая строка
Это третья строка"""

# Способ 2: Тройные одинарные кавычки  
multiline2 = '''Первая строка
Вторая строка
Третья строка'''

# Способ 3: Конкатенация с \n
multiline3 = "Первая строка\nВторая строка\nТретья строка"

# Способ 4: Продолжение строки с помощью \
multiline4 = "Первая строка\n" \
             "Вторая строка\n" \
             "Третья строка"

print(multiline1)
print("=" * 20)
print(multiline3)  # Результат одинаковый
```

### Особенности тройных кавычек

```python
# Сохраняют отступы и пробелы
code_example = """def hello():
    print("Привет, мир!")
    return True
"""
print(repr(code_example))  # Видно все пробелы и \n


# Можно использовать и одинарные, и двойные кавычки внутри
mixed_quotes = """Он сказал: "Привет!"
Она ответила: 'Как дела?'
Никто не сказал: "It's working!" """

# Проблема с отступами в коде
def get_sql_query():
    query = """
        SELECT user_id, name, email
        FROM users 
        WHERE active = 1
        ORDER BY name
    """
    return query

print(repr(get_sql_query()))  # Много лишних пробелов!
```

### Решение проблемы отступов

```python
import textwrap

def get_clean_sql():
    query = """
        SELECT user_id, name, email
        FROM users 
        WHERE active = 1
        ORDER BY name
    """
    return textwrap.dedent(query).strip()

print(repr(get_clean_sql()))  # Чистый SQL без лишних отступов

# Альтернативный способ - форматирование вручную
def get_manual_sql():
    return """\
SELECT user_id, name, email
FROM users 
WHERE active = 1
ORDER BY name"""

# Использование parentheses для длинных строк
def get_parentheses_sql():
    return ("SELECT user_id, name, email "
            "FROM users "
            "WHERE active = 1 "
            "ORDER BY name")
```

### Практические применения

```python
# HTML шаблоны
def generate_html(title, content):
    return f"""<!DOCTYPE html>
<html>
<head>
    <title>{title}</title>
</head>
<body>
    <h1>{title}</h1>
    <p>{content}</p>
</body>
</html>"""

# Многострочные f-строки
name = "Иван"
age = 25
city = "Москва"

user_info = f"""
Информация о пользователе:
    Имя: {name}
    Возраст: {age} лет
    Город: {city}
    Статус: {'Совершеннолетний' if age >= 18 else 'Несовершеннолетний'}
"""

print(user_info.strip())
```

## 💡 Лучшие практики

```python
# ✅ Хорошо: Используй textwrap.dedent для отступов
import textwrap

def good_multiline():
    return textwrap.dedent("""
        SELECT name, email
        FROM users
        WHERE active = 1
    """).strip()

# ❌ Плохо: Сложно читать и поддерживать
def bad_multiline():
    return "SELECT name, email\nFROM users\nWHERE active = 1"

# ✅ Хорошо: Логическое разделение длинных строк
def good_long_string():
    return ("Это очень длинная строка, которая "
            "разделена на несколько частей "
            "для лучшей читаемости кода")

# ✅ Хорошо: Использование join для списков строк
def good_join_method():
    parts = [
        "Первая часть",
        "Вторая часть", 
        "Третья часть"
    ]
    return " ".join(parts)
```

---

[[03_2_3_none|← 🚫 None]] | [[03_2_5_str_methods|🔤 Методы строк →]]
