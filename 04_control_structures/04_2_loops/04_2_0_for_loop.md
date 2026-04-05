[[04_1_1_ternary|← 🔀 Тернарное выражение]] | [[04_2_1_while_loop| 🔄 Цикл while →]]

---
# 🔁 Цикл for

## Что это?
```toc
minLevel: 2
```
Цикл `for` используется для перебора элементов последовательности (списка, строки, словаря и т.д.). Это самый популярный цикл в Python.

## Синтаксис

```python
for элемент in последовательность:
    # действия с элементом
```

---

## Базовые примеры

### Перебор списка

```python
fruits = ["яблоко", "банан", "вишня"]

for fruit in fruits:
    print(f"Мне нравится {fruit}")

# Вывод:
# Мне нравится яблоко
# Мне нравится банан
# Мне нравится вишня
```

### Перебор строки

```python
for letter in "Python":
    print(letter)

# Вывод:
# P
# y
# t
# h
# o
# n
```

### Перебор чисел

```python
# Числа от 0 до 4
for i in range(5):
    print(i)

# Вывод: 0, 1, 2, 3, 4
```

---

## Перебор различных типов данных

### Списки и кортежи

```python
# Список
numbers = [1, 2, 3, 4, 5]
for num in numbers:
    print(num * 2)

# Кортеж
coordinates = (10, 20, 30)
for coord in coordinates:
    print(f"Координата: {coord}")
```

### Словари

```python
person = {"name": "Иван", "age": 25, "city": "Москва"}

# Перебор ключей (по умолчанию)
for key in person:
    print(key)
# Вывод: name, age, city

# Перебор значений
for value in person.values():
    print(value)
# Вывод: Иван, 25, Москва

# Перебор пар ключ-значение
for key, value in person.items():
    print(f"{key}: {value}")
# Вывод:
# name: Иван
# age: 25
# city: Москва
```

### Множества

```python
colors = {"красный", "синий", "зелёный"}

for color in colors:
    print(color)
# Порядок не гарантирован!
```

---

## Работа с индексами

Иногда нужен не только элемент, но и его индекс:

### Способ 1: range(len())

```python
fruits = ["яблоко", "банан", "вишня"]

for i in range(len(fruits)):
    print(f"{i}: {fruits[i]}")

# Вывод:
# 0: яблоко
# 1: банан
# 2: вишня
```

### Способ 2: enumerate() (рекомендуется)

```python
fruits = ["яблоко", "банан", "вишня"]

for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")

# Вывод:
# 0: яблоко
# 1: банан
# 2: вишня
```

Подробнее: [[04_3_1_enumerate|enumerate()]]

---

## Практические примеры

### Сумма чисел

```python
numbers = [1, 2, 3, 4, 5]
total = 0

for num in numbers:
    total += num

print(f"Сумма: {total}")  # Сумма: 15
```

### Поиск элемента

```python
names = ["Анна", "Борис", "Вера", "Георгий"]
search_name = "Вера"

for name in names:
    if name == search_name:
        print(f"Найден: {name}")
        break
else:
    print(f"{search_name} не найден")
```

### Фильтрация данных

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
even_numbers = []

for num in numbers:
    if num % 2 == 0:
        even_numbers.append(num)

print(even_numbers)  # [2, 4, 6, 8, 10]
```

### Обработка строк

```python
text = "Hello World"
vowels = "aeiouAEIOU"
vowel_count = 0

for char in text:
    if char in vowels:
        vowel_count += 1

print(f"Количество гласных: {vowel_count}")  # 3
```

---

## Изменение элементов списка

### ❌ Неправильно

```python
numbers = [1, 2, 3, 4, 5]

# Это НЕ изменит исходный список!
for num in numbers:
    num = num * 2

print(numbers)  # [1, 2, 3, 4, 5] — без изменений
```

### ✅ Правильно

```python
numbers = [1, 2, 3, 4, 5]

# Способ 1: через индексы
for i in range(len(numbers)):
    numbers[i] = numbers[i] * 2

# Способ 2: создать новый список
doubled = [num * 2 for num in numbers]

# Способ 3: enumerate
for i, num in enumerate(numbers):
    numbers[i] = num * 2

print(numbers)  # [2, 4, 6, 8, 10]
```

---

## Распаковка в цикле

Когда элементы — это кортежи или списки:

```python
# Список пар
pairs = [(1, 'a'), (2, 'b'), (3, 'c')]

for number, letter in pairs:
    print(f"{number} -> {letter}")

# Вывод:
# 1 -> a
# 2 -> b
# 3 -> c
```

```python
# Список студентов
students = [
    ("Иван", 20, "Москва"),
    ("Анна", 22, "СПб"),
    ("Пётр", 19, "Казань")
]

for name, age, city in students:
    print(f"{name}, {age} лет, {city}")
```

---

## Часто используемые паттерны

### Подсчёт элементов

```python
fruits = ["яблоко", "банан", "яблоко", "вишня", "банан", "яблоко"]
count = {}

for fruit in fruits:
    if fruit in count:
        count[fruit] += 1
    else:
        count[fruit] = 1

print(count)  # {'яблоко': 3, 'банан': 2, 'вишня': 1}

# Или проще с collections.Counter
from collections import Counter
count = Counter(fruits)
```

### Объединение двух списков

```python
names = ["Анна", "Борис", "Вера"]
ages = [25, 30, 28]

# С помощью zip (рекомендуется)
for name, age in zip(names, ages):
    print(f"{name}: {age} лет")

# Вывод:
# Анна: 25 лет
# Борис: 30 лет
# Вера: 28 лет
```

Подробнее: [[04_3_2_zip|zip()]]

---

## Частые ошибки

### ❌ Изменение списка во время итерации

```python
numbers = [1, 2, 3, 4, 5]

# ОПАСНО! Может привести к неожиданному поведению
for num in numbers:
    if num % 2 == 0:
        numbers.remove(num)
```

✅ Правильно:
```python
numbers = [1, 2, 3, 4, 5]

# Создать новый список
numbers = [num for num in numbers if num % 2 != 0]

# Или использовать копию для итерации
for num in numbers[:]:  # [:] создаёт копию
    if num % 2 == 0:
        numbers.remove(num)
```

### ❌ Забыть двоеточие

```python
for i in range(5)  # ОШИБКА! Нет двоеточия
    print(i)
```

✅ Правильно:
```python
for i in range(5):
    print(i)
```

---

## Связанные темы

- [[04_3_0_range|range()]] — генерация последовательности чисел
- [[04_3_1_enumerate|enumerate()]] — получение индекса и значения
- [[04_3_2_zip|zip()]] — объединение нескольких последовательностей
- [[04_2_2_loop_control|Управление циклами]] — break, continue, else
- [[04_3_4_nested_loops|Вложенные циклы]]
- [[04_4_comprehensions|Списковые включения]] — компактная альтернатива

---

[[04_1_1_ternary|← 🔀 Тернарное выражение]] | [[04_2_1_while_loop| 🔄 Цикл while →]]

