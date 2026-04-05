[[04_3_0_range|← 🔢 range() — генерация числовых последовательностей]] | [[04_3_2_zip| 🤐 zip() — объединение последовательностей →]]

---
# 🔢 enumerate() — нумерация элементов
```toc
minLevel: 2
```

## Что это?

`enumerate()` — встроенная функция Python, которая возвращает **пары (индекс, элемент)** при итерации по последовательности. Это элегантная альтернатива использованию `range(len())`.

---

## Синтаксис

```python
enumerate(iterable, start=0)
```

- **`iterable`** — любая итерируемая последовательность (список, строка, кортеж и т.д.)
- **`start`** — начальное значение счётчика (по умолчанию 0)

---

## Базовые примеры

### Простейший пример

```python
fruits = ["яблоко", "банан", "вишня"]

for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")

# Вывод:
# 0: яблоко
# 1: банан
# 2: вишня
```

### Начало отсчёта с 1

```python
fruits = ["яблоко", "банан", "вишня"]

for i, fruit in enumerate(fruits, start=1):
    print(f"{i}. {fruit}")

# Вывод:
# 1. яблоко
# 2. банан
# 3. вишня
```

### Преобразование в список

```python
fruits = ["яблоко", "банан", "вишня"]

indexed = list(enumerate(fruits))
print(indexed)
# [(0, 'яблоко'), (1, 'банан'), (2, 'вишня')]

# С нумерацией от 1
indexed_from_1 = list(enumerate(fruits, start=1))
print(indexed_from_1)
# [(1, 'яблоко'), (2, 'банан'), (3, 'вишня')]
```

---

## enumerate vs range(len())

### ❌ Неудобный способ (range + len)

```python
fruits = ["яблоко", "банан", "вишня"]

for i in range(len(fruits)):
    fruit = fruits[i]
    print(f"{i}: {fruit}")
```

### ✅ Элегантный способ (enumerate)

```python
fruits = ["яблоко", "банан", "вишня"]

for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")
```

**Преимущества enumerate:**
- Более читаемый код
- Не требует индексации `fruits[i]`
- Работает с любыми итерируемыми объектами (не только списками)
- Более питоничный стиль

---

## Работа с разными типами данных

### Списки

```python
numbers = [10, 20, 30, 40, 50]

for i, num in enumerate(numbers):
    print(f"Элемент {i}: {num}")

# Вывод:
# Элемент 0: 10
# Элемент 1: 20
# ...
```

### Строки

```python
text = "Python"

for i, char in enumerate(text):
    print(f"Символ {i}: {char}")

# Вывод:
# Символ 0: P
# Символ 1: y
# Символ 2: t
# ...
```

### Кортежи

```python
colors = ("красный", "зелёный", "синий")

for i, color in enumerate(colors):
    print(f"{i}: {color}")
```

### Множества

```python
# Для множеств порядок не гарантирован!
unique_numbers = {5, 1, 3, 2, 4}

for i, num in enumerate(unique_numbers):
    print(f"{i}: {num}")
```

### Словари

```python
person = {"name": "Анна", "age": 25, "city": "Москва"}

# Нумерация ключей
for i, key in enumerate(person):
    print(f"{i}: {key} = {person[key]}")

# Нумерация пар ключ-значение
for i, (key, value) in enumerate(person.items()):
    print(f"{i}: {key} = {value}")
```

---

## Практические примеры

### Модификация элементов по индексу

```python
numbers = [10, 20, 30, 40, 50]

# Удвоить каждый элемент
for i, num in enumerate(numbers):
    numbers[i] = num * 2

print(numbers)  # [20, 40, 60, 80, 100]
```

### Поиск с возвратом индекса

```python
def find_index(lst, target):
    """Находит индекс первого вхождения элемента"""
    for i, item in enumerate(lst):
        if item == target:
            return i
    return None

names = ["Анна", "Борис", "Вера", "Георгий"]
name = "Вера"
index = find_index(names, name)

if index is not None:
    print(f"'{name}' найден на позиции {index}")
else:
    print("Не найден")

# Вывод: 'Вера' найден на позиции 2
```

### Создание словаря с индексами

```python
names = ["Анна", "Борис", "Вера", "Георгий"]

# Словарь: элемент -> индекс
index_dict = {name: i for i, name in enumerate(names)}
print(index_dict)
# {'Анна': 0, 'Борис': 1, 'Вера': 2, 'Георгий': 3}

# Словарь: индекс -> элемент
name_dict = dict(enumerate(names))
print(name_dict)
# {0: 'Анна', 1: 'Борис', 2: 'Вера', 3: 'Георгий'}
```

### Нумерованный список

```python
tasks = ["Купить молоко", "Позвонить маме", "Сделать домашку"]

print("Список задач:")
for i, task in enumerate(tasks, start=1):
    print(f"{i}. {task}")

# Вывод:
# Список задач:
# 1. Купить молоко
# 2. Позвонить маме
# 3. Сделать домашку
```

### Поиск всех вхождений

```python
def find_all_indices(lst, target):
    """Находит все индексы элемента"""
    return [i for i, item in enumerate(lst) if item == target]

numbers = [1, 2, 3, 2, 4, 2, 5]
indices = find_all_indices(numbers, 2)
print(f"Число 2 найдено на позициях: {indices}")
# Число 2 найдено на позициях: [1, 3, 5]
```

### Обработка с отслеживанием позиции

```python
data = ["apple", "banana", "avocado", "cherry", "apricot"]

print("Слова, начинающиеся с 'a':")
for i, word in enumerate(data, start=1):
    if word.startswith('a'):
        print(f"  {i}. {word}")

# Вывод:
# Слова, начинающиеся с 'a':
#   1. apple
#   3. avocado
#   5. apricot
```

### Сравнение двух списков

```python
list1 = [1, 2, 3, 4, 5]
list2 = [1, 2, 5, 4, 5]

differences = []
for i, (a, b) in enumerate(zip(list1, list2)):
    if a != b:
        differences.append((i, a, b))

if differences:
    print("Различия найдены:")
    for i, a, b in differences:
        print(f"  Позиция {i}: {a} != {b}")
else:
    print("Списки идентичны")

# Вывод:
# Различия найдены:
#   Позиция 2: 3 != 5
```

### Обработка файла с обработкой ошибок

```python
def process_file_lines(filename):
    """Обрабатывает файл с отслеживанием номеров строк"""
    with open(filename, 'r', encoding='utf-8') as file:
        for line_num, line in enumerate(file, start=1):
            line = line.strip()
            
            if not line:  # пропускаем пустые строки
                continue
                
            if 'ERROR' in line:
                print(f"Ошибка в строке {line_num}: {line}")
            elif 'WARNING' in line:
                print(f"Предупреждение в строке {line_num}: {line}")

# process_file_lines("log.txt")
```

---

## Комбинирование с другими функциями

### enumerate + zip

```python
names = ["Анна", "Борис", "Вера"]
ages = [25, 30, 28]
cities = ["Москва", "СПб", "Казань"]

for i, (name, age, city) in enumerate(zip(names, ages, cities), start=1):
    print(f"{i}. {name}, {age} лет, {city}")

# Вывод:
# 1. Анна, 25 лет, Москва
# 2. Борис, 30 лет, СПб
# 3. Вера, 28 лет, Казань
```

### enumerate + filter

```python
numbers = [10, 15, 20, 25, 30, 35, 40]

# Найти индексы чисел > 25
large_indices = [i for i, num in enumerate(numbers) if num > 25]
print(large_indices)  # [4, 5, 6]
```

### enumerate + sorted

```python
words = ["banana", "apple", "cherry", "date"]

# Сортируем с сохранением исходных индексов
indexed_words = list(enumerate(words))
sorted_by_word = sorted(indexed_words, key=lambda x: x[1])

print("Отсортированные слова с исходными индексами:")
for orig_idx, word in sorted_by_word:
    print(f"  {word} (был на позиции {orig_idx})")

# Вывод:
# Отсортированные слова с исходными индексами:
#   apple (был на позиции 1)
#   banana (был на позиции 0)
#   cherry (был на позиции 2)
#   date (был на позиции 3)
```

---

## Распаковка в enumerate

Если элементы — это кортежи или списки:

```python
pairs = [("a", 1), ("b", 2), ("c", 3)]

for i, (letter, number) in enumerate(pairs):
    print(f"Пара {i}: буква={letter}, число={number}")

# Вывод:
# Пара 0: буква=a, число=1
# Пара 1: буква=b, число=2
# Пара 2: буква=c, число=3
```

---

## Производительность

### Почему enumerate эффективнее?

```python
# ❌ Медленнее - требует дополнительных вызовов len() и []
fruits = ["яблоко", "банан", "вишня"]
for i in range(len(fruits)):
    fruit = fruits[i]
    print(f"{i}: {fruit}")

# ✅ Быстрее - прямая итерация
for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")
```

### Сравнение подходов

```python
import timeit

data = list(range(1000))

# Способ 1: range(len())
def method1():
    result = []
    for i in range(len(data)):
        result.append((i, data[i]))
    return result

# Способ 2: enumerate
def method2():
    result = []
    for i, item in enumerate(data):
        result.append((i, item))
    return result

# enumerate быстрее!
```

---

## Частые ошибки

### ❌ Забыть распаковать кортеж

```python
fruits = ["яблоко", "банан", "вишня"]

# ОШИБКА - получаем кортеж, а не индекс и значение
for item in enumerate(fruits):
    print(item)  # (0, 'яблоко'), (1, 'банан'), ...
```

✅ Правильно:
```python
for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")
```

### ❌ Попытка повторно использовать enumerate

```python
enum_obj = enumerate(fruits)

# Первый раз работает
for i, fruit in enum_obj:
    print(i, fruit)

# Второй раз - пусто!
for i, fruit in enum_obj:
    print(i, fruit)  # ничего не выведет
```

✅ Правильно:
```python
# Создавать новый объект enumerate каждый раз
for i, fruit in enumerate(fruits):
    print(i, fruit)

for i, fruit in enumerate(fruits):  # снова работает
    print(i, fruit)
```

### ❌ Изменение списка во время итерации

```python
numbers = [1, 2, 3, 4, 5]

# ОПАСНО!
for i, num in enumerate(numbers):
    if num % 2 == 0:
        numbers.pop(i)  # может пропустить элементы
```

✅ Правильно:
```python
# Создать новый список
numbers = [num for num in numbers if num % 2 != 0]

# Или итерировать по копии
for i, num in enumerate(numbers[:]):
    if num % 2 == 0:
        numbers.remove(num)
```

---

## Альтернативы enumerate

### Когда НЕ нужен enumerate

**Если индекс не используется:**
```python
# ❌ Бесполезное использование enumerate
for i, fruit in enumerate(fruits):
    print(fruit)  # индекс i не используется

# ✅ Лучше
for fruit in fruits:
    print(fruit)
```

**Если нужен только индекс:**
```python
# ❌ Игнорирование значения
for i, _ in enumerate(fruits):
    print(i)

# ✅ Лучше
for i in range(len(fruits)):
    print(i)
```

---

## Полезные паттерны

### Первый и последний элементы

```python
items = ["a", "b", "c", "d", "e"]

for i, item in enumerate(items):
    if i == 0:
        print(f"Первый: {item}")
    elif i == len(items) - 1:
        print(f"Последний: {item}")
    else:
        print(f"Средний: {item}")
```

### Группировка с шагом

```python
data = ["a", "b", "c", "d", "e", "f", "g", "h"]

# Группы по 3 элемента
for i, item in enumerate(data):
    if i % 3 == 0:
        print()  # новая группа
    print(item, end=" ")

# Вывод:
# a b c 
# d e f 
# g h
```

### Пометка особых элементов

```python
scores = [85, 92, 78, 95, 88]

for i, score in enumerate(scores, start=1):
    marker = " ⭐" if score >= 90 else ""
    print(f"Студент {i}: {score}{marker}")

# Вывод:
# Студент 1: 85
# Студент 2: 92 ⭐
# Студент 3: 78
# Студент 4: 95 ⭐
# Студент 5: 88
```

---

## Связанные темы

- [[04_2_0_for_loop|Цикл for]] — основное применение enumerate
- [[04_3_0_range|range()]] — для простой генерации индексов
- [[04_3_2_zip|zip()]] — для параллельной итерации по нескольким последовательностям

---

[[04_3_0_range|← 🔢 range() — генерация числовых последовательностей]] | [[04_3_2_zip| 🤐 zip() — объединение последовательностей →]]
