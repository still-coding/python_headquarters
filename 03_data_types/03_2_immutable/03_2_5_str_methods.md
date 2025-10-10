[[03_2_4_strings|← 🔤 Строки]] | [[03_2_6_type_conversion|📋 Преобразование типов →]]

---
> 💡 **Для начинающих:** это справочный материал, для того чтобы пользоваться им как документацией. Не нужно заучивать все методы, достаточно ориентироваться в них, чтобы понимать какие есть и для чего нужны.
# 🔤 Методы строк
```toc
minLevel: 2
```
Что такое методы строк?

Методы строк - это готовые функции, которые позволяют работать со строками: изменять их, проверять содержимое, искать подстроки и многое другое. Методы вызываются через точку после переменной со строкой.

```python
# Синтаксис: переменная.метод()
text = "Привет, мир!"
result = text.upper()  # Преобразуем в верхний регистр
print(result)  # ПРИВЕТ, МИР!
```

---

## 1. Методы изменения регистра

### `upper()` - Преобразование в верхний регистр

```python
str.upper() -> str
```

```python
text = "привет, мир!"
print(text.upper())  # ПРИВЕТ, МИР!

# Полезно для сравнения без учета регистра
name1 = "Анна"
name2 = "анна"
print(name1.upper() == name2.upper())  # True
```

### `lower()` - Преобразование в нижний регистр

```python
str.lower() -> str
```

```python
text = "ПРИВЕТ, МИР!"
print(text.lower())  # привет, мир!

# Пример: проверка пароля без учета регистра
# НЕ ДЕЛАЙТЕ ТАК В НАСТОЯЩЕМ КОДЕ!
password = "MyPassword123"
if password.lower() == "mypassword123":
    print("Пароль правильный!")
```

### `capitalize()` - Первая буква заглавная, остальные строчные

```python
str.capitalize() -> str
```

```python
text = "привет, мир!"
print(text.capitalize())  # Привет, мир!

# Внимание: только первая буква всей строки!
text2 = "привет мир"
print(text2.capitalize())  # Привет мир (не "Привет Мир")
```

### `title()` - Каждое слово с заглавной буквы

```python
str.title() -> str
```

```python
text = "привет, мир!"
print(text.title())  # Привет, Мир!

# Полезно для имен и заголовков
name = "иван петров"
print(name.title())  # Иван Петров
```

### `swapcase()` - Меняет регистр каждой буквы

```python
str.swapcase() -> str
```

```python
text = "Привет, Мир!"
print(text.swapcase())  # пРИВЕТ, мИР!
```

### `casefold()` - Агрессивное приведение к нижнему регистру

```python
str.casefold() -> str
```

```python
# Обычный lower()
text1 = "Привет"
print(text1.lower())  # привет

# casefold() работает лучше с некоторыми языками
text2 = "Straße"  # Немецкий язык
print(text2.lower())     # straße
print(text2.casefold())  # strasse

# Полезно для сравнения строк на разных языках
password1 = "Müller"
password2 = "müller"
print(password1.lower() == password2.lower())         # True
print(password1.casefold() == password2.casefold())   # True

# Кириллица
text3 = "МОСКВА"
print(text3.casefold())  # москва
```

---

## 2. Методы проверки содержимого

### `isdigit()` - Проверка на цифры

```python
str.isdigit() -> bool
```

```python
text1 = "123"
text2 = "123abc"
text3 = ""

print(text1.isdigit())  # True
print(text2.isdigit())  # False
print(text3.isdigit())  # False

# Практический пример: проверка ввода
user_input = input("Введи возраст: ")
if user_input.isdigit():
    age = int(user_input)
    print(f"Тебе {age} лет")
else:
    print("Пожалуйста, введи число")
```

### `isalpha()` - Проверка на буквы

```python
str.isalpha() -> bool
```

```python
text1 = "Привет"
text2 = "Привет123"
text3 = "Привет мир"  # Содержит пробел

print(text1.isalpha())  # True
print(text2.isalpha())  # False
print(text3.isalpha())  # False
```

### `isalnum()` - Проверка на буквы и цифры

```python
str.isalnum() -> bool
```

```python
text1 = "Привет123"
text2 = "Привет 123"  # Содержит пробел
text3 = "Привет!"     # Содержит символ

print(text1.isalnum())  # True
print(text2.isalnum())  # False
print(text3.isalnum())  # False
```

### `isspace()` - Проверка на пробельные символы

```python
str.isspace() -> bool
```

```python
text1 = "   "
text2 = "\t\n"  # Табуляция и перевод строки
text3 = " a "

print(text1.isspace())  # True
print(text2.isspace())  # True
print(text3.isspace())  # False
```

### `islower()` / `isupper()` - Проверка регистра

```python
str.islower() -> bool
str.isupper() -> bool
```

```python
text1 = "привет"
text2 = "ПРИВЕТ"
text3 = "Привет"

print(text1.islower())  # True
print(text2.isupper())  # True
print(text3.islower())  # False
print(text3.isupper())  # False
```

### `istitle()` - Проверка на "заголовочный" регистр

```python
str.istitle() -> bool
```

```python
text1 = "Привет Мир"
text2 = "Привет мир"
text3 = "ПРИВЕТ МИР"
text4 = "привет мир"

print(text1.istitle())  # True
print(text2.istitle())  # False
print(text3.istitle())  # False
print(text4.istitle())  # False

# Работает с каждым словом
text5 = "Это Заголовок Книги"
print(text5.istitle())  # True

# Практический пример: проверка правильности ввода имени
name = input("Введи имя: ")
if name.istitle():
    print("Имя введено правильно")
else:
    print(f"Попробуй так: {name.title()}")
```

---

## 3. Методы поиска

### `find()` - Поиск подстроки (возвращает индекс или -1)

```python
str.find(sub: str, start: int = 0, end: int = len(str)) -> int
```

```python
text = "Привет, мир! Привет, Python!"

# Поиск первого вхождения
index = text.find("Привет")
print(index)  # 0

# Поиск с позиции
index2 = text.find("Привет", 5)  # Ищем с 5-й позиции
print(index2)  # 13

# Если не найдено
index3 = text.find("Java")
print(index3)  # -1

# Практический пример
email = "user@example.com"
at_pos = email.find("@")
if at_pos != -1:
    username = email[:at_pos]
    domain = email[at_pos + 1:]
    print(f"Пользователь: {username}, Домен: {domain}")
```

### `count()` - Подсчет вхождений

```python
str.count(sub: str, start: int = 0, end: int = len(str)) -> int
```

```python
text = "Привет, мир! Привет, Python!"

count = text.count("Привет")
print(count)  # 2

# Подсчет символов
count_comma = text.count(",")
print(count_comma)  # 2

# Практический пример: анализ текста
sentence = "Это предложение содержит слово это несколько раз"
word_count = sentence.lower().count("это")
print(f"Слово 'это' встречается {word_count} раз(а)")
```

### `startswith()` - Проверка начала строки

```python
str.startswith(prefix: str | tuple[str, ...], start: int = 0, end: int = len(str)) -> bool
```

```python
text = "Привет, мир!"

print(text.startswith("Привет"))     # True
print(text.startswith("мир"))        # False
print(text.startswith("привет"))     # False (регистр важен!)

# Можно проверить несколько вариантов
print(text.startswith(("Привет", "Здравствуй")))  # True

# Практический пример: проверка типа файла
filename = "document.pdf"
if filename.startswith("document"):
    print("Это файл документа")

# Проверка протокола URL
url = "https://example.com"
if url.startswith(("http://", "https://")):
    print("Это веб-ссылка")
```

### `endswith()` - Проверка конца строки

```python
str.endswith(suffix: str | tuple[str, ...], start: int = 0, end: int = len(str)) -> bool
```

```python
text = "Привет, мир!"

print(text.endswith("мир!"))     # True
print(text.endswith("Привет"))   # False

# Проверка расширения файла
filename = "photo.jpg"
if filename.endswith((".jpg", ".jpeg", ".png")):
    print("Это изображение")

# Проверка окончания предложения
sentences = ["Как дела?", "Все хорошо.", "Отлично!"]
for sentence in sentences:
    if sentence.endswith((".", "!", "?")):
        print(f"'{sentence}' - завершенное предложение")
```

---

## 4. Методы разделения и объединения

### `split()` - Разделение строки на список

```python
str.split(sep: str | None = None, maxsplit: int = -1) -> list[str]
```

```python
# Разделение по пробельным символам ' \n\r\t\f' (по умолчанию)
text = "Привет мир Python"
words = text.split()
print(words)  # ['Привет', 'мир', 'Python']

# Разделение по определенному символу
data = "яблоко,банан,апельсин"
fruits = data.split(",")
print(fruits)  # ['яблоко', 'банан', 'апельсин']

# Ограничение количества разделений
text = "один-два-три-четыре"
parts = text.split("-", 2)  # Разделить максимум 2 раза
print(parts)  # ['один', 'два', 'три-четыре']

# Практический пример: обработка CSV
csv_line = "Иван,25,Москва"
name, age, city = csv_line.split(",")
print(f"Имя: {name}, Возраст: {age}, Город: {city}")
```

### `join()` - Объединение списка в строку

```python
str.join(iterable: Iterable[str]) -> str
```

```python
# Объединение через пробел
words = ["Привет", "мир", "Python"]
sentence = " ".join(words)
print(sentence)  # Привет мир Python

# Объединение через запятую
fruits = ["яблоко", "банан", "апельсин"]
fruit_list = ", ".join(fruits)
print(fruit_list)  # яблоко, банан, апельсин

# Объединение без разделителя
letters = ["П", "р", "и", "в", "е", "т"]
word = "".join(letters)
print(word)  # Привет

# Практический пример: создание пути к файлу
path_parts = ["home", "user", "documents", "file.txt"]
full_path = "/".join(path_parts)
print(full_path)  # home/user/documents/file.txt
```

---

## 5. Методы очистки строк

### `strip()` - Удаление пробелов с начала и конца

```python
str.strip(chars: str | None = None) -> str
```

```python
text = "   Привет, мир!   "
clean_text = text.strip()
print(f"'{clean_text}'")  # 'Привет, мир!'

# Удаление определенных символов
text2 = "...Привет, мир!..."
clean_text2 = text2.strip(".")
print(clean_text2)  # Привет, мир!

# Практический пример: очистка ввода пользователя
user_input = input("Введи имя: ").strip()
if user_input:
    print(f"Привет, {user_input}!")
else:
    print("Имя не введено")
```

### `lstrip()` - Удаление пробелов слева

```python
str.lstrip(chars: str | None = None) -> str
```

```python
text = "   Привет, мир!   "
print(f"'{text.lstrip()}'")  # 'Привет, мир!   '
```

### `rstrip()` - Удаление пробелов справа

```python
str.rstrip(chars: str | None = None) -> str
```

```python
text = "   Привет, мир!   "
print(f"'{text.rstrip()}'")  # '   Привет, мир!'
```

### `replace()` - Замена подстроки

```python
str.replace(old: str, new: str, count: int = -1) -> str
```

```python
text = "Привет, мир! Привет, Python!"

# Замена всех вхождений
new_text = text.replace("Привет", "Здравствуй")
print(new_text)  # Здравствуй, мир! Здравствуй, Python!

# Замена ограниченного количества вхождений
new_text2 = text.replace("Привет", "Здравствуй", 1)
print(new_text2)  # Здравствуй, мир! Привет, Python!

# Удаление символов (замена на пустую строку)
phone = "+7 (123) 456-78-90"
clean_phone = phone.replace(" ", "").replace("(", "").replace(")", "").replace("-", "")
print(clean_phone)  # +712345678900

# Практический пример: обработка данных
data = "Иван;25;Москва"
csv_data = data.replace(";", ",")
print(csv_data)  # Иван,25,Москва
```

---

## 6. Методы форматирования

### `format()` - Форматирование строки

```python
str.format(*args, **kwargs) -> str
```

```python
# Позиционные аргументы
template = "Меня зовут {} и мне {} лет"
result = template.format("Анна", 25)
print(result)  # Меня зовут Анна и мне 25 лет

# Именованные аргументы
template2 = "Меня зовут {name} и мне {age} лет"
result2 = template2.format(name="Анна", age=25)
print(result2)  # Меня зовут Анна и мне 25 лет

# Индексы
template3 = "Привет, {0}! Как дела, {0}?"
result3 = template3.format("Анна")
print(result3)  # Привет, Анна! Как дела, Анна?

# Форматирование чисел
price = 1234.5678
formatted = "Цена: {:.2f} руб.".format(price)
print(formatted)  # Цена: 1234.57 руб.

# Выравнивание
name = "Python"
print("Язык: '{:<10}'".format(name))  # Язык: 'Python    '
print("Язык: '{:>10}'".format(name))  # Язык: '    Python'
print("Язык: '{:^10}'".format(name))  # Язык: '  Python  '
```

### `center()` - Центрирование строки

```python
str.center(width: int, fillchar: str = ' ') -> str
```

```python
text = "Python"
print(text.center(20))        # '       Python       '
print(text.center(20, '*'))   # '*******Python*******'
```

### `ljust()` - Выравнивание по левому краю

```python
str.ljust(width: int, fillchar: str = ' ') -> str
```

```python
text = "Python"
print(text.ljust(20))        # 'Python              '
print(text.ljust(20, '-'))   # 'Python--------------'
```

### `rjust()` - Выравнивание по правому краю

```python
str.rjust(width: int, fillchar: str = ' ') -> str
```

```python
text = "Python"
print(text.rjust(20))        # '              Python'
print(text.rjust(20, '-'))   # '--------------Python'
```

### `zfill()` - Дополнение нулями слева

```python
str.zfill(width: int) -> str
```

```python
number = "42"
print(number.zfill(5))   # '00042'

# Работает с отрицательными числами
negative = "-42"
print(negative.zfill(5))  # '-0042'
```

---

## 7. Дополнительные методы

### `index()` - Поиск подстроки (выбрасывает ошибку если не найдено)

```python
str.index(sub: str, start: int = 0, end: int = len(str)) -> int
```

```python
text = "Привет, мир!"

# Найдено
index = text.index("мир")
print(index)  # 8

# Не найдено - вызовет ошибку ValueError
try:
    index = text.index("Java")
except ValueError:
    print("Подстрока не найдена")
```

### `rfind()` - Поиск последнего вхождения

```python
str.rfind(sub: str, start: int = 0, end: int = len(str)) -> int
```

```python
text = "Привет, мир! Привет, Python!"

# Поиск последнего вхождения
index = text.rfind("Привет")
print(index)  # 13 (а не 0)
```

### `splitlines()` - Разделение по переводам строк

```python
str.splitlines(keepends: bool = False) -> list[str]
```

```python
text = "Первая строка\nВторая строка\nТретья строка"
lines = text.splitlines()
print(lines)  # ['Первая строка', 'Вторая строка', 'Третья строка']

# С сохранением символов перевода строки
lines2 = text.splitlines(keepends=True)
print(lines2)  # ['Первая строка\n', 'Вторая строка\n', 'Третья строка']
```

### `partition()` - Разделение на 3 части

```python
str.partition(sep: str) -> tuple[str, str, str]
```

```python
email = "user@example.com"
username, sep, domain = email.partition("@")
print(username)  # 'user'
print(sep)       # '@'
print(domain)    # 'example.com'
```

### `expandtabs()` - Замена табуляции на пробелы

```python
str.expandtabs(tabsize: int = 8) -> str
```

```python
text = "Имя:\tИван\nВозраст:\t25"
expanded = text.expandtabs(4)
print(expanded)
# Имя:    Иван
# Возраст:    25
```

---

## 8. Практические примеры

### Пример 1: Валидация email

```python
def validate_email(email):
    """Простая проверка email"""
    email = email.strip().lower()

    if not email:
        return email, False, "Email не может быть пустым"

    if email.count("@") != 1:
        return email, False, "Email должен содержать один символ @"

    if not email.endswith((".com", ".ru", ".org")):
        return email, False, "Неподдерживаемый домен"

    return email, True, "Email корректен"

# Тест
emails = ["user@example.com", "invalid-email", "  test@gmail.com  "]
for email in emails:
    email, is_valid, message = validate_email(email)
    print(f"{email}: {message}")
```

### Пример 2: Форматирование телефона

```python
def format_phone(phone):
    """Форматирование номера телефона"""
    # Убираем все лишние символы
    digits = "".join(char for char in phone if char.isdigit())
    
    # Проверяем длину
    if len(digits) == 11 and digits.startswith("8"):
        digits = "7" + digits[1:]  # Заменяем 8 на 7
    
    if len(digits) != 11 or not digits.startswith("7"):
        return "Неверный формат номера"
    
    # Форматируем
    formatted = f"+7 ({digits[1:4]}) {digits[4:7]}-{digits[7:9]}-{digits[9:11]}"
    return formatted

# Тест
phones = ["89991234567", "+7 999 123 45 67", "8-999-123-45-67"]
for phone in phones:
    print(f"{phone} -> {format_phone(phone)}")
```

---

## Заключение

Методы строк в Python - это мощный инструмент для работы с текстом. Помни:

1. **Строки неизменяемы** - методы возвращают новую строку
2. **Методы можно объединять**: `text.strip().lower().replace(" ", "_")`
3. **Всегда проверяй результат** методов типа `find()` на -1
4. **Используй подходящие методы** для каждой задачи

Этот справочник поможет тебе быстро найти нужный метод и понять, как его использовать!

---

[[03_2_4_strings|← 🔤 Строки]] | [[03_2_6_type_conversion|📋 Преобразование типов →]]
