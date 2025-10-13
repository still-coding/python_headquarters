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

### Фабрики функций

```python
def create_power_function(exponent):
    """
    Создаёт функцию для возведения в степень.
    
    Args:
        exponent: Показатель степени
    
    Returns:
        Функция для возведения числа в заданную степень
    """
    def power(base):
        return base ** exponent
    
    return power

square = create_power_function(2)
cube = create_power_function(3)

print(square(5))  # 25
print(cube(5))    # 125
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

### Пример 2: Конвейер обработки данных

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
passwords = ["Pass123", "pass", "PASSWORD", "Pass1234"]
for pwd in passwords:
    valid, message = check_password(pwd)
    print(f"{'✓' if valid else '✗'} '{pwd}': {message}")
```

### Пример 4: Создание условных функций

```python
def conditional_function(condition, true_func, false_func):
    """
    Создаёт функцию, которая выполняет разные действия в зависимости от условия.
    
    Args:
        condition: Функция-условие, возвращающая bool
        true_func: Функция для выполнения, если условие истинно
        false_func: Функция для выполнения, если условие ложно
    
    Returns:
        Функция, выполняющая условную логику
    """
    def execute(value):
        if condition(value):
            return true_func(value)
        else:
            return false_func(value)
    
    return execute

# Создаём функцию обработки чисел
process_number = conditional_function(
    condition=lambda x: x > 0,
    true_func=lambda x: x * 2,      # Положительные удваиваем
    false_func=lambda x: abs(x)     # Отрицательные делаем положительными
)

print(process_number(5))   # 10
print(process_number(-5))  # 5
print(process_number(0))   # 0
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

3. **Комбинируйте map, filter, reduce для обработки данных**
   ```python
   from functools import reduce
   
   numbers = [1, 2, 3, 4, 5]
   result = reduce(
       lambda a, b: a + b,
       filter(lambda x: x % 2 == 0, numbers)
   )
   ```

4. **Используйте функции высших порядков для конфигурации**
   ```python
   def create_formatter(prefix, suffix):
       def format_text(text):
           return f"{prefix}{text}{suffix}"
       return format_text
   
   html_bold = create_formatter("<b>", "</b>")
   html_italic = create_formatter("<i>", "</i>")
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

3. **Использование map/filter там, где лучше list comprehension**
   ```python
   # ❌ Менее читаемо
   result = list(map(lambda x: x ** 2, filter(lambda x: x > 0, numbers)))
   
   # ✅ Более читаемо
   result = [x ** 2 for x in numbers if x > 0]
   ```

## Когда использовать функции высших порядков

**Используйте, когда:**
- Нужна абстракция над операциями
- Хотите параметризовать поведение
- Создаёте гибкие API
- Реализуете функциональные паттерны

**Не используйте, когда:**
- Код становится менее читаемым
- Простой цикл понятнее
- List comprehension более выразительны

## Связь с другими концепциями

Для более глубокого понимания функций высших порядков изучите:
- [[11_closures_and_decorators|🎁 Замыкания и декораторы]] — продвинутые техники с функциями высших порядков
- [[10_recursion|🔄 Рекурсия]] — рекурсивные функции высших порядков
- [[08_lambda_functions|λ Lambda-функции]] — анонимные функции как аргументы

## 🔍 Проверка на ходу

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
>>> def apply_twice(func, x):
...     return func(func(x))
...
>>> apply_twice(lambda x: x * 2, 5)
20
>>>
>>> numbers = [1, 2, 3, 4, 5]
>>> list(map(lambda x: x ** 2, numbers))
[1, 4, 9, 16, 25]
>>> list(filter(lambda x: x > 2, numbers))
[3, 4, 5]
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

**Преимущества:**
- Абстракция и переиспользование кода
- Создание гибких API
- Функциональный стиль программирования
- Композиция и модульность

**Применение:**
- Обработка коллекций данных
- Создание конфигурируемых функций
- Построение pipeline для обработки данных
- Реализация функциональных паттернов

---

[[08_lambda_functions|← λ Lambda-функции]] | [[10_recursion|🔄 Рекурсия →]]
