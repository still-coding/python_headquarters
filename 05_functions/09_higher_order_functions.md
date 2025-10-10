[[08_lambda_functions|← λ Lambda-функции]] | [[10_recursion|🔄 Рекурсия →]]

---
# 🎭 Функции высших порядков
```toc
minLevel: 2
```

## Что такое функция высшего порядка?

**Функция высшего порядка** (Higher-Order Function) — это функция, которая:
1. Принимает другую функцию как аргумент, **ИЛИ**
2. Возвращает функцию как результат

В Python функции — это **объекты первого класса** (first-class objects), что означает:
- Функции можно присваивать переменным
- Функции можно передавать как аргументы
- Функции можно возвращать из других функций
- Функции можно хранить в структурах данных

## Функции как объекты

### Присваивание функции переменной

```python
def greeting():
    return "Привет!"

# Функция — это объект
greet = greeting

print(type(greeting))  # <class 'function'>
print(greet())         # Привет!
print(greeting())      # Привет!
```

### Функции в структурах данных

```python
def add(a, b):
    return a + b

def multiply(a, b):
    return a * b

def subtract(a, b):
    return a - b

# Храним функции в словаре
operations = {
    "add": add,
    "multiply": multiply,
    "subtract": subtract
}

# Вызываем через словарь
print(operations["add"](10, 5))        # 15
print(operations["multiply"](10, 5))   # 50
```

## Функции как аргументы

### Простой пример

```python
def apply_twice(function, value):
    """
    Применяет функцию к значению дважды.
    
    Args:
        function: Функция, принимающая один аргумент
        value: Начальное значение
    
    Returns:
        Результат двойного применения функции
    """
    result = function(value)
    result = function(result)
    return result

def double(x):
    return x * 2

print(apply_twice(double, 3))  # 12
# Первый вызов: double(3) = 6
# Второй вызов: double(6) = 12
```

### Практический пример: обработка данных

```python
def process_list(items, processor):
    """
    Применяет обработчик к каждому элементу списка.
    
    Args:
        items: Список элементов
        processor: Функция для обработки каждого элемента
    
    Returns:
        Новый список с обработанными элементами
    """
    return [processor(item) for item in items]

def square(x):
    return x ** 2

numbers = [1, 2, 3, 4, 5]
squares = process_list(numbers, square)
print(squares)  # [1, 4, 9, 16, 25]

# С lambda
doubled = process_list(numbers, lambda x: x * 2)
print(doubled)  # [2, 4, 6, 8, 10]
```

## Встроенные функции высших порядков

Python предоставляет несколько встроенных функций высших порядков.

### `map()` — применить функцию к каждому элементу

```python
def square(x):
    return x ** 2

numbers = [1, 2, 3, 4, 5]
squares = list(map(square, numbers))
print(squares)  # [1, 4, 9, 16, 25]

# С lambda
cubes = list(map(lambda x: x ** 3, numbers))
print(cubes)  # [1, 8, 27, 64, 125]

# С несколькими итерируемыми объектами
a = [1, 2, 3]
b = [10, 20, 30]
sums = list(map(lambda x, y: x + y, a, b))
print(sums)  # [11, 22, 33]
```

### `filter()` — отфильтровать элементы

```python
def is_even(x):
    return x % 2 == 0

numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
even = list(filter(is_even, numbers))
print(even)  # [2, 4, 6, 8, 10]

# С lambda
greater_than_5 = list(filter(lambda x: x > 5, numbers))
print(greater_than_5)  # [6, 7, 8, 9, 10]
```

### `reduce()` — свернуть список в одно значение

```python
from functools import reduce

def add(a, b):
    return a + b

numbers = [1, 2, 3, 4, 5]
total = reduce(add, numbers)
print(total)  # 15
# Работает так: ((((1 + 2) + 3) + 4) + 5) = 15

# С lambda: произведение всех чисел
product = reduce(lambda a, b: a * b, numbers)
print(product)  # 120

# С начальным значением
total_with_initial = reduce(lambda a, b: a + b, numbers, 100)
print(total_with_initial)  # 115 (100 + 1 + 2 + 3 + 4 + 5)
```

### `sorted()` с параметром `key`

```python
words = ["яблоко", "банан", "киви", "апельсин"]

# Сортировка по длине
by_length = sorted(words, key=lambda word: len(word))
print(by_length)  # ['киви', 'банан', 'яблоко', 'апельсин']

# Сортировка по последней букве
by_last = sorted(words, key=lambda word: word[-1])
print(by_last)  # ['банан', 'киви', 'яблоко', 'апельсин']

# Сложные объекты
students = [
    {"name": "Anna", "score": 85},
    {"name": "Ivan", "score": 92},
    {"name": "Maria", "score": 78}
]

by_score = sorted(students, key=lambda s: s["score"], reverse=True)
for student in by_score:
    print(student)
# {'name': 'Ivan', 'score': 92}
# {'name': 'Anna', 'score': 85}
# {'name': 'Maria', 'score': 78}
```

## Возвращаемые функции

### Простой пример

```python
def create_multiplier(factor):
    """
    Создаёт функцию, которая умножает на заданное число.
    
    Args:
        factor: Число, на которое будет умножаться
    
    Returns:
        Функция, принимающая один аргумент
    """
    def multiply(x):
        return x * factor
    
    return multiply

# Создаём разные множители
multiply_by_2 = create_multiplier(2)
multiply_by_10 = create_multiplier(10)

print(multiply_by_2(5))   # 10
print(multiply_by_10(5))  # 50
print(multiply_by_2(7))   # 14
```

### Замыкание (Closure)

**Замыкание** — это функция, которая "запоминает" переменные из внешней области видимости.

```python
def create_counter():
    """Создаёт функцию-счётчик с внутренним состоянием"""
    count = 0
    
    def increment():
        nonlocal count
        count += 1
        return count
    
    return increment

counter1 = create_counter()
counter2 = create_counter()

print(counter1())  # 1
print(counter1())  # 2
print(counter1())  # 3

print(counter2())  # 1 — у каждого счётчика своё состояние
print(counter2())  # 2
```

### Практический пример: конфигурируемые функции

```python
def create_validator(minimum, maximum):
    """
    Создаёт функцию-валидатор для диапазона значений.
    
    Args:
        minimum: Минимально допустимое значение
        maximum: Максимально допустимое значение
    
    Returns:
        Функция-валидатор
    """
    def validate(value):
        return minimum <= value <= maximum
    
    return validate

# Создаём валидаторы для разных диапазонов
check_age = create_validator(0, 150)
check_percent = create_validator(0, 100)

print(check_age(25))     # True
print(check_age(200))    # False

print(check_percent(50))   # True
print(check_percent(150))  # False
```

## Декораторы — продвинутые функции высших порядков

**Декоратор** — это функция, которая принимает функцию и возвращает модифицированную функцию.

### Базовый пример

```python
def logger(function):
    """Декоратор, который логирует вызовы функции"""
    def wrapper(*args, **kwargs):
        print(f"Вызов функции {function.__name__} с аргументами {args}")
        result = function(*args, **kwargs)
        print(f"Функция {function.__name__} вернула {result}")
        return result
    
    return wrapper

@logger
def add(a, b):
    return a + b

result = add(3, 5)
# Вызов функции add с аргументами (3, 5)
# Функция add вернула 8
print(result)  # 8
```

**Синтаксис `@декоратор` эквивалентен:**
```python
def add(a, b):
    return a + b

add = logger(add)
```

### Декоратор с параметрами

```python
def repeat(times):
    """Декоратор, который повторяет функцию несколько раз"""
    def decorator(function):
        def wrapper(*args, **kwargs):
            results = []
            for _ in range(times):
                results.append(function(*args, **kwargs))
            return results
        return wrapper
    return decorator

@repeat(3)
def greeting(name):
    return f"Привет, {name}!"

print(greeting("Anna"))
# ['Привет, Anna!', 'Привет, Anna!', 'Привет, Anna!']
```

## Композиция функций

**Композиция** — это применение нескольких функций последовательно.

```python
def compose(*functions):
    """
    Создаёт композицию функций.
    
    Args:
        *functions: Функции для композиции (применяются справа налево)
    
    Returns:
        Композицию функций
    """
    def composition(x):
        result = x
        for function in reversed(functions):
            result = function(result)
        return result
    
    return composition

# Создаём функции
double = lambda x: x * 2
add_10 = lambda x: x + 10
square = lambda x: x ** 2

# Композиция: сначала удвоить, потом прибавить 10, потом возвести в квадрат
process = compose(square, add_10, double)

print(process(5))  # ((5 * 2) + 10) ** 2 = 400
```

## 🎯 Практические примеры

### Пример 1: Обработчик данных с цепочкой функций

```python
def create_processor(*transformations):
    """Создаёт обработчик с цепочкой преобразований"""
    def process(data):
        result = data
        for transformation in transformations:
            result = transformation(result)
        return result
    
    return process

# Преобразования для строк
string_processor = create_processor(
    lambda s: s.strip(),           # Убрать пробелы
    lambda s: s.lower(),           # В нижний регистр
    lambda s: s.replace(" ", "_")  # Пробелы в подчёркивания
)

print(string_processor("  Hello World  "))  # "hello_world"

# Преобразования для чисел
number_processor = create_processor(
    lambda x: abs(x),              # Абсолютное значение
    lambda x: x * 2,               # Удвоить
    lambda x: x + 10               # Прибавить 10
)

print(number_processor(-5))  # 20
# abs(-5) = 5, 5 * 2 = 10, 10 + 10 = 20
```

### Пример 2: Кеширование результатов

```python
def memoization(function):
    """Декоратор для кеширования результатов функции"""
    cache = {}
    
    def wrapper(*args):
        if args not in cache:
            print(f"Вычисление для {args}")
            cache[args] = function(*args)
        else:
            print(f"Результат из кеша для {args}")
        return cache[args]
    
    return wrapper

@memoization
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

print(fibonacci(10))
# Вычисления происходят только один раз для каждого n
```

### Пример 3: Валидация с цепочкой правил

```python
def create_validator(*rules):
    """
    Создаёт валидатор с несколькими правилами.
    
    Args:
        *rules: Функции-валидаторы, возвращающие (bool, сообщение)
    
    Returns:
        Функция-валидатор
    """
    def validate(value):
        for rule in rules:
            valid, message = rule(value)
            if not valid:
                return False, message
        return True, "Валидация пройдена"
    
    return validate

# Правила валидации пароля
not_empty = lambda s: (bool(s), "Пароль не может быть пустым")
min_length = lambda s: (len(s) >= 8, "Пароль должен быть не менее 8 символов")
has_digit = lambda s: (any(c.isdigit() for c in s), "Пароль должен содержать цифру")
has_upper = lambda s: (any(c.isupper() for c in s), "Пароль должен содержать заглавную букву")

# Создаём валидатор
check_password = create_validator(
    not_empty,
    min_length,
    has_digit,
    has_upper
)

# Тестируем
valid, message = check_password("Pass123")
print(f"{'✓' if valid else '✗'} {message}")
```

### Пример 4: Конвейер обработки данных

```python
def pipeline(*stages):
    """Создаёт конвейер обработки данных"""
    def process(data):
        result = data
        for stage in stages:
            result = stage(result)
        return result
    
    return process

# Этапы обработки
filter_positive = lambda numbers: [x for x in numbers if x > 0]
double_all = lambda numbers: [x * 2 for x in numbers]
sum_all = lambda numbers: sum(numbers)

# Создаём конвейер
process_numbers = pipeline(
    filter_positive,
    double_all,
    sum_all
)

numbers = [-5, 3, -2, 7, -1, 4]
result = process_numbers(numbers)
print(result)  # 28
# Положительные: [3, 7, 4]
# Удвоенные: [6, 14, 8]
# Сумма: 28
```

## 💡 Лучшие практики

### ✅ Хорошие практики

1. **Используйте функции высших порядков для абстракции**
   ```python
   def apply_operation(items, operation):
       return [operation(item) for item in items]
   ```

2. **Создавайте фабрики функций**
   ```python
   def create_validator(min_val, max_val):
       return lambda x: min_val <= x <= max_val
   ```

3. **Используйте декораторы для cross-cutting concerns**
   ```python
   @logger
   @timer
   def important_function():
       pass
   ```

### ❌ Плохие практики

1. **Слишком сложные цепочки функций**
   ```python
   # ❌ Трудно читать
   result = reduce(lambda x, f: f(x), [f1, f2, f3, f4, f5], data)
   
   # ✅ Лучше явно
   result = data
   result = f1(result)
   result = f2(result)
   ```

2. **Злоупотребление lambda в сложных случаях**
   ```python
   # ❌ Сложная lambda
   process = lambda x: x * 2 + 10 if x > 0 else abs(x) * 3
   
   # ✅ Используйте def
   def process(x):
       if x > 0:
           return x * 2 + 10
       return abs(x) * 3
   ```

## 🔎 Проверка на ходу

Эксперименты в REPL:

```python
>>> add = lambda x, y: x + y
>>> multiply = lambda x, y: x * y
>>>
>>> operations = [add, multiply]
>>> operations[0](3, 4)
7
>>> operations[1](3, 4)
12
>>>
>>> def create_adder(n):
...     return lambda x: x + n
...
>>> add_5 = create_adder(5)
>>> add_5(10)
15
```

## Резюме

**Функции высших порядков:**
- Принимают функции как аргументы
- Возвращают функции как результаты
- Позволяют создавать абстракции и переиспользовать код

**Встроенные функции:**
- `map(функция, итерируемый)` — применить функцию
- `filter(функция, итерируемый)` — отфильтровать
- `reduce(функция, итерируемый)` — свернуть
- `sorted(итерируемый, key=функция)` — сортировать

**Замыкания:**
- Функция "запоминает" переменные из внешней области
- Используются для создания функций с состоянием

**Декораторы:**
- Модифицируют поведение функций
- Синтаксис: `@декоратор`

**Преимущества:**
- Абстракция и переиспользование кода
- Создание гибких API
- Функциональный стиль программирования

---

[[08_lambda_functions|← λ Lambda-функции]] | [[10_recursion|🔄 Рекурсия →]]
