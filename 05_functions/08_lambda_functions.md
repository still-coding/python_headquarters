[[07_advanced_parameters|← ⚙️ Продвинутые параметры]] | [[09_higher_order_functions|🎭 Функции высших порядков →]]

---
# λ Lambda-функции
```toc
minLevel: 2
```

## Что такое lambda-функция?

**Lambda-функция** — это **анонимная функция** (без имени), которую можно записать в одну строку. Это удобный способ создать простую функцию прямо в месте её использования.

```python
# Обычная функция
def square(x):
    return x ** 2

# Lambda-функция (эквивалент)
square_lambda = lambda x: x ** 2

print(square(5))         # 25
print(square_lambda(5))  # 25
```

## Синтаксис lambda

```python
lambda параметры: выражение
```

**Важно:**
- `lambda` — ключевое слово
- `параметры` — один или несколько параметров (через запятую)
- `:` — разделитель
- `выражение` — **одно** выражение, результат которого возвращается автоматически
- Нет `return` — результат выражения возвращается автоматически
- Нет фигурных скобок и многострочности

## Простые примеры

### Lambda без параметров

```python
get_pi = lambda: 3.14159

print(get_pi())  # 3.14159
```

### Lambda с одним параметром

```python
double = lambda x: x * 2

print(double(5))   # 10
print(double(10))  # 20
```

### Lambda с несколькими параметрами

```python
add = lambda a, b: a + b

print(add(3, 4))   # 7
print(add(10, 20)) # 30
```

### Lambda со значениями по умолчанию

```python
power = lambda x, n=2: x ** n

print(power(5))     # 25 (5^2)
print(power(5, 3))  # 125 (5^3)
```

## Lambda — это выражение

Lambda — это **выражение**, а не оператор. Это значит, что lambda можно использовать везде, где ожидается выражение:

```python
# Прямо в списке
operations = [
    lambda x: x + 10,
    lambda x: x * 2,
    lambda x: x ** 2
]

result = operations[0](5)  # 15
print(result)

# Прямо в словаре
operations_dict = {
    "add": lambda a, b: a + b,
    "multiply": lambda a, b: a * b
}

print(operations_dict["add"](3, 4))       # 7
print(operations_dict["multiply"](3, 4))  # 12
```

## Типичные применения lambda

### 1. С функцией `sorted()`

```python
# Сортировка списка кортежей по второму элементу
pairs = [(1, 5), (3, 2), (2, 8), (4, 1)]

sorted_pairs = sorted(pairs, key=lambda pair: pair[1])
print(sorted_pairs)  # [(4, 1), (3, 2), (1, 5), (2, 8)]
```

```python
# Сортировка словарей
users = [
    {"name": "Anna", "age": 25},
    {"name": "Ivan", "age": 30},
    {"name": "Maria", "age": 22}
]

sorted_by_age = sorted(users, key=lambda u: u["age"])
print(sorted_by_age)
# [{'name': 'Maria', 'age': 22}, {'name': 'Anna', 'age': 25}, {'name': 'Ivan', 'age': 30}]
```

### 2. С функцией `map()`

```python
numbers = [1, 2, 3, 4, 5]

# Возведение в квадрат
squares = list(map(lambda x: x ** 2, numbers))
print(squares)  # [1, 4, 9, 16, 25]

# Преобразование в строки
strings = list(map(lambda x: f"Число: {x}", numbers))
print(strings)  # ['Число: 1', 'Число: 2', ...]
```

### 3. С функцией `filter()`

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Только чётные
even = list(filter(lambda x: x % 2 == 0, numbers))
print(even)  # [2, 4, 6, 8, 10]

# Только больше 5
greater_than_5 = list(filter(lambda x: x > 5, numbers))
print(greater_than_5)  # [6, 7, 8, 9, 10]
```

### 4. С функциями `max()` и `min()`

```python
words = ["apple", "banana", "cherry", "date"]

# Самое длинное слово
longest = max(words, key=lambda w: len(w))
print(longest)  # banana

# Самое короткое слово
shortest = min(words, key=lambda w: len(w))
print(shortest)  # date
```

## Lambda с условными выражениями

Lambda может содержать тернарный оператор (условное выражение):

```python
# Абсолютное значение
abs_value = lambda x: x if x >= 0 else -x

print(abs_value(5))   # 5
print(abs_value(-5))  # 5
```

```python
# Классификация по возрасту
classify_age = lambda age: "ребёнок" if age < 18 else "взрослый"

print(classify_age(15))  # ребёнок
print(classify_age(25))  # взрослый
```

```python
# Оценка по баллам
grade = lambda score: "A" if score >= 90 else "B" if score >= 80 else "C" if score >= 70 else "F"

print(grade(95))  # A
print(grade(85))  # B
print(grade(75))  # C
print(grade(65))  # F
```

## 🎯 Практические примеры

### Пример 1: Обработка данных API

```python
users_api = [
    {"id": 1, "name": "Anna", "age": 25},
    {"id": 2, "name": "Ivan", "age": 30},
    {"id": 3, "name": "Maria", "age": 22}
]

# Извлечь имена
names = list(map(lambda u: u["name"], users_api))
print(names)  # ['Anna', 'Ivan', 'Maria']

# Найти самого молодого
youngest = min(users_api, key=lambda u: u["age"])
print(youngest)  # {'id': 3, 'name': 'Maria', 'age': 22}

# Отфильтровать старше 23
older_than_23 = list(filter(lambda u: u["age"] > 23, users_api))
print(older_than_23)
```

### Пример 2: Конфигурация валидаторов

```python
validators = {
    "email": lambda s: "@" in s and "." in s,
    "phone": lambda s: s.isdigit() and len(s) == 10,
    "age": lambda n: 0 < n < 150
}

# Проверка
print(validators["email"]("user@example.com"))  # True
print(validators["phone"]("1234567890"))        # True
print(validators["age"](25))                    # True
```

### Пример 3: Динамические вычисления

```python
operations = {
    "+": lambda a, b: a + b,
    "-": lambda a, b: a - b,
    "*": lambda a, b: a * b,
    "/": lambda a, b: a / b if b != 0 else None,
    "**": lambda a, b: a ** b
}

def calculator(a, operation, b):
    if operation in operations:
        return operations[operation](a, b)
    return None

print(calculator(10, "+", 5))   # 15
print(calculator(10, "*", 5))   # 50
print(calculator(2, "**", 8))   # 256
```

### Пример 4: Цепочки обработки

```python
from functools import reduce

# Цепочка преобразований
processing = [
    lambda x: x.strip(),           # Убрать пробелы
    lambda x: x.lower(),           # В нижний регистр
    lambda x: x.replace(" ", "_")  # Пробелы в подчёркивания
]

def process_string(string, processors):
    return reduce(lambda s, func: func(s), processors, string)

result = process_string("  Hello World  ", processing)
print(result)  # "hello_world"
```

## Ограничения lambda

Lambda-функции имеют ограничения:

### ❌ Нельзя использовать операторы

```python
# ❌ Неправильно - нельзя использовать print
bad_lambda = lambda x: print(x)  # Работает, но плохая практика!

# ❌ Неправильно - нельзя использовать присваивание
bad_lambda = lambda x: y = x + 1  # SyntaxError

# ❌ Неправильно - нельзя использовать несколько выражений
bad_lambda = lambda x: (x + 1, x + 2, x + 3)  # Вернёт кортеж, но не то, что хотелось
```

### ❌ Только одно выражение

```python
# ❌ Неправильно - нельзя писать несколько строк
bad_lambda = lambda x:
    result = x * 2
    return result  # SyntaxError

# ✅ Правильно - используйте def
def multiply_by_two(x):
    result = x * 2
    return result
```

### ❌ Нет docstring

```python
# ❌ У lambda нет docstring
square = lambda x: x ** 2

# ✅ Используйте def, если нужна документация
def square(x):
    """Возвращает квадрат числа"""
    return x ** 2
```

## Когда использовать lambda, а когда def?

### ✅ Используйте lambda когда

- ✅ Функция простая и умещается в одну строку
- ✅ Функция используется один раз (callback)
- ✅ Логика очевидна без документации
- ✅ Нужна короткая функция для `sorted()`, `map()`, `filter()`, `max()`, `min()`

### ✅ Используйте def когда

- 📝 Функция будет использоваться многократно
- 📝 Функция требует документации
- 📝 Логика занимает больше одной строки
- 📝 Функция должна иметь понятное имя
- 📝 Нужна сложная обработка ошибок

## Сравнение: lambda vs def vs list comprehension

```python
numbers = [1, 2, 3, 4, 5]

# С lambda и map
squares_lambda = list(map(lambda x: x ** 2, numbers))

# С def и map
def square(x):
    return x ** 2
squares_def = list(map(square, numbers))

# С list comprehension (часто предпочтительнее)
squares_lc = [x ** 2 for x in numbers]

# Все дают одинаковый результат
print(squares_lambda)  # [1, 4, 9, 16, 25]
print(squares_def)     # [1, 4, 9, 16, 25]
print(squares_lc)      # [1, 4, 9, 16, 25]
```

**Когда что использовать:**
- **Lambda** — для коротких callback-функций (sorted, max с key и т.д.)
- **List comprehension** — для map/filter (обычно читабельнее)
- **def** — для всего остального

## 💡 Лучшие практики

### ✅ Хорошие практики

1. **Используйте lambda для простых операций**
   ```python
   sorted_users = sorted(users, key=lambda u: u["age"])
   ```

2. **Lambda для callback-функций**
   ```python
   button.on_click(lambda event: print("Clicked!"))
   ```

3. **Lambda в функциональном стиле**
   ```python
   numbers = [1, 2, 3, 4, 5]
   even = list(filter(lambda x: x % 2 == 0, numbers))
   ```

### ❌ Плохие практики

1. **Сложные lambda с множественными условиями**
   ```python
   # ❌ Плохо - слишком сложно
   classify = lambda x: "A" if x >= 90 else "B" if x >= 80 else "C" if x >= 70 else "D" if x >= 60 else "F"
   
   # ✅ Хорошо - используйте def
   def classify(score):
       if score >= 90: return "A"
       if score >= 80: return "B"
       if score >= 70: return "C"
       if score >= 60: return "D"
       return "F"
   ```

2. **Присваивание lambda переменной**
   ```python
   # ❌ Избегайте - лучше использовать def
   square = lambda x: x ** 2
   
   # ✅ Лучше
   def square(x):
       return x ** 2
   ```

3. **Lambda вместо list comprehension**
   ```python
   # ❌ Менее читабельно
   squares = list(map(lambda x: x ** 2, numbers))
   
   # ✅ Более читабельно
   squares = [x ** 2 for x in numbers]
   ```

## 🔎 Проверка на ходу

Эксперименты в REPL:

```python
>>> add = lambda x, y: x + y
>>> add(3, 5)
8

>>> numbers = [1, 2, 3, 4, 5]
>>> list(map(lambda x: x * 2, numbers))
[2, 4, 6, 8, 10]

>>> sorted([(1, 5), (3, 2), (2, 8)], key=lambda t: t[1])
[(3, 2), (1, 5), (2, 8)]
```

## Резюме

**Lambda-функция:**
- Анонимная функция в одну строку
- Синтаксис: `lambda параметры: выражение`
- Это **выражение**, не оператор
- Автоматически возвращает результат

**Когда использовать lambda:**
- ✅ Простые одноразовые функции
- ✅ Callback для функций высших порядков
- ✅ Короткие функции без сложной логики

**Когда использовать def:**
- ✅ Функции с документацией
- ✅ Сложная логика
- ✅ Многократное использование
- ✅ Понятное именование

**Альтернативы:**
- List comprehension часто лучше, чем map/filter с lambda
- Generator expression для больших данных

**Правило:** Если lambda занимает больше одной строки или трудно читается — используйте `def`!

---

[[07_advanced_parameters|← ⚙️ Продвинутые параметры]] | [[09_higher_order_functions|🎭 Функции высших порядков →]]
