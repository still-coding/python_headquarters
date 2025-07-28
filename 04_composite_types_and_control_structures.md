# 🏗️ Составные типы данных и алгоритмические структуры

# 🗿 Неизменяемые составные типы (immutable)

## 📦 Кортежи `tuple`

Кортеж - это упорядоченная **неизменяемая** коллекция объектов.

### Создание кортежей

```python
# Пустой кортеж
empty = ()
also_empty = tuple()

# Кортеж с элементами
point = (3, 4)
colors = ("красный", "зеленый", "синий")

# Кортеж из одного элемента (обязательна запятая!)
single = (42,)
not_tuple = (42)    # Это число, а не кортеж!

# Кортеж без скобок (но лучше со скобками для читаемости)
coords = 10, 20
```

### Операции с кортежами

```python
point = (3, 4, 5)

# Доступ (как у списков)
point[0]           # 3
point[-1]          # 5

# Срезы
point[0:2]         # (3, 4)

# Длина
len(point)         # 3

# Проверка принадлежности
3 in point         # True

# Конкатенация
(1, 2) + (3, 4)    # (1, 2, 3, 4)

# Повторение
(1, 2) * 3         # (1, 2, 1, 2, 1, 2)

# Нельзя изменять!
# point[0] = 10    # TypeError!
```

### Распаковка кортежей

```python
point = (3, 4)

# Распаковка
x, y = point       # x = 3, y = 4

# Обмен значений
a = 10
b = 20
a, b = b, a        # a = 20, b = 10

# Множественное присваивание
name, age, city = ("Иван", 25, "Москва")
```

### Когда использовать кортежи?

- Для неизменяемых данных (координаты, RGB цвета)
- Как ключи в словарях (списки нельзя!)
- Для возвращения нескольких значений из функции
- Когда нужна гарантия неизменности

# 🔄 Изменяемые типы данных (mutable)

## 📋 Списки `list`

Список - это упорядоченная изменяемая коллекция объектов.

### Создание списков

```python
# Пустой список
empty = []
also_empty = list()

# Список с элементами
numbers = [1, 2, 3, 4, 5]
mixed = [1, "hello", 3.14, True]
nested = [[1, 2], [3, 4], [5, 6]]

# Создание списка с помощью range()
nums = list(range(5))      # [0, 1, 2, 3, 4]
evens = list(range(0, 10, 2))  # [0, 2, 4, 6, 8]
```

### Операции со списками

```python
fruits = ["яблоко", "банан", "вишня"]

# Длина
len(fruits)        # 3

# Доступ по индексу
fruits[0]          # "яблоко"
fruits[-1]         # "вишня" (последний элемент)

# Срезы (как у строк)
fruits[0:2]        # ["яблоко", "банан"]
fruits[:2]         # ["яблоко", "банан"]
fruits[1:]         # ["банан", "вишня"]

# Изменение элементов
fruits[0] = "апельсин"
print(fruits)      # ["апельсин", "банан", "вишня"]

# Конкатенация
[1, 2] + [3, 4]    # [1, 2, 3, 4]

# Повторение
[1, 2] * 3         # [1, 2, 1, 2, 1, 2]

# Проверка принадлежности
"банан" in fruits  # True
"лимон" in fruits  # False
```

### [[04a_lists|Методы списков]]

```python
fruits = ["яблоко", "банан"]

# Добавление элементов
fruits.append("вишня")       # ["яблоко", "банан", "вишня"]
fruits.insert(1, "лимон")    # ["яблоко", "лимон", "банан", "вишня"]
fruits.extend(["груша", "слива"])  # добавляет несколько элементов

# Удаление элементов
fruits.remove("лимон")       # удаляет первое вхождение
last = fruits.pop()          # удаляет и возвращает последний элемент
second = fruits.pop(1)       # удаляет и возвращает элемент по индексу
del fruits[0]                # удаляет элемент по индексу

# Поиск
fruits.index("банан")        # возвращает индекс первого вхождения
fruits.count("яблоко")       # количество вхождений

# Сортировка и изменение порядка
numbers = [3, 1, 4, 1, 5]
numbers.sort()               # [1, 1, 3, 4, 5] (изменяет исходный список)
numbers.reverse()            # [5, 4, 3, 1, 1] (изменяет исходный список)

# Получение отсортированной копии (не изменяет исходный)
original = [3, 1, 4, 1, 5]
sorted_copy = sorted(original)  # [1, 1, 3, 4, 5]
reversed_copy = list(reversed(original))  # [5, 1, 4, 1, 3]
```

## 📚 Словари `dict`

Словарь - это изменяемая коллекция пар "ключ-значение". Доступ к элементам по ключу, а не по индексу.

### Создание словарей

```python
# Пустой словарь
empty = {}
also_empty = dict()

# Словарь с данными
person = {
    "name": "Иван",
    "age": 25,
    "city": "Москва"
}

# Создание из пар ключ-значение
grades = dict([("математика", 5), ("физика", 4)])

# Создание с помощью именованных аргументов
student = dict(name="Анна", age=22, major="Информатика")
```

### Операции со словарями

```python
person = {"name": "Иван", "age": 25, "city": "Москва"}

# Доступ к значениям
person["name"]           # "Иван"
person.get("age")        # 25
person.get("job", "Безработный")  # "Безработный" (значение по умолчанию)

# Изменение и добавление
person["age"] = 26       # изменяем значение
person["job"] = "Программист"  # добавляем новую пару

# Удаление
del person["city"]       # удаляем пару ключ-значение
job = person.pop("job")  # удаляем и возвращаем значение

# Проверка наличия ключа
"name" in person         # True
"salary" in person       # False

# Длина
len(person)              # количество пар ключ-значение
```

### [[04b_dicts|Методы словарей]]

```python
person = {"name": "Иван", "age": 25, "city": "Москва"}

# Получение ключей, значений, пар
person.keys()            # dict_keys(['name', 'age', 'city'])
person.values()          # dict_values(['Иван', 25, 'Москва'])
person.items()           # dict_items([('name', 'Иван'), ('age', 25), ('city', 'Москва')])

# Обновление
person.update({"job": "Программист", "age": 26})

# Очистка
person.clear()           # удаляет все элементы
```

## 🎯 Множества `set`

Множество - это изменяемая коллекция уникальных элементов без определенного порядка.

### Создание множеств

```python
# Пустое множество
empty = set()            # НЕ {}! Это словарь

# Множество с элементами
colors = {"красный", "зеленый", "синий"}
numbers = set([1, 2, 3, 2, 1])  # {1, 2, 3} (дубликаты удаляются)

# Множество из строки
letters = set("hello")   # {'h', 'e', 'l', 'o'}
```

### Операции с множествами и [[04c_sets|методы]]

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

# Добавление и удаление
a.add(5)                 # {1, 2, 3, 4, 5}
a.remove(1)              # {2, 3, 4, 5} (ошибка, если элемента нет)
a.discard(1)             # ничего не делает, если элемента нет

# Объединение (союз)
a | b                    # {1, 2, 3, 4, 5, 6}
a.union(b)               # то же самое

# Пересечение
a & b                    # {3, 4}
a.intersection(b)        # то же самое

# Разность
a - b                    # {1, 2}
a.difference(b)          # то же самое

# Симметричная разность (элементы, которые есть в одном из множеств, но не в обоих)
a ^ b                    # {1, 2, 5, 6}
a.symmetric_difference(b) # то же самое

# Проверки
3 in a                   # True
a.issubset(b)           # является ли a подмножеством b
a.issuperset(b)         # является ли a надмножеством b
a.isdisjoint(b)         # не пересекаются ли множества
```

### Применение множеств

```python
# Удаление дубликатов из списка
numbers = [1, 2, 2, 3, 3, 3, 4]
unique = list(set(numbers))  # [1, 2, 3, 4]

# Быстрая проверка принадлежности (для больших коллекций)
valid_ids = {1, 5, 10, 15, 20}
user_id = 5
if user_id in valid_ids:     # Очень быстро для больших множеств
    print("Доступ разрешен")
```

### 🧊 Frozenset - неизменяемые множества

`frozenset` - это неизменяемая версия множества, которую можно использовать как ключ в словаре или элемент другого множества.

```python
# Создание frozenset
frozen = frozenset([1, 2, 3, 2, 1])  # frozenset({1, 2, 3})
also_frozen = frozenset("hello")      # frozenset({'h', 'e', 'l', 'o'})

# Разница с обычным set
regular_set = {1, 2, 3}
frozen_set = frozenset([1, 2, 3])

# regular_set.add(4)     # ✅ Работает
# frozen_set.add(4)      # ❌ AttributeError: 'frozenset' object has no attribute 'add'

# Использование как ключи словаря
coordinates = {
    frozenset([0, 0]): "начало",
    frozenset([1, 1]): "диагональ",
    frozenset([0, 1]): "верх"
}

# Множество множеств
set_of_sets = {
    frozenset([1, 2]),
    frozenset([3, 4]),
    frozenset([1, 2])  # Дубликат будет удален
}
print(set_of_sets)  # {frozenset({1, 2}), frozenset({3, 4})}

# Все операции множеств работают
a = frozenset([1, 2, 3])
b = frozenset([3, 4, 5])
print(a | b)  # frozenset({1, 2, 3, 4, 5})
print(a & b)  # frozenset({3})
```

# 🔄 Алгоритмические структуры (управляющие конструкции)

## 🤔 Условные операторы

### Оператор `if`

```python
age = 18

if age >= 18:
    print("Вы совершеннолетний")
```

### Оператор `if-else`

```python
temperature = 25

if temperature > 30:
    print("Жарко!")
else:
    print("Нормальная температура")
```

### Оператор `if-elif-else`

```python
score = 85

if score >= 90:
    grade = "Отлично"
elif score >= 80:
    grade = "Хорошо"
elif score >= 70:
    grade = "Удовлетворительно"
else:
    grade = "Неудовлетворительно"

print(f"Оценка: {grade}")
```

### Тернарный оператор

```python
age = 20
status = "Взрослый" if age >= 18 else "Ребенок"

# Эквивалентно:
if age >= 18:
    status = "Взрослый"
else:
    status = "Ребенок"
```

## 🔁 Циклы

### Цикл `for`

Цикл `for` используется для перебора элементов последовательности.

```python
# Перебор списка
fruits = ["яблоко", "банан", "вишня"]
for fruit in fruits:
    print(f"Мне нравится {fruit}")

# Перебор строки
for letter in "Python":
    print(letter)

# Перебор словаря
person = {"name": "Иван", "age": 25}

# Перебор ключей
for key in person:
    print(key)

# Перебор значений
for value in person.values():
    print(value)

# Перебор пар ключ-значение
for key, value in person.items():
    print(f"{key}: {value}")

# Использование range()
for i in range(5):          # 0, 1, 2, 3, 4
    print(i)

for i in range(1, 6):       # 1, 2, 3, 4, 5
    print(i)

for i in range(0, 10, 2):   # 0, 2, 4, 6, 8
    print(i)
```

### Цикл `while`

Цикл `while` выполняется, пока условие истинно.

```python
# Простой цикл while
count = 0
while count < 5:
    print(f"Счетчик: {count}")
    count += 1

# Ввод от пользователя
password = ""
while password != "secret":
    password = input("Введите пароль: ")
print("Доступ разрешен!")
```

### Управление циклами

```python
# break - выходит из цикла
for i in range(10):
    if i == 5:
        break  # выходим из цикла при i = 5
    print(i)  # выведет: 0, 1, 2, 3, 4

# continue - переходит к следующей итерации
for i in range(5):
    if i == 2:
        continue  # пропускаем i = 2
    print(i)  # выведет: 0, 1, 3, 4

# else в циклах - выполняется, если цикл завершился нормально (без break)
for i in range(3):
    print(i)
else:
    print("Цикл завершен нормально")  # выполнится

for i in range(5):
    if i == 3:
        break
    print(i)
else:
    print("Этот блок не выполнится")  # не выполнится из-за break
```

### 🔢 Подробнее про `range()`

`range()` создает объект-последовательность чисел, который генерирует числа по требованию (ленивое вычисление).

#### Синтаксис и возможности

```python
# Основные формы
range(stop)                    # от 0 до stop-1
range(start, stop)             # от start до stop-1  
range(start, stop, step)       # от start до stop-1 с шагом step

# Примеры
list(range(5))                 # [0, 1, 2, 3, 4]
list(range(2, 8))              # [2, 3, 4, 5, 6, 7]
list(range(0, 10, 2))          # [0, 2, 4, 6, 8]
list(range(10, 0, -1))         # [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
list(range(5, 0, -2))          # [5, 3, 1]

# range() - это объект, а не список!
r = range(5)
print(type(r))                 # <class 'range'>
print(r)                       # range(0, 5)

# Атрибуты range
print(r.start)                 # 0
print(r.stop)                  # 5
print(r.step)                  # 1

# Проверка принадлежности (очень быстро!)
1000000 in range(0, 2000000, 2)  # True (проверка за O(1))
```

#### Эффективность памяти

```python
# ❌ Неэффективно для больших диапазонов
big_list = list(range(1000000))  # Создает список из 1 млн элементов в памяти

# ✅ Эффективно - генерирует по требованию
big_range = range(1000000)       # Объект range занимает постоянное количество памяти

# Использование в циклах
for i in range(1000000):         # Память не расходуется на весь список
    if i == 10:
        break
```

#### Практические применения

```python
# Обратный отсчет
for i in range(10, 0, -1):
    print(f"До старта: {i}")
print("Пуск!")

# Создание индексов
data = ["a", "b", "c", "d"]
for i in range(len(data)):
    print(f"Индекс {i}: {data[i]}")

# Но лучше использовать enumerate!
for i, item in enumerate(data):
    print(f"Индекс {i}: {item}")

# Пропуск элементов
numbers = list(range(20))
every_third = numbers[::3]      # [0, 3, 6, 9, 12, 15, 18]

# Разбиение на блоки
def chunks(lst, chunk_size):
    for i in range(0, len(lst), chunk_size):
        yield lst[i:i + chunk_size]

data = list(range(15))
for chunk in chunks(data, 4):
    print(chunk)
# [0, 1, 2, 3]
# [4, 5, 6, 7]
# [8, 9, 10, 11]
# [12, 13, 14]
```

---

### 📝 `enumerate()` - нумерация элементов

`enumerate()` добавляет счетчик к итерируемому объекту и возвращает пары (индекс, элемент).

```python
# Базовое использование
fruits = ["яблоко", "банан", "вишня"]

# Без enumerate (плохо)
for i in range(len(fruits)):
    print(f"{i}: {fruits[i]}")

# С enumerate (хорошо)
for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")

# Результат одинаковый:
# 0: яблоко
# 1: банан
# 2: вишня

# Начальное значение счетчика
for i, fruit in enumerate(fruits, start=1):
    print(f"{i}. {fruit}")
# 1. яблоко
# 2. банан
# 3. вишня

# enumerate возвращает объект enumerate
enum_obj = enumerate(fruits)
print(list(enum_obj))  # [(0, 'яблоко'), (1, 'банан'), (2, 'вишня')]
```

#### Практические примеры enumerate

```python
# Поиск с индексом
def find_with_index(lst, target):
    for i, item in enumerate(lst):
        if item == target:
            return i, item
    return None, None

names = ["Анна", "Борис", "Вера", "Георгий"]
index, name = find_with_index(names, "Вера")
print(f"'{name}' найден на позиции {index}")

# Создание словаря с индексами
index_dict = {item: i for i, item in enumerate(names)}
print(index_dict)  # {'Анна': 0, 'Борис': 1, 'Вера': 2, 'Георгий': 3}

# Обработка файла по строкам
def process_file_lines(filename):
    with open(filename, 'r') as file:
        for line_num, line in enumerate(file, start=1):
            if 'ERROR' in line:
                print(f"Ошибка в строке {line_num}: {line.strip()}")

# Сравнение двух списков
list1 = [1, 2, 3, 4]
list2 = [1, 2, 5, 4]

for i, (a, b) in enumerate(zip(list1, list2)):
    if a != b:
        print(f"Различие в позиции {i}: {a} != {b}")
```

---

### 🤐 `zip()` - объединение последовательностей

`zip()` объединяет несколько итерируемых объектов, создавая кортежи из соответствующих элементов.

```python
# Базовое использование
names = ["Анна", "Борис", "Вера"]
ages = [25, 30, 28]
cities = ["Москва", "СПб", "Казань"]

# Объединение двух списков
for name, age in zip(names, ages):
    print(f"{name}: {age} лет")

# Объединение трех списков
for name, age, city in zip(names, ages, cities):
    print(f"{name}, {age} лет, живет в {city}")

# zip возвращает объект zip
zipped = zip(names, ages)
print(list(zipped))  # [('Анна', 25), ('Борис', 30), ('Вера', 28)]
```

#### Особенности zip

```python
# zip останавливается на самой короткой последовательности
short = [1, 2]
long = [10, 20, 30, 40]
result = list(zip(short, long))
print(result)  # [(1, 10), (2, 20)] - элементы 30, 40 игнорируются

# Для работы с последовательностями разной длины используйте itertools.zip_longest
from itertools import zip_longest
result = list(zip_longest(short, long, fillvalue=0))
print(result)  # [(1, 10), (2, 20), (0, 30), (0, 40)]

# Распаковка zip (транспонирование)
pairs = [(1, 'a'), (2, 'b'), (3, 'c')]
numbers, letters = zip(*pairs)
print(numbers)  # (1, 2, 3)
print(letters)  # ('a', 'b', 'c')
```

#### Практические примеры zip

```python
# Создание словаря из двух списков
keys = ['name', 'age', 'city']
values = ['Иван', 30, 'Москва']
person = dict(zip(keys, values))
print(person)  # {'name': 'Иван', 'age': 30, 'city': 'Москва'}

# Параллельная обработка списков
prices = [100, 200, 300]
quantities = [2, 1, 3]
totals = [price * qty for price, qty in zip(prices, quantities)]
print(totals)  # [200, 200, 900]

# Сравнение списков
expected = [1, 2, 3, 4]
actual = [1, 2, 5, 4]
differences = [(i, exp, act) for i, (exp, act) in enumerate(zip(expected, actual)) if exp != act]
print(differences)  # [(2, 3, 5)]

# Группировка по парам
data = [1, 2, 3, 4, 5, 6]
pairs = list(zip(data[::2], data[1::2]))  # [(1, 2), (3, 4), (5, 6)]

# Матричные операции
matrix1 = [[1, 2], [3, 4]]
matrix2 = [[5, 6], [7, 8]]
result = [[a + b for a, b in zip(row1, row2)] for row1, row2 in zip(matrix1, matrix2)]
print(result)  # [[6, 8], [10, 12]]
```

---

### 🔄 Вложенные циклы

Вложенные циклы позволяют перебирать многомерные структуры данных или создавать комбинации элементов.

#### Базовые вложенные циклы

```python
# Простой вложенный цикл
for i in range(3):
    for j in range(3):
        print(f"({i}, {j})", end=" ")
    print()  # Переход на новую строку
# (0, 0) (0, 1) (0, 2) 
# (1, 0) (1, 1) (1, 2) 
# (2, 0) (2, 1) (2, 2) 

# Таблица умножения
for i in range(1, 4):
    for j in range(1, 4):
        print(f"{i} × {j} = {i*j:2d}", end="  ")
    print()
# 1 × 1 =  1  1 × 2 =  2  1 × 3 =  3  
# 2 × 1 =  2  2 × 2 =  4  2 × 3 =  6  
# 3 × 1 =  3  3 × 2 =  6  3 × 3 =  9  
```

#### Работа с матрицами

```python
# Создание матрицы
matrix = []
for i in range(3):
    row = []
    for j in range(4):
        row.append(i * 4 + j)
    matrix.append(row)

print(matrix)  # [[0, 1, 2, 3], [4, 5, 6, 7], [8, 9, 10, 11]]

# Обход матрицы
for i in range(len(matrix)):
    for j in range(len(matrix[i])):
        print(f"matrix[{i}][{j}] = {matrix[i][j]}")

# Более питоничный способ
for i, row in enumerate(matrix):
    for j, value in enumerate(row):
        print(f"matrix[{i}][{j}] = {value}")

# Поиск элемента в матрице
def find_in_matrix(matrix, target):
    for i, row in enumerate(matrix):
        for j, value in enumerate(row):
            if value == target:
                return i, j
    return None

position = find_in_matrix(matrix, 7)
print(f"Элемент 7 найден в позиции: {position}")  # (1, 3)
```

#### Генерация комбинаций

```python
# Все пары из двух списков
colors = ["красный", "синий", "зеленый"]
sizes = ["S", "M", "L"]

products = []
for color in colors:
    for size in sizes:
        products.append(f"{color}-{size}")

print(products)
# ['красный-S', 'красный-M', 'красный-L', 'синий-S', 'синий-M', 'синий-L', 'зеленый-S', 'зеленый-M', 'зеленый-L']

# Игральные карты
suits = ["♠", "♥", "♦", "♣"]
ranks = ["6", "7", "8", "9", "10", "J", "Q", "K", "A"]

deck = []
for suit in suits:
    for rank in ranks:
        deck.append(f"{rank}{suit}")

print(f"В колоде {len(deck)} карт")  # В колоде 36 карт
print(deck[:5])  # ['6♠', '7♠', '8♠', '9♠', '10♠']
```

#### Управление вложенными циклами

```python
# break во вложенных циклах
found = False
for i in range(5):
    for j in range(5):
        if i * j == 6:
            print(f"Найдено: {i} × {j} = 6")
            found = True
            break
    if found:
        break

# Использование флага для выхода из всех циклов
def find_pair_sum(matrix, target):
    for i, row in enumerate(matrix):
        for j, value in enumerate(row):
            for k in range(j + 1, len(row)):
                if value + row[k] == target:
                    return i, j, k, value, row[k]
    return None

# continue во вложенных циклах
for i in range(3):
    for j in range(3):
        if i == j:
            continue  # пропускаем диагональные элементы
        print(f"({i}, {j})", end=" ")
    print()
```
#### Оптимизация вложенных циклов

```python
# ❌ Неэффективно - повторные вычисления
def slow_distance_matrix(points):
    n = len(points)
    distances = []
    for i in range(n):
        row = []
        for j in range(n):
            # Вычисляем расстояние каждый раз
            dx = points[i][0] - points[j][0]
            dy = points[i][1] - points[j][1]
            distance = (dx*dx + dy*dy) ** 0.5
            row.append(distance)
        distances.append(row)
    return distances

# ✅ Эффективно - используем симметрию
def fast_distance_matrix(points):
    n = len(points)
    distances = [[0] * n for _ in range(n)]
    
    for i in range(n):
        for j in range(i + 1, n):  # только верхняя треугольная часть
            dx = points[i][0] - points[j][0]
            dy = points[i][1] - points[j][1]
            distance = (dx*dx + dy*dy) ** 0.5
            distances[i][j] = distance
            distances[j][i] = distance  # используем симметрию
    
    return distances

points = [(0, 0), (1, 0), (0, 1), (1, 1)]
matrix = fast_distance_matrix(points)
```

## 🔄 Включения (Comprehensions)

Включения - это компактный способ создания новых коллекций на основе существующих.

### Включения списков

```python
# Обычный способ
squares = []
for i in range(5):
    squares.append(i ** 2)
# squares = [0, 1, 4, 9, 16]

# С помощью включения
squares = [i ** 2 for i in range(5)]  # [0, 1, 4, 9, 16]

# С условием
evens = [i for i in range(10) if i % 2 == 0]  # [0, 2, 4, 6, 8]

# Обработка строк
words = ["python", "java", "javascript"]
lengths = [len(word) for word in words]  # [6, 4, 10]

# Вложенные циклы
matrix = [[i * j for j in range(3)] for i in range(3)]
# [[0, 0, 0], [0, 1, 2], [0, 2, 4]]
```

### Включения словарей

```python
# Создание словаря
squares_dict = {i: i ** 2 for i in range(5)}
# {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# Обращение словаря
original = {"a": 1, "b": 2, "c": 3}
reversed_dict = {v: k for k, v in original.items()}
# {1: 'a', 2: 'b', 3: 'c'}

# С условием
big_squares = {i: i ** 2 for i in range(10) if i ** 2 > 10}
# {4: 16, 5: 25, 6: 36, 7: 49, 8: 64, 9: 81}
```

### Включения множеств

```python
# Уникальные квадраты
unique_squares = {i ** 2 for i in range(-5, 6)}
# {0, 1, 4, 9, 16, 25}

# Уникальные символы
text = "hello world"
unique_chars = {char for char in text if char != ' '}
# {'h', 'e', 'l', 'o', 'w', 'r', 'd'}
```

## 🎯 Полезные встроенные функции для работы с коллекциями

```python
numbers = [1, 5, 3, 9, 2]

# Сортировка
sorted(numbers)          # [1, 2, 3, 5, 9] (новый список)
sorted(numbers, reverse=True)  # [9, 5, 3, 2, 1]

# Фильтрация
list(filter(lambda x: x > 3, numbers))  # [5, 9]

# Преобразование
list(map(lambda x: x ** 2, numbers))    # [1, 25, 9, 81, 4]

# Объединение
list(zip([1, 2, 3], ['a', 'b', 'c']))   # [(1, 'a'), (2, 'b'), (3, 'c')]

# Нумерация элементов
fruits = ["яблоко", "банан", "вишня"]
list(enumerate(fruits))  # [(0, 'яблоко'), (1, 'банан'), (2, 'вишня')]

# Использование enumerate в цикле
for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")

# Агрегирующие функции
sum(numbers)            # 20
min(numbers)            # 1
max(numbers)            # 9
len(numbers)            # 5

# Проверки
all([True, True, False])   # False (все ли True?)
any([True, False, False])  # True (есть ли хотя бы один True?)
```

## 📊 Полезные паттерны

### Подсчет элементов

```python
from collections import Counter

text = "hello world"
counter = Counter(text)
print(counter)  # Counter({'l': 3, 'o': 2, 'h': 1, 'e': 1, ' ': 1, 'w': 1, 'r': 1, 'd': 1})

# Самые частые элементы
counter.most_common(3)  # [('l', 3), ('o', 2), ('h', 1)]
```

### Группировка данных

```python
# Группировка чисел по четности
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
grouped = {"even": [], "odd": []}

for num in numbers:
    if num % 2 == 0:
        grouped["even"].append(num)
    else:
        grouped["odd"].append(num)

print(grouped)  # {'even': [2, 4, 6, 8, 10], 'odd': [1, 3, 5, 7, 9]}
```

### Работа с вложенными структурами

```python
# Список студентов
students = [
    {"name": "Иван", "grades": [5, 4, 5]},
    {"name": "Анна", "grades": [4, 5, 4]},
    {"name": "Петр", "grades": [3, 4, 3]}
]

# Средние оценки
for student in students:
    avg_grade = sum(student["grades"]) / len(student["grades"])
    print(f"{student['name']}: {avg_grade:.1f}")

# Лучшие студенты (средний балл >= 4.5)
best_students = [
    student["name"] 
    for student in students 
    if sum(student["grades"]) / len(student["grades"]) >= 4.5
]
print(f"Лучшие студенты: {best_students}")
```

## 🏁 Итоги

Теперь ты знаешь:

**Составные типы данных:**
- **Списки** `list` - изменяемые упорядоченные коллекции
- **Кортежи** `tuple` - неизменяемые упорядоченные коллекции
- **Словари** `dict` - изменяемые коллекции пар ключ-значение
- **Множества** `set` - изменяемые коллекции уникальных элементов

**Алгоритмические структуры:**
- **Условия**: `if`, `elif`, `else`
- **Циклы**: `for`, `while`
- **Управление циклами**: `break`, `continue`, `else`
- **Включения**: компактный синтаксис для создания коллекций
