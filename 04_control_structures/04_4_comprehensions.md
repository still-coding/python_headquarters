[[04_3_2_zip|← 🤐 zip() — объединение последовательностей]] | [[06_functions/06_00_intro|🍆 Функции →]]

---
# 📜 Comprehensions (включения)
```toc
minLevel: 2
```
**Comprehensions** — это элегантный способ создания коллекций (списков, словарей, множеств) в одну строку. Это как "сжатый" цикл for с возможностью фильтрации и преобразования данных.

## Зачем нужны comprehensions?

### Было (обычный цикл):
```python
squares = []
for n in range(5):
    squares.append(n ** 2)
print(squares)  # [0, 1, 4, 9, 16]
```

### Стало (list comprehension):
```python
squares = [n ** 2 for n in range(5)]
print(squares)  # [0, 1, 4, 9, 16]
```

**Преимущества:**
- Код короче и понятнее
- Быстрее выполняется (обычно на 20-30%)
- Более "питоничный" стиль

---

## Списковые включения (List Comprehensions)

### Базовый синтаксис

```python
[выражение for элемент in последовательность]
```

**Схема работы:**
1. Берём каждый `элемент` из `последовательность`
2. Применяем к нему `выражение`
3. Собираем результаты в новый список

### Простые примеры

```python
# Квадраты чисел от 0 до 4
squares = [i ** 2 for i in range(5)]
print(squares)  # [0, 1, 4, 9, 16]

# Удвоение каждого числа
numbers = [1, 2, 3, 4, 5]
doubled = [n * 2 for n in numbers]
print(doubled)  # [2, 4, 6, 8, 10]

# Преобразование строк в заглавные буквы
words = ["hello", "world", "python"]
upper_words = [word.upper() for word in words]
print(upper_words)  # ['HELLO', 'WORLD', 'PYTHON']

# Длины слов
lengths = [len(word) for word in words]
print(lengths)  # [5, 5, 6]
```

### С условием (фильтрация)

```python
# Синтаксис:
[выражение for элемент in последовательность if условие]
```

```python
# Только чётные числа
numbers = range(10)
evens = [n for n in numbers if n % 2 == 0]
print(evens)  # [0, 2, 4, 6, 8]

# Только положительные числа
numbers = [-2, -1, 0, 1, 2, 3]
positives = [n for n in numbers if n > 0]
print(positives)  # [1, 2, 3]

# Слова длиннее 3 символов
words = ["cat", "elephant", "dog", "tiger"]
long_words = [word for word in words if len(word) > 3]
print(long_words)  # ['elephant', 'tiger']

# Квадраты нечётных чисел
odd_squares = [n ** 2 for n in range(10) if n % 2 != 0]
print(odd_squares)  # [1, 9, 25, 49, 81]
```

### Работа со строками

```python
# Извлечение всех чисел из строки
text = "У меня 3 яблока и 5 груш"
digits = [char for char in text if char.isdigit()]
print(digits)  # ['3', '5']

# Первые буквы слов
sentence = "Hello World from Python"
initials = [word[0] for word in sentence.split()]
print(initials)  # ['H', 'W', 'f', 'P']

# Гласные буквы в тексте
text = "hello world"
vowels = [char for char in text if char in 'aeiou']
print(vowels)  # ['e', 'o', 'o']
```

### Математические операции

```python
# Квадратные корни
numbers = [1, 4, 9, 16, 25]
roots = [n ** 0.5 for n in numbers]
print(roots)  # [1.0, 2.0, 3.0, 4.0, 5.0]

# Проценты
values = [100, 200, 300]
percentages = [v * 0.15 for v in values]  # 15% от каждого
print(percentages)  # [15.0, 30.0, 45.0]

# Факториалы (требуется import math)
import math
numbers = [1, 2, 3, 4, 5]
factorials = [math.factorial(n) for n in numbers]
print(factorials)  # [1, 2, 6, 24, 120]
```

### Вложенные списковые включения

Используются для работы с двумерными структурами (матрицами).

```python
# Создание матрицы 3×4
matrix = [[i * 4 + j for j in range(4)] for i in range(3)]
print(matrix)
# [[0, 1, 2, 3], [4, 5, 6, 7], [8, 9, 10, 11]]

# Сглаживание матрицы (из 2D в 1D)
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
flat = [item for row in matrix for item in row]
print(flat)  # [1, 2, 3, 4, 5, 6, 7, 8, 9]

# Транспонирование матрицы
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
transposed = [[row[i] for row in matrix] for i in range(len(matrix[0]))]
print(transposed)
# [[1, 4, 7], [2, 5, 8], [3, 6, 9]]
```

**Как читать вложенные for:**
```python
# Эквивалентный код для flat:
flat = []
for row in matrix:        # внешний for
    for item in row:      # внутренний for
        flat.append(item)
```

### С if-else (тернарное выражение)

```python
# Синтаксис:
[значение_если_истина if условие else значение_если_ложь for элемент in последовательность]
```

```python
# Чётные делаем отрицательными, нечётные оставляем
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
result = [n if n % 2 != 0 else -n for n in numbers]
print(result)  # [1, -2, 3, -4, 5, -6, 7, -8, 9, -10]

# Замена None на 0
data = [1, None, 3, None, 5]
cleaned = [x if x is not None else 0 for x in data]
print(cleaned)  # [1, 0, 3, 0, 5]

# Оценки: "pass" или "fail"
grades = [45, 67, 89, 34, 92]
results = ["pass" if g >= 50 else "fail" for g in grades]
print(results)  # ['fail', 'pass', 'pass', 'fail', 'pass']
```

---

## Словарные включения (Dict Comprehensions)

### Базовый синтаксис

```python
{ключ: значение for элемент in последовательность}
```

### Простые примеры

```python
# Квадраты чисел
squares_dict = {i: i ** 2 for i in range(5)}
print(squares_dict)  # {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# Длины слов
words = ["python", "java", "c"]
lengths = {word: len(word) for word in words}
print(lengths)  # {'python': 6, 'java': 4, 'c': 1}

# Словарь с одинаковыми значениями
keys = ['a', 'b', 'c']
default_dict = {key: 0 for key in keys}
print(default_dict)  # {'a': 0, 'b': 0, 'c': 0}
```

### Обращение словаря

Меняем ключи и значения местами.

```python
original = {"a": 1, "b": 2, "c": 3}
reversed_dict = {v: k for k, v in original.items()}
print(reversed_dict)  # {1: 'a', 2: 'b', 3: 'c'}

# Реальный пример: коды стран
country_codes = {"RU": "Россия", "US": "США", "GB": "Великобритания"}
code_by_country = {v: k for k, v in country_codes.items()}
print(code_by_country)
# {'Россия': 'RU', 'США': 'US', 'Великобритания': 'GB'}
```

### С условием

```python
numbers = range(10)
even_squares = {i: i ** 2 for i in numbers if i % 2 == 0}
print(even_squares)  # {0: 0, 2: 4, 4: 16, 6: 36, 8: 64}

# Фильтрация словаря
grades = {"Анна": 85, "Борис": 92, "Вера": 78, "Георгий": 95}
high_grades = {name: grade for name, grade in grades.items() if grade >= 90}
print(high_grades)  # {'Борис': 92, 'Георгий': 95}

# Удаление пустых значений
data = {"a": 1, "b": None, "c": 3, "d": None}
cleaned = {k: v for k, v in data.items() if v is not None}
print(cleaned)  # {'a': 1, 'c': 3}
```

### Преобразование значений

```python
prices_usd = {"apple": 1.5, "banana": 0.8, "orange": 2.0}
exchange_rate = 75

# Конвертация в рубли
prices_rub = {item: price * exchange_rate for item, price in prices_usd.items()}
print(prices_rub)  # {'apple': 112.5, 'banana': 60.0, 'orange': 150.0}

# Округление цен
prices_rounded = {item: round(price) for item, price in prices_rub.items()}
print(prices_rounded)  # {'apple': 112, 'banana': 60, 'orange': 150}
```

### Создание словаря из двух списков

```python
keys = ['name', 'age', 'city']
values = ['Иван', 30, 'Москва']

person = {k: v for k, v in zip(keys, values)}
print(person)  # {'name': 'Иван', 'age': 30, 'city': 'Москва'}

# Или просто: person = dict(zip(keys, values))
```

---

## Включения множеств (Set Comprehensions)

### Базовый синтаксис

```python
{выражение for элемент in последовательность}
```

**Важно:** Множество автоматически удаляет дубликаты!

### Простые примеры

```python
# Уникальные квадраты (включая отрицательные числа)
numbers = range(-5, 6)
unique_squares = {n ** 2 for n in numbers}
print(unique_squares)  # {0, 1, 4, 9, 16, 25}
# Обратите внимание: (-5)² = 5² = 25, поэтому дубликаты удалены

# Уникальные символы в тексте
text = "hello world"
unique_chars = {char for char in text if char != ' '}
print(unique_chars)  # {'h', 'e', 'l', 'o', 'w', 'r', 'd'}

# Уникальные длины слов
words = ["cat", "dog", "bird", "fish"]
lengths = {len(word) for word in words}
print(lengths)  # {3, 4}
```

### С условием

```python
# Уникальные длины слов больше 3
words = ["cat", "elephant", "dog", "tiger", "ant", "lion"]
lengths = {len(word) for word in words if len(word) > 3}
print(lengths)  # {4, 5, 8}

# Уникальные цифры в тексте
text = "Мой номер: 8-800-555-35-35"
digits = {char for char in text if char.isdigit()}
print(digits)  # {'0', '3', '5', '8'}
```

---

## Генераторные выражения (Generator Expressions)

Похожи на списковые включения, но используют круглые скобки и создают **генератор** (ленивое вычисление).

### Синтаксис

```python
(выражение for элемент in последовательность)
```

### Отличия от списковых включений

```python
# Списковое включение — создаёт список сразу
squares_list = [i ** 2 for i in range(1000000)]  # занимает ~8 МБ памяти

# Генераторное выражение — создаёт элементы по требованию
squares_gen = (i ** 2 for i in range(1000000))   # занимает ~200 байт

# Использование генератора
for square in squares_gen:
    if square > 100:
        break
    print(square)
```

### Когда использовать генераторы?

**Используй генераторы когда:**
- Данных много и они не поместятся в память
- Нужно только один раз пройтись по данным
- Не нужен весь список сразу

**Используй списковые включения когда:**
- Нужно несколько раз обращаться к данным
- Данные небольшие
- Нужен именно список

### Применение

```python
# Сумма квадратов
total = sum(i ** 2 for i in range(10))
print(total)  # 285

# Максимальная длина слова
words = ["python", "java", "javascript", "c"]
max_length = max(len(word) for word in words)
print(max_length)  # 10

# Проверка условия для всех элементов
numbers = [2, 4, 6, 8, 10]
all_even = all(n % 2 == 0 for n in numbers)
print(all_even)  # True

# Есть ли хотя бы одно отрицательное число?
numbers = [1, 2, -3, 4, 5]
has_negative = any(n < 0 for n in numbers)
print(has_negative)  # True
```

---

## Практические примеры

### Обработка данных

```python
# Студенты и их оценки
students = [
    {"name": "Анна", "grades": [5, 4, 5]},
    {"name": "Борис", "grades": [4, 5, 4]},
    {"name": "Вера", "grades": [3, 4, 3]}
]

# Средние оценки
averages = {s["name"]: sum(s["grades"]) / len(s["grades"]) for s in students}
print(averages)
# {'Анна': 4.666..., 'Борис': 4.333..., 'Вера': 3.333...}

# Отличники (средний балл >= 4.5)
top_students = [s["name"] for s in students 
                if sum(s["grades"]) / len(s["grades"]) >= 4.5]
print(top_students)  # ['Анна']
```

### Работа со строками

```python
sentence = "Hello World from Python"

# Слова длиннее 4 символов
long_words = [word for word in sentence.split() if len(word) > 4]
print(long_words)  # ['Hello', 'World', 'Python']

# Первые буквы слов
initials = [word[0] for word in sentence.split()]
print(initials)  # ['H', 'W', 'f', 'P']

# Все слова в нижнем регистре без дубликатов
unique_words = {word.lower() for word in sentence.split()}
print(unique_words)  # {'hello', 'world', 'from', 'python'}
```

### Преобразование структур данных

```python
# Список кортежей в словарь
pairs = [("name", "Иван"), ("age", 30), ("city", "Москва")]
person = {k: v for k, v in pairs}
print(person)  # {'name': 'Иван', 'age': 30, 'city': 'Москва'}

# Список словарей в словарь словарей
users = [
    {"id": 1, "name": "Анна"},
    {"id": 2, "name": "Борис"},
    {"id": 3, "name": "Вера"}
]
users_by_id = {user["id"]: user for user in users}
print(users_by_id)
# {1: {'id': 1, 'name': 'Анна'}, 2: {...}, 3: {...}}
```

### Фильтрация и преобразование

```python
# Чётные числа, возведённые в квадрат
numbers = range(10)
even_squares = [n ** 2 for n in numbers if n % 2 == 0]
print(even_squares)  # [0, 4, 16, 36, 64]

# Имена, начинающиеся с 'A'
names = ["Anna", "Boris", "Alexander", "Vera", "Andrew"]
a_names = [name for name in names if name.startswith('A')]
print(a_names)  # ['Anna', 'Alexander', 'Andrew']

# Цены со скидкой 20%
prices = {"яблоко": 100, "банан": 50, "апельсин": 80}
discounted = {item: price * 0.8 for item, price in prices.items()}
print(discounted)
# {'яблоко': 80.0, 'банан': 40.0, 'апельсин': 64.0}
```

### Математические операции

```python
# Таблица умножения
multiplication_table = {
    f"{i}×{j}": i * j 
    for i in range(1, 6) 
    for j in range(1, 6)
}
# {'1×1': 1, '1×2': 2, ..., '5×5': 25}

# Декартово произведение (все комбинации)
colors = ["red", "blue"]
sizes = ["S", "M", "L"]
products = [f"{color}-{size}" for color in colors for size in sizes]
print(products)
# ['red-S', 'red-M', 'red-L', 'blue-S', 'blue-M', 'blue-L']
```

---

## Сложные примеры

### Множественные условия

```python
# Числа, кратные 2 или 3, но не обоим сразу
numbers = range(20)
result = [n for n in numbers 
          if (n % 2 == 0 or n % 3 == 0) and not (n % 2 == 0 and n % 3 == 0)]
print(result)
# [2, 3, 4, 8, 9, 10, 14, 15, 16]
```

### Вложенные циклы с условиями

```python
# Пары чисел, сумма которых равна 10
pairs = [(i, j) for i in range(11) for j in range(11) if i + j == 10 and i <= j]
print(pairs)
# [(0, 10), (1, 9), (2, 8), (3, 7), (4, 6), (5, 5)]

# Координаты точек в прямоугольнике
points = [(x, y) for x in range(5) for y in range(3)]
print(points)
# [(0, 0), (0, 1), (0, 2), (1, 0), (1, 1), ...]
```

---

## Когда использовать comprehensions?

### ✅ Хорошо подходит для:

**Простых преобразований:**
```python
# Хорошо
squares = [x ** 2 for x in range(10)]
upper_words = [word.upper() for word in words]
```

**Фильтрации:**
```python
# Хорошо
positives = [n for n in numbers if n > 0]
adults = [person for person in people if person["age"] >= 18]
```

**Создания словарей и множеств:**
```python
# Хорошо
word_lengths = {word: len(word) for word in words}
unique_letters = {char for char in text if char.isalpha()}
```

### ❌ Не стоит использовать для:

**Сложной логики:**
```python
# Плохо — слишком сложно читать
result = [complex_function(x, y, z) for x in range(10) 
          for y in range(10) for z in range(10) 
          if condition1(x) and condition2(y) and condition3(z)]

# Лучше использовать обычный цикл
result = []
for x in range(10):
    for y in range(10):
        for z in range(10):
            if condition1(x) and condition2(y) and condition3(z):
                result.append(complex_function(x, y, z))
```

**Побочных эффектов:**
```python
# Плохо — включения для вычислений, а не для создания списка
[print(x) for x in numbers]  # НЕ ДЕЛАЙТЕ ТАК!

# Правильно
for x in numbers:
    print(x)
```

**Слишком больших данных:**
```python
# Плохо для больших данных
big_list = [process(x) for x in huge_dataset]

# Лучше — используй генератор
big_gen = (process(x) for x in huge_dataset)
```

---

## Производительность

### Comprehensions vs циклы

```python
import timeit

# Списковое включение (быстрее)
def with_comprehension():
    return [i ** 2 for i in range(1000)]

# Обычный цикл (медленнее)
def with_loop():
    result = []
    for i in range(1000):
        result.append(i ** 2)
    return result

# Включения обычно быстрее на 20-30%
```

### Генераторы vs списки

```python
# Для больших данных генераторы эффективнее по памяти
import sys

# Список — вся память сразу
list_comp = [i ** 2 for i in range(1000000)]
print(sys.getsizeof(list_comp))  # ~8 МБ

# Генератор — минимум памяти
gen_exp = (i ** 2 for i in range(1000000))
print(sys.getsizeof(gen_exp))  # ~200 байт
```

---

## Частые ошибки

### ❌ Слишком сложные включения

```python
# Плохо — нечитаемо
result = [[f(x, y) for x in range(10) if g(x)] for y in range(10) if h(y)]

# Лучше — разбить на части или использовать циклы
```

### ❌ Побочные эффекты

```python
# Плохо — включения не для этого
[file.write(line) for line in lines]

# Правильно
for line in lines:
    file.write(line)
```

### ❌ Забыть про генераторы для больших данных

```python
# Плохо для больших данных — съест всю память
big_list = [process(x) for x in huge_dataset]

# Лучше — используй генератор
big_gen = (process(x) for x in huge_dataset)
```

---

## Сравнение подходов

### Списковое включение vs map/filter

```python
numbers = [1, 2, 3, 4, 5]

# С включением (более питоничн)
squares = [x ** 2 for x in numbers]
evens = [x for x in numbers if x % 2 == 0]

# С map/filter (менее питоничный стиль в Python)
squares = list(map(lambda x: x ** 2, numbers))
evens = list(filter(lambda x: x % 2 == 0, numbers))
```

---

## Связанные темы

- [[04_2_0_for_loop|Цикл for]] — основа для понимания включений
- [[04_1_1_ternary|Тернарный оператор]] — часто используется в включениях
- [[04_3_0_range|range()]], [[04_3_1_enumerate|enumerate()]], [[04_3_2_zip|zip()]] — часто применяются вместе

---

## Итоги

Comprehensions — это мощный инструмент Python для:
- ✅ Компактного создания коллекций
- ✅ Читаемого кода для простых операций
- ✅ Повышения производительности

**Но помните о читаемости** — если включение становится слишком сложным, лучше использовать обычный цикл!

**Главное правило:** Код должен быть понятен не только компьютеру, но и другим программистам (и вам самим через месяц! 😊)

---

[[04_3_2_zip|← 🤐 zip() — объединение последовательностей]] | [[06_functions/06_00_intro|🍆 Функции →]]
