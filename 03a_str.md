# Методы [[03_immutable_data_types#🔤 Строки `str`|строк]] в Python

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
user_input = input("Введите возраст: ")
if user_input.isdigit():
    age = int(user_input)
    print(f"Вам {age} лет")
else:
    print("Пожалуйста, введите число")
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
name = input("Введите имя: ")
if name.istitle():
    print("Имя введено правильно")
else:
    print(f"Попробуйте так: {name.title()}")
```

### `isnumeric()` - Проверка на числовые символы

```python
str.isnumeric() -> bool
```

```python
text1 = "123"
text2 = "123.45"  # Содержит точку
text3 = "½"       # Дробь
text4 = "²"       # Степень
text5 = "Ⅴ"       # Римская цифра

print(text1.isnumeric())  # True
print(text2.isnumeric())  # False
print(text3.isnumeric())  # True
print(text4.isnumeric())  # True
print(text5.isnumeric())  # True

# Разница между isdigit() и isnumeric()
print(text1.isdigit())    # True
print(text1.isnumeric())  # True
print(text5.isdigit())      # False
print(text5.isnumeric())    # True
```

### `isdecimal()` - Проверка на десятичные цифры

```python
str.isdecimal() -> bool
```

```python
text1 = "123"
text2 = "123.45"  # Содержит точку
text3 = "²"       # Степень
text4 = "½"       # Дробь

print(text1.isdecimal())  # True
print(text2.isdecimal())  # False
print(text3.isdecimal())  # False
print(text4.isdecimal())  # False

# Сравнение методов проверки чисел
number_tests = ["123", "²", "½", "Ⅴ", "123.45"]
for test in number_tests:
    print(f"'{test}': decimal={test.isdecimal()}, digit={test.isdigit()}, numeric={test.isnumeric()}")
```

### `isprintable()` - Проверка на печатаемые символы

```python
str.isprintable() -> bool
```

```python
text1 = "Привет мир"
text2 = "Привет\nмир"    # Содержит перевод строки
text3 = "Привет\tмир"    # Содержит табуляцию
text4 = "Привет\x00мир"  # Содержит нулевой символ

print(text1.isprintable())  # True
print(text2.isprintable())  # False
print(text3.isprintable())  # False
print(text4.isprintable())  # False

# Практический пример: проверка данных для вывода
def safe_print(text):
    if text.isprintable():
        print(f"Безопасно: {text}")
    else:
        print(f"Содержит непечатаемые символы: {repr(text)}")

safe_print("Обычный текст")
safe_print("Текст с\nпереводом строки")
```

### `isascii()` - Проверка на ASCII символы

```python
str.isascii() -> bool
```

```python
text1 = "Hello World"
text2 = "Привет мир"
text3 = "Hello, мир!"
text4 = "123!@#"

print(text1.isascii())  # True
print(text2.isascii())  # False
print(text3.isascii())  # False
print(text4.isascii())  # True

# Практический пример: проверка для систем, поддерживающих только ASCII
def check_ascii_compatibility(text):
    if text.isascii():
        return text
    else:
        # Простая транслитерация для демонстрации
        return text.encode('ascii', errors='ignore').decode('ascii')

print(check_ascii_compatibility("Hello"))        # Hello
print(check_ascii_compatibility("Привет"))       # (пустая строка)
print(check_ascii_compatibility("Hello123"))     # Hello123
```

### `isidentifier()` - Проверка на корректное имя переменной

```python
str.isidentifier() -> bool
```

```python
name1 = "variable_name"
name2 = "2variable"      # Начинается с цифры
name3 = "my-variable"    # Содержит дефис
name4 = "class"          # Зарезервированное слово
name5 = "_private"       # Корректное имя

print(name1.isidentifier())  # True
print(name2.isidentifier())  # False
print(name3.isidentifier())  # False
print(name4.isidentifier())  # True (но это зарезервированное слово!)
print(name5.isidentifier())  # True

# Полная проверка с учетом зарезервированных слов
import keyword

def is_valid_variable_name(name):
    return name.isidentifier() and not keyword.iskeyword(name)

names = ["variable", "class", "def", "my_var", "2var", "valid_name"]
for name in names:
    valid = is_valid_variable_name(name)
    print(f"'{name}': {'✓' if valid else '✗'}")

# Практический пример: генератор имен переменных
def make_valid_variable_name(text):
    # Убираем пробелы и заменяем на подчеркивания
    name = text.replace(" ", "_").replace("-", "_")
    
    # Убираем недопустимые символы
    valid_chars = "".join(c for c in name if c.isalnum() or c == "_")
    
    # Если начинается с цифры, добавляем префикс
    if valid_chars and valid_chars[0].isdigit():
        valid_chars = "var_" + valid_chars
    
    # Проверяем результат
    if valid_chars.isidentifier() and not keyword.iskeyword(valid_chars):
        return valid_chars
    else:
        return "var_" + valid_chars

# Тест
test_names = ["user name", "2nd-place", "class", "my variable", "123test"]
for name in test_names:
    valid_name = make_valid_variable_name(name)
    print(f"'{name}' -> '{valid_name}'")
```

---

## 3. Методы поиска

### `find()` - Поиск подстроки (возвращает индекс или -1)

```python
str.find(sub: str, start: int = 0, end: int = len(str)) -> int
```
- **`sub`** - подстрока для поиска
- **`start`** - начальная позиция для поиска
- **`end`** - конечная позиция для поиска

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

### `index()` - Поиск подстроки (выдает ошибку, если не найдено)

```python
str.index(sub: str, start: int = 0, end: int = len(str)) -> int
```
- **`sub`** - подстрока для поиска
- **`start`** - начальная позиция для поиска
- **`end`** - конечная позиция для поиска

```python
text = "Привет, мир!"

# Если найдено
index = text.index("мир")
print(index)  # 8

# Если не найдено - будет ошибка!
try:
    index = text.index("Java")
except ValueError:
    print("Подстрока не найдена")
```

### `rfind()` / `rindex()` - Поиск справа

```python
str.rfind(sub: str, start: int = 0, end: int = len(str)) -> int

str.rindex(sub: str, start: int = 0, end: int = len(str)) -> int
```

```python
text = "Привет, мир! Привет, Python!"

# Поиск последнего вхождения
last_index = text.rfind("Привет")
print(last_index)  # 13

first_index = text.find("Привет")
print(first_index)  # 0
```

### `count()` - Подсчет вхождений

```python
str.count(sub: str, start: int = 0, end: int = len(str)) -> int
```
- **`sub`** - подстрока для поиска
- **`start`** - начальная позиция для поиска
- **`end`** - конечная позиция для поиска

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
print(f"Слово 'это' встречается {word_count} раз(a)")
```

---

## 4. Методы проверки начала и конца

### `startswith()` - Проверка начала строки

```python
str.startswith(prefix: str | tuple[str, ...], start: int = 0, end: int = len(str)) -> bool
```
- **`prefix`** - подстрока для проверки
- **`start`** - начальная позиция для проверки
- **`end`** - конечная позиция для проверки

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
- **`suffix`** - подстрока для проверки
- **`start`** - начальная позиция для проверки
- **`end`** - конечная позиция для проверки

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

## 5. Методы разделения и объединения

### `split()` - Разделение строки на список

```python
str.split(sep: str | None = None, maxsplit: int = -1) -> list[str]
```
- **`sep`** - разделитель
- **`maxsplit`** - максимальное количество разделений (-1 означает все)

```python
# Разделение по пробельным символам ' \n\r\t\f' (по умолчанию)
text = "Привет мир Python"
words = text.split()
print(words)  # ['Привет', 'мир', 'Python']

# Разделение по определенному символу
data = "яблоко,банан,апельсин"
fruits = data.split(",")
print(fruits)  # ['яблоко', 'банан', 'апельсин']

# Или по нескольким
data = "яблоко-|-банан-|-апельсин"
fruits = data.split("-|-")
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

### `rsplit()` - Разделение справа

```python
str.rsplit(sep: str | None = None, maxsplit: int = -1) -> list[str]
```
- **`sep`** - разделитель
- **`maxsplit`** - максимальное количество разделений (-1 означает все)

```python
path = "/home/user/documents/file.txt"
# Разделение слева
parts1 = path.split("/", 2)
print(parts1)  # ['', 'home', 'user/documents/file.txt']

# Разделение справа
parts2 = path.rsplit("/", 2)
print(parts2)  # ['/home/user', 'documents', 'file.txt']
```

### `splitlines()` - Разделение по строкам

```python
str.splitlines(keepends: bool = False) -> list[str]
```
- **`keepends`** - сохранять ли символы перевода строки

```python
text = """Первая строка
Вторая строка
Третья строка"""

lines = text.splitlines()
print(lines)  # ['Первая строка', 'Вторая строка', 'Третья строка']

# Сохранение символов перевода строки
lines_with_breaks = text.splitlines(keepends=True)
print(lines_with_breaks)  # ['Первая строка\n', 'Вторая строка\n', 'Третья строка']
```

### `join()` - Объединение списка в строку

```python
str.join(iterable: Iterable[str]) -> str
```
- **`iterable`** - итерируемый объект со строками

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

## 6. Методы очистки строк

### `strip()` - Удаление пробелов с начала и конца

```python
str.strip(chars: str | None = None) -> str
```
- **`chars`** - символы для удаления (`None` означает пробельные)

```python
text = "   Привет, мир!   "
clean_text = text.strip()
print(f"'{clean_text}'")  # 'Привет, мир!'

# Удаление определенных символов
text2 = "...Привет, мир!..."
clean_text2 = text2.strip(".")
print(clean_text2)  # Привет, мир!

# Практический пример: очистка ввода пользователя
user_input = input("Введите имя: ").strip()
if user_input:
    print(f"Привет, {user_input}!")
else:
    print("Имя не введено")
```

### `lstrip()` - Удаление слева

```python
str.lstrip(chars: str | None = None) -> str
```
- **`chars`** - символы для удаления (`None` означает пробельные)

```python
text = "   Привет, мир!   "
left_clean = text.lstrip()
print(f"'{left_clean}'")  # 'Привет, мир!   '
```

### `rstrip()` - Удаление справа

```python
str.rstrip(chars: str | None = None) -> str
```
- **`chars`** - символы для удаления (`None` означает пробельные)

```python
text = "   Привет, мир!   "
right_clean = text.rstrip()
print(f"'{right_clean}'")  # '   Привет, мир!'
```

---

## 7. Методы форматирования

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

### `format_map()` - Форматирование с помощью словаря

```python
str.format_map(mapping: Mapping[str, Any]) -> str
```
- **`mapping`** - dict-like объект с параметрами

```python
template = "Привет, {name}! Твой возраст: {age}"

# Обычный format требует **
data = {"name": "Анна", "age": 25}
result1 = template.format(**data)
print(result1)  # Привет, Анна! Твой возраст: 25

# format_map работает напрямую со словарем
result2 = template.format_map(data)
print(result2)  # Привет, Анна! Твой возраст: 25

# Можно использовать с любым объектом, похожим на словарь
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def __getitem__(self, key):
        return getattr(self, key)

person = Person("Петр", 30)
result3 = template.format_map(person)
print(result3)  # Привет, Петр! Твой возраст: 30
```

---

## 8. Методы замены

### `replace()` - Замена подстроки

```python
str.replace(old: str, new: str, count: int = -1) -> str
```
- **`old`** - подстрока, которую нужно заменять новой
- **`new`** - подстрока, на которую нужно заменять старую
- **`count`** - максимальное количество замен (-1 означает все)

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

### `removeprefix()` - Удаление префикса (Python 3.9+)

```python
str.removeprefix(prefix: str) -> str
```
**`prefix`** - подстрока для удаления

```python
text = "https://example.com"
without_https = text.removeprefix("https://")
print(without_https)  # example.com

# Если префикса нет, строка остается без изменений
text2 = "example.com"
result = text2.removeprefix("https://")
print(result)  # example.com

# Практический пример: обработка URL
urls = ["https://google.com", "http://yandex.ru", "ftp://files.com"]
for url in urls:
    clean_url = url.removeprefix("https://").removeprefix("http://").removeprefix("ftp://")
    print(f"{url} -> {clean_url}")
```

### `removesuffix()` - Удаление суффикса (Python 3.9+)

```python
str.removesuffix(suffix: str) -> str
```
**`suffix`** - подстрока для удаления

```python
filename = "document.pdf"
name_without_ext = filename.removesuffix(".pdf")
print(name_without_ext)  # document

# Если суффикса нет, строка остается без изменений
filename2 = "document"
result = filename2.removesuffix(".pdf")
print(result)  # document

# Практический пример: работа с файлами
files = ["photo.jpg", "music.mp3", "document.pdf", "script.py"]
for file in files:
    name = file.removesuffix(".jpg").removesuffix(".mp3").removesuffix(".pdf").removesuffix(".py")
    print(f"Имя файла: {name}")
```

---

## 9. Методы трансляции

### `maketrans()` - Создание таблицы перевода

```python
str.maketrans(x: str | dict[str, str], y: str | None = None, z: str | None = None) -> dict[int, str | None]
```

**Аргумент `x`** может быть:
- **`str`** - строка символов для замены (если используется с параметром `y`)
- **`dict[str, str]`** - словарь, где ключи - заменяемые символы, значения - заменяющие

**Аргумент `y: str | None = None`**:
- **`str`** - строка символов-замен (должна быть той же длины, что и `x`)
- **`None`** - не используется (по умолчанию, если `x` - словарь)

**Аргумент `z: str | None = None`**:
- **`str`** - строка символов для удаления из текста
- **`None`** - ничего не удалять (по умолчанию)

**Возвращает**: `dict[int, str | None]`
- Словарь, где ключи - коды символов Unicode (int)
- Значения - строки для замены или None для удаления

### Способы использования

#### 1. Словарь как единственный аргумент
```python
# Создаем таблицу через словарь
table = str.maketrans({
    'а': 'a',  # русская 'а' → латинская 'a'
    'о': 'o',  # русская 'о' → латинская 'o'
    'и': 'i'   # русская 'и' → латинская 'i'
})
print(table)
# {1072: 'a', 1086: 'o', 1080: 'i'}
```

#### 2. Два строковых аргумента (x и y)
```python
# Создаем таблицу: 'a'→'1', 'b'→'2', 'c'→'3'
table = str.maketrans("abc", "123")
print(table)
# {97: '1', 98: '2', 99: '3'}
# где 97, 98, 99 - это ord('a'), ord('b'), ord('c')
```

#### 3. Три аргумента (замена + удаление)
```python
# Заменяем гласные на цифры и удаляем пробелы
table = str.maketrans("aeiou", "12345", " \t\n")
print(table)
# {97: '1', 101: '2', 105: '3', 111: '4', 117: '5', 32: None, 9: None, 10: None}
# None означает "удалить этот символ"
```

#### 4. Только удаление символов
```python
# Создаем таблицу только для удаления
table = str.maketrans("", "", "!@#$%^&*()")
print(table)
# {33: None, 64: None, 35: None, ...}
```


```python
# Создание таблицы перевода
translation_table = str.maketrans("aeio", "4310")
text = "hello world"
result = text.translate(translation_table)
print(result)  # h3ll0 w0rld

# Создание таблицы с помощью словаря
trans_dict = {"а": "a", "о": "o", "и": "i", "е": "e"}
translation_table2 = str.maketrans(trans_dict)
text2 = "привет"
result2 = text2.translate(translation_table2)
print(result2)  # прiвeт

# Удаление символов (третий параметр)
translation_table3 = str.maketrans("", "", "aeiou")  # Удаляем гласные
text3 = "hello world"
result3 = text3.translate(translation_table3)
print(result3)  # hll wrld
```

### `translate()` - Применение таблицы перевода

```python
str.translate(table: dict[int, str | None]) -> str
```
- **`table`** - словарь, созданный методом `maketrans()`
	- Ключи: коды символов Unicode (int)
	- Значения: 
	  - `str` - строка для замены символа
	  - `None` - удалить символ из результата
#### Как работает translate()

1. **Проходит по каждому символу** в исходной строке
2. **Получает код символа** через `ord(символ)`
3. **Ищет этот код** в таблице перевода
4. **Если найден**:
   - Значение `str` → заменяет символ
   - Значение `None` → удаляет символ
5. **Если не найден** → оставляет символ без изменений

```python
# Простая замена символов
text = "Hello, World!"
translation = str.maketrans("Hlo", "Xyz")
result = text.translate(translation)
print(result)  # Xeyyz, Wzryd!

# Транслитерация (упрощенная)
cyrillic_to_latin = str.maketrans(
    "абвгдежзийклмнопрстуфх",
    "abvgdezhijklmnoprstufh"
)
russian_text = "привет мир"
transliterated = russian_text.translate(cyrillic_to_latin)
print(transliterated)  # privet mir

# Очистка строки от нежелательных символов
text = "Привет!!! Как дела??? Отлично!!!"
# Удаляем повторяющиеся препинания
cleanup_table = str.maketrans("", "", "!?")
clean_text = text.translate(cleanup_table)
print(clean_text)  # Привет Как дела Отлично

# Практический пример: очистка данных
def clean_data(text):
    """Очистка данных от специальных символов"""
    # Удаляем все кроме букв, цифр и пробелов
    allowed_chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789 "
    allowed_chars += "абвгдеёжзийклмнопрстуфхцчшщъыьэюяАБВГДЕЁЖЗИЙКЛМНОПРСТУФХЦЧШЩЪЫЬЭЮЯ"

    # Создаем таблицу для удаления всех остальных символов
    all_chars = ''.join(chr(i) for i in range(256))
    chars_to_remove = ''.join(c for c in all_chars if c not in allowed_chars)

    cleanup_table = str.maketrans("", "", chars_to_remove)
    return text.translate(cleanup_table)

# Тест
dirty_data = "Привет!@#$%^&*()_+{}[]|\\:;\"'<>?,./~`"
clean_data_result = clean_data(dirty_data)
print(f"Исходно: {dirty_data}")
print(f"Очищено: {clean_data_result}")  # Привет
```

---

## 10. Методы выравнивания

### `center()` - Выравнивание по центру

```python
str.center(width: int, fillchar: str = ' ') -> str
```
- **`width`** - ширина
- **`fillchar`** - символ, с помощью которого будет осуществлено выравнивание

```python
text = "Python"
centered = text.center(20)
print(f"'{centered}'")  # '       Python       '

# С заполнителем
centered_star = text.center(20, "*")
print(centered_star)  # *******Python*******
```

### `ljust()` - Выравнивание по левому краю

```python
str.ljust(width: int, fillchar: str = ' ') -> str
```
- **`width`** - ширина
- **`fillchar`** - символ, с помощью которого будет осуществлено выравнивание

```python
text = "Python"
left_aligned = text.ljust(20, "-")
print(f"'{left_aligned}'")  # 'Python--------------'
```

### `rjust()` - Выравнивание по правому краю

```python
str.rjust(width: int, fillchar: str = ' ') -> str
```
- **`width`** - ширина
- **`fillchar`** - символ, с помощью которого будет осуществлено выравнивание

```python
text = "Python"
right_aligned = text.rjust(20, "-")
print(f"'{right_aligned}'")  # '--------------Python'

# Практический пример: форматирование таблицы
products = ["Яблоко", "Банан", "Апельсин"]
prices = [100, 50, 80]

print("Товар".ljust(15) + "Цена".rjust(10))
print("-" * 25)
for product, price in zip(products, prices):
    print(product.ljust(15) + str(price).rjust(10))
```

### `zfill()` - Заполнение нулями слева

```python
str.zfill(width: int) -> str
```
- **`width`** - ширина

```python
number = "42"
padded = number.zfill(5)
print(padded)  # 00042

# Полезно для номеров
order_id = "123"
formatted_id = order_id.zfill(6)
print(f"Заказ №{formatted_id}")  # Заказ №000123
```

---

## 11. Методы кодирования

### `encode()` - Кодирование в байты

```python
str.encode(encoding: str = 'utf-8', errors: str = 'strict') -> bytes
```
- **`encoding`** - название кодировки для преобразования строки в байты
- **`errors`** - способ обработки ошибок, т.е. символов, которые нельзя закодировать в выбранной кодировке.
	**Универсальные обработчики ошибок (для всех кодировок):**
	- `'strict'` - Выбрасывать `UnicodeError` (по умолчанию)
	- `'ignore'` - Игнорировать проблемные символы
	- `'replace'` - Заменять на `?` при кодировании, на `�` (U+FFFD) при декодировании
	- `'backslashreplace'` - Заменять на escape-последовательности (`\xhh`, `\uxxxx`, `\Uxxxxxxxx`)
	- `'surrogatescape'` - Заменять байты на суррогатные символы U+DC80-U+DCFF
	**Обработчики только для кодирования:**
	- `'xmlcharrefreplace'` - Заменять на XML-ссылки вида `&#num;`
	- `'namereplace'` - Заменять на имена Unicode `\N{NAME}`
	**Специальный обработчик для UTF кодировок:**
	- `'surrogatepass'` - Разрешать кодирование суррогатных символов (UTF-8, UTF-16, UTF-32)

```python
text = "Привет, мир!"
encoded = text.encode("utf-8")
print(encoded)  # b'\xd0\x9f\xd1\x80\xd0\xb8\xd0\xb2\xd0\xb5\xd1\x82, \xd0\xbc\xd0\xb8\xd1\x80!'
print(type(encoded))  # <class 'bytes'>

# Декодирование обратно
decoded = encoded.decode("utf-8")
print(decoded)  # Привет, мир!
```

---

## 12. Специальные методы

### `partition()` - Разделение на три части

```python
str.partition(sep: str) -> tuple[str, str, str]
```
- **`sep`** - ширина

```python
text = "name@example.com"
before, separator, after = text.partition("@")
print(f"До: {before}")      # name
print(f"Разделитель: {separator}")  # @
print(f"После: {after}")    # example.com

# Если разделитель не найден
text2 = "noatsign"
before, separator, after = text2.partition("@")
print(f"До: '{before}', Разделитель: '{separator}', После: '{after}'")
# До: 'noatsign', Разделитель: '', После: ''
```

### `rpartition()` - Разделение справа

```python
str.rpartition(sep: str) -> tuple[str, str, str]
```
- **`sep`** - ширина

```python
path = "/home/user/file.txt"
before, separator, after = path.rpartition("/")
print(f"Путь: {before}")     # /home/user
print(f"Файл: {after}")      # file.txt
```

### `expandtabs()` - Замена табуляций пробелами

```python
str.expandtabs(tabsize: int = 8) -> str
```
- **`tabsize`** - число пробелов для замены табуляции

```python
text = "Привет\tмир\tPython"
expanded = text.expandtabs(4)  # Заменить табуляции на 4 пробела
print(expanded)  # Привет    мир    Python
```

---

## 13. Практические примеры

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

### Пример 2: Обработка текста

```python
def analyze_text(text):
    """Анализ текста"""
    text = text.strip()
    
    # Статистика
    words = text.split()
    sentences = text.count(".") + text.count("!") + text.count("?")
    
    # Самое длинное слово
    longest_word = max(words, key=len) if words else ""
    
    print(f"Количество слов: {len(words)}")
    print(f"Количество предложений: {sentences}")
    print(f"Самое длинное слово: {longest_word}")
    print(f"Содержит вопросы: {'Да' if '?' in text else 'Нет'}")

# Тест
text = """Это пример текста для анализа. 
Он содержит несколько предложений. 
Есть ли здесь вопросы? Да, есть!"""

analyze_text(text)
```

### Пример 3: Форматирование данных

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

Методы строк в Python - это мощный инструмент для работы с текстом. Помните:

1. **Строки неизменяемы** - методы возвращают новую строку
2. **Методы можно объединять**: `text.strip().lower().replace(" ", "_")`
3. **Всегда проверяйте результат** методов типа `find()` на -1
4. **Используйте подходящие методы** для каждой задачи

Этот справочник поможет вам быстро найти нужный метод и понять, как его использовать!