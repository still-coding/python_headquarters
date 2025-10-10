[[03_2_6_type_conversion|← 📋 Преобразование типов]] | [[03_3_2_list_methods|📋Методы списков →]]

---
# 📋 Списки `list`
```toc
minLevel: 2
```
Список - это упорядоченная **изменяемая** коллекция объектов.

## Создание списков

```python
# Пустой список
empty = []
also_empty = list()

# Список с элементами
numbers = [1, 2, 3, 4, 5]
mixed = [1, "hello", 3.14, True]
nested = [[1, 2], [3, 4], [5, 6]]

# Создание списка с помощью range()
nums = list(range(5))           # [0, 1, 2, 3, 4]
evens = list(range(0, 10, 2))   # [0, 2, 4, 6, 8]

# Создание списка через list()
from_string = list("Python")    # ['P', 'y', 't', 'h', 'o', 'n']
from_tuple = list((1, 2, 3))    # [1, 2, 3]
```

## Базовые операции

```python
fruits = ["яблоко", "банан", "вишня"]

# Длина
len(fruits)        # 3

# Доступ по индексу
fruits[0]          # "яблоко"
fruits[-1]         # "вишня" (последний элемент)
fruits[-2]         # "банан" (предпоследний элемент)

# Срезы (как у строк)
fruits[0:2]        # ["яблоко", "банан"]
fruits[:2]         # ["яблоко", "банан"] (от начала)
fruits[1:]         # ["банан", "вишня"] (до конца)
fruits[::2]        # ["яблоко", "вишня"] (каждый второй)
fruits[::-1]       # ["вишня", "банан", "яблоко"] (обратный порядок)

# Изменение элементов
fruits[0] = "апельсин"
print(fruits)      # ["апельсин", "банан", "вишня"]

# Изменение среза
fruits[0:2] = ["груша", "слива"]
print(fruits)      # ["груша", "слива", "вишня"]
```

## Операторы для списков

```python
# Конкатенация
[1, 2] + [3, 4]    # [1, 2, 3, 4]

# Повторение
[1, 2] * 3         # [1, 2, 1, 2, 1, 2]
[0] * 5            # [0, 0, 0, 0, 0]

# Проверка принадлежности
"банан" in fruits   # True
"лимон" in fruits   # False
"лимон" not in fruits  # True

# Сравнение списков
[1, 2, 3] == [1, 2, 3]  # True
[1, 2] < [1, 3]         # True (лексикографическое сравнение)
```

## Изменяемость списков

⚠️ **Важно!** Списки изменяемы, что влияет на копирование:

```python
# Копирование по ссылке (неправильно!)
original = [1, 2, 3]
copy = original           # Это не копия!
copy.append(4)
print(original)           # [1, 2, 3, 4] - изменился и оригинал!

# Правильное копирование
original = [1, 2, 3]
copy = original.copy()    # Создаём копию
# или
copy = original[:]        # Через срез
# или
copy = list(original)     # Через конструктор

copy.append(4)
print(original)           # [1, 2, 3] - оригинал не изменился
print(copy)               # [1, 2, 3, 4]
```

## Вложенные списки (матрицы)

```python
# Создание матрицы 3x3
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# Доступ к элементам
matrix[0][0]       # 1
matrix[1][2]       # 6
matrix[2][1]       # 8

# ⚠️ Ошибка при создании матрицы
wrong = [[0] * 3] * 3      # Все строки ссылаются на один список!
wrong[0][0] = 1
print(wrong)               # [[1, 0, 0], [1, 0, 0], [1, 0, 0]]

# ✅ Правильное создание
correct = [[0] * 3 for _ in range(3)]
correct[0][0] = 1
print(correct)             # [[1, 0, 0], [0, 0, 0], [0, 0, 0]]
```

## Списки и функции

### Функции, принимающие списки

```python
def print_items(items):
    """Выводит все элементы списка"""
    for item in items:
        print(f"- {item}")

fruits = ["яблоко", "банан", "вишня"]
print_items(fruits)
```

### Функции, возвращающие списки

```python
def get_even_numbers(n):
    """Возвращает список четных чисел от 0 до n"""
    return [x for x in range(n + 1) if x % 2 == 0]

evens = get_even_numbers(10)
print(evens)  # [0, 2, 4, 6, 8, 10]
```

### ⚠️ Изменяемые значения по умолчанию

```python
# НЕПРАВИЛЬНО! Не используйте изменяемые значения по умолчанию
def add_item_wrong(item, items=[]):
    items.append(item)
    return items

print(add_item_wrong(1))  # [1]
print(add_item_wrong(2))  # [1, 2] - неожиданно!

# ПРАВИЛЬНО
def add_item_correct(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items

print(add_item_correct(1))  # [1]
print(add_item_correct(2))  # [2] - как ожидалось
```

## List Comprehensions (Включения списков)

Компактный способ создания списков:

```python
# Базовый синтаксис: [выражение for элемент in последовательность]
squares = [x**2 for x in range(5)]
print(squares)  # [0, 1, 4, 9, 16]

# С условием
evens = [x for x in range(10) if x % 2 == 0]
print(evens)  # [0, 2, 4, 6, 8]

# С преобразованием
words = ["hello", "world", "python"]
upper_words = [word.upper() for word in words]
print(upper_words)  # ['HELLO', 'WORLD', 'PYTHON']

# Вложенные comprehensions
matrix = [[i * j for j in range(3)] for i in range(3)]
print(matrix)  # [[0, 0, 0], [0, 1, 2], [0, 2, 4]]
```

## Методы списков

Список имеет множество полезных методов. Подробное описание см. в [[03_3_2_list_methods|Методы списков]].

Краткий обзор:

```python
fruits = ["яблоко", "банан"]

# Добавление
fruits.append("вишня")       # Добавить в конец
fruits.insert(1, "лимон")    # Вставить на позицию
fruits.extend(["груша", "слива"])  # Добавить несколько

# Удаление
fruits.remove("лимон")       # Удалить первое вхождение
last = fruits.pop()          # Удалить и вернуть последний
second = fruits.pop(1)       # Удалить и вернуть по индексу
del fruits[0]                # Удалить по индексу

# Поиск
index = fruits.index("банан")    # Индекс первого вхождения
count = fruits.count("яблоко")   # Количество вхождений

# Сортировка
numbers = [3, 1, 4, 1, 5]
numbers.sort()               # [1, 1, 3, 4, 5] (изменяет список)
numbers.reverse()            # [5, 4, 3, 1, 1] (изменяет список)

# Создание отсортированной копии
sorted_copy = sorted(numbers)     # Не изменяет оригинал
reversed_copy = list(reversed(numbers))  # Не изменяет оригинал
```

Подробнее о методах: [[03_3_2_list_methods|Методы списков →]]

## Когда использовать списки?

✅ **Используйте списки для:**
- Упорядоченных коллекций данных
- Когда нужно изменять элементы
- Когда требуется добавлять/удалять элементы
- Хранения однородных данных (список чисел, строк и т.д.)
- Стека или очереди

❌ **Не используйте списки когда:**
- Нужна гарантия неизменности (используйте кортежи)
- Важна быстрая проверка принадлежности (используйте множества)
- Нужен доступ по ключу (используйте словари)

## Полезные встроенные функции

```python
numbers = [1, 5, 3, 9, 2]

# Агрегирующие функции
sum(numbers)           # 20
min(numbers)           # 1
max(numbers)           # 9
len(numbers)           # 5

# Сортировка (создаёт новый список)
sorted(numbers)        # [1, 2, 3, 5, 9]
sorted(numbers, reverse=True)  # [9, 5, 3, 2, 1]

# Фильтрация и преобразование
list(filter(lambda x: x > 3, numbers))  # [5, 9]
list(map(lambda x: x**2, numbers))      # [1, 25, 9, 81, 4]

# Нумерация элементов
for i, num in enumerate(numbers):
    print(f"Индекс {i}: {num}")

# Объединение списков
names = ["Анна", "Борис", "Вера"]
ages = [25, 30, 28]
for name, age in zip(names, ages):
    print(f"{name}: {age} лет")
```

## Практические примеры

### Удаление дубликатов с сохранением порядка

```python
def remove_duplicates(items):
    """Удаляет дубликаты, сохраняя порядок"""
    seen = set()
    result = []
    for item in items:
        if item not in seen:
            seen.add(item)
            result.append(item)
    return result

numbers = [1, 2, 2, 3, 1, 4, 3, 5]
unique = remove_duplicates(numbers)
print(unique)  # [1, 2, 3, 4, 5]
```

### Разбиение списка на части

```python
def chunks(lst, size):
    """Разбивает список на части заданного размера"""
    for i in range(0, len(lst), size):
        yield lst[i:i + size]

data = list(range(10))
for chunk in chunks(data, 3):
    print(chunk)
# [0, 1, 2]
# [3, 4, 5]
# [6, 7, 8]
# [9]
```

### Поиск максимума с индексом

```python
numbers = [3, 1, 4, 1, 5, 9, 2, 6]

# Максимальное значение и его индекс
max_value = max(numbers)
max_index = numbers.index(max_value)
print(f"Максимум: {max_value} на позиции {max_index}")
```

---

[[03_2_6_type_conversion|← 📋 Преобразование типов]] | [[03_3_2_list_methods|📋Методы списков →]]
