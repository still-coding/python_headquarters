[[06_10_recursion|← 🔄 Рекурсия]] | [[07_00_intro|🏛️ Основы ООП →]]

---
# 🎁 Замыкания и декораторы
```toc
minLevel: 2
```

## Замыкания (Closures)

**Замыкание** — это функция, которая "запоминает" переменные из внешней области видимости, даже после того, как эта внешняя функция завершила выполнение.

### Базовый пример замыкания

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

### Как работают замыкания

```python
def outer_function(x):
    """Внешняя функция с переменной x"""
    
    def inner_function(y):
        """Внутренняя функция 'замыкает' переменную x"""
        return x + y
    
    return inner_function

# Создаём замыкание с x = 10
add_10 = outer_function(10)

print(add_10(5))   # 15 (10 + 5)
print(add_10(20))  # 30 (10 + 20)

# Создаём другое замыкание с x = 100
add_100 = outer_function(100)
print(add_100(5))  # 105 (100 + 5)
```

**Что происходит:**
1. `outer_function(10)` создаёт `inner_function`, которая помнит `x = 10`
2. Эта `inner_function` возвращается и присваивается `add_10`
3. Когда мы вызываем `add_10(5)`, она использует сохранённое значение `x = 10`

### Практические примеры замыканий

#### Пример 1: Конфигурируемые функции

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
check_temperature = create_validator(-273, 1000)

print(check_age(25))           # True
print(check_age(200))          # False

print(check_percent(50))       # True
print(check_percent(150))      # False

print(check_temperature(-300))  # False
print(check_temperature(25))    # True
```

#### Пример 2: Создание множителей

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
multiply_by_100 = create_multiplier(100)

print(multiply_by_2(5))    # 10
print(multiply_by_10(5))   # 50
print(multiply_by_100(5))  # 500
```

#### Пример 3: Приватное состояние

```python
def create_bank_account(initial_balance):
    """
    Создаёт банковский счёт с приватным балансом.
    
    Args:
        initial_balance: Начальный баланс
    
    Returns:
        Словарь с методами для работы со счётом
    """
    balance = initial_balance
    
    def deposit(amount):
        nonlocal balance
        if amount > 0:
            balance += amount
            return True
        return False
    
    def withdraw(amount):
        nonlocal balance
        if 0 < amount <= balance:
            balance -= amount
            return True
        return False
    
    def get_balance():
        return balance
    
    return {
        'deposit': deposit,
        'withdraw': withdraw,
        'get_balance': get_balance
    }

# Создаём счёт
account = create_bank_account(1000)

print(account['get_balance']())  # 1000
account['deposit'](500)
print(account['get_balance']())  # 1500
account['withdraw'](200)
print(account['get_balance']())  # 1300

# Прямого доступа к balance нет — это инкапсуляция!
```

### Ключевое слово `nonlocal`

```python
def outer():
    x = 0
    
    def inner_without_nonlocal():
        x = 1  # Создаёт НОВУЮ локальную переменную
        print(f"Inside inner_without_nonlocal: x = {x}")
    
    def inner_with_nonlocal():
        nonlocal x  # Использует переменную из outer()
        x = 1
        print(f"Inside inner_with_nonlocal: x = {x}")
    
    print(f"Before: x = {x}")
    inner_without_nonlocal()
    print(f"After inner_without_nonlocal: x = {x}")
    
    inner_with_nonlocal()
    print(f"After inner_with_nonlocal: x = {x}")

outer()
# Before: x = 0
# Inside inner_without_nonlocal: x = 1
# After inner_without_nonlocal: x = 0
# Inside inner_with_nonlocal: x = 1
# After inner_with_nonlocal: x = 1
```

## Декораторы — продвинутые функции высших порядков

**Декоратор** — это функция, которая принимает функцию и возвращает модифицированную функцию.

### Базовый пример декоратора

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

### Как работает декоратор

```python
def my_decorator(func):
    """Шаблон декоратора"""
    def wrapper(*args, **kwargs):
        # Код ДО выполнения функции
        print("Что-то делаем до")
        
        # Вызываем оригинальную функцию
        result = func(*args, **kwargs)
        
        # Код ПОСЛЕ выполнения функции
        print("Что-то делаем после")
        
        return result
    
    return wrapper

@my_decorator
def say_hello():
    print("Привет!")

say_hello()
# Что-то делаем до
# Привет!
# Что-то делаем после
```

### Практические примеры декораторов

#### Пример 1: Измерение времени выполнения

```python
import time

def timer(function):
    """Декоратор для измерения времени выполнения функции"""
    def wrapper(*args, **kwargs):
        start_time = time.time()
        result = function(*args, **kwargs)
        end_time = time.time()
        print(f"{function.__name__} выполнялась {end_time - start_time:.4f} секунд")
        return result
    
    return wrapper

@timer
def slow_function():
    time.sleep(2)
    return "Готово"

result = slow_function()
# slow_function выполнялась 2.0021 секунд
```

#### Пример 2: Проверка типов аргументов

```python
def validate_types(*expected_types):
    """Декоратор для проверки типов аргументов"""
    def decorator(function):
        def wrapper(*args, **kwargs):
            # Проверяем типы позиционных аргументов
            for arg, expected_type in zip(args, expected_types):
                if not isinstance(arg, expected_type):
                    raise TypeError(
                        f"Аргумент {arg} должен быть типа {expected_type}, "
                        f"но получен {type(arg)}"
                    )
            
            return function(*args, **kwargs)
        
        return wrapper
    return decorator

@validate_types(int, int)
def add(a, b):
    return a + b

print(add(3, 5))      # 8
# print(add("3", 5))  # TypeError!
```

#### Пример 3: Кеширование результатов (мемоизация)

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

print(fibonacci(5))
# Вычисления происходят только один раз для каждого n
```

#### Пример 4: Счётчик вызовов

```python
def count_calls(function):
    """Декоратор, подсчитывающий количество вызовов функции"""
    def wrapper(*args, **kwargs):
        wrapper.calls += 1
        print(f"Вызов #{wrapper.calls} функции {function.__name__}")
        return function(*args, **kwargs)
    
    wrapper.calls = 0
    return wrapper

@count_calls
def say_hello():
    return "Привет!"

say_hello()  # Вызов #1 функции say_hello
say_hello()  # Вызов #2 функции say_hello
say_hello()  # Вызов #3 функции say_hello

print(f"Всего вызовов: {say_hello.calls}")  # Всего вызовов: 3
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

**Как это работает:**
```python
# @repeat(3) эквивалентно:
def greeting(name):
    return f"Привет, {name}!"

greeting = repeat(3)(greeting)
```

### Множественные декораторы

```python
def bold(function):
    """Обёртывает результат в теги <b>"""
    def wrapper(*args, **kwargs):
        return f"<b>{function(*args, **kwargs)}</b>"
    return wrapper

def italic(function):
    """Обёртывает результат в теги <i>"""
    def wrapper(*args, **kwargs):
        return f"<i>{function(*args, **kwargs)}</i>"
    return wrapper

@bold
@italic
def greeting():
    return "Привет!"

print(greeting())  # <b><i>Привет!</i></b>
```

**Порядок применения:**
```python
# Декораторы применяются снизу вверх:
greeting = bold(italic(greeting))
```

### Использование `functools.wraps`

```python
from functools import wraps

def my_decorator(function):
    @wraps(function)  # Сохраняет метаданные оригинальной функции
    def wrapper(*args, **kwargs):
        """Обёртка для функции"""
        return function(*args, **kwargs)
    
    return wrapper

@my_decorator
def add(a, b):
    """Складывает два числа"""
    return a + b

print(add.__name__)  # add (без @wraps было бы wrapper)
print(add.__doc__)   # Складывает два числа (без @wraps было бы "Обёртка для функции")
```

## 🎯 Практические примеры

### Пример 1: Декоратор для авторизации

```python
def require_auth(function):
    """Декоратор, проверяющий авторизацию перед выполнением функции"""
    def wrapper(user, *args, **kwargs):
        if not user.get('is_authenticated'):
            raise PermissionError("Требуется авторизация")
        
        return function(user, *args, **kwargs)
    
    return wrapper

@require_auth
def view_profile(user):
    return f"Профиль пользователя: {user['name']}"

# Использование
authenticated_user = {'name': 'Anna', 'is_authenticated': True}
guest_user = {'name': 'Guest', 'is_authenticated': False}

print(view_profile(authenticated_user))  # Профиль пользователя: Anna
# print(view_profile(guest_user))  # PermissionError!
```

### Пример 2: Декоратор для повторных попыток

```python
import time
from functools import wraps

def retry(max_attempts=3, delay=1):
    """
    Декоратор для повторных попыток выполнения функции при ошибке.
    
    Args:
        max_attempts: Максимальное количество попыток
        delay: Задержка между попытками в секундах
    """
    def decorator(function):
        @wraps(function)
        def wrapper(*args, **kwargs):
            attempts = 0
            while attempts < max_attempts:
                try:
                    return function(*args, **kwargs)
                except Exception as e:
                    attempts += 1
                    if attempts >= max_attempts:
                        raise
                    print(f"Попытка {attempts} не удалась: {e}. Повтор через {delay}с...")
                    time.sleep(delay)
        
        return wrapper
    return decorator

@retry(max_attempts=3, delay=0.5)
def unreliable_function():
    import random
    if random.random() < 0.7:
        raise ConnectionError("Сбой соединения")
    return "Успех!"
```

### Пример 3: Декоратор для логирования с уровнями

```python
from functools import wraps
from datetime import datetime

def log(level="INFO"):
    """Декоратор для логирования с указанием уровня"""
    def decorator(function):
        @wraps(function)
        def wrapper(*args, **kwargs):
            timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
            print(f"[{timestamp}] [{level}] Вызов {function.__name__}")
            
            try:
                result = function(*args, **kwargs)
                print(f"[{timestamp}] [{level}] {function.__name__} завершилась успешно")
                return result
            except Exception as e:
                print(f"[{timestamp}] [ERROR] {function.__name__} вызвала ошибку: {e}")
                raise
        
        return wrapper
    return decorator

@log(level="DEBUG")
def calculate(x, y):
    return x / y

calculate(10, 2)
# [2024-01-15 10:30:45] [DEBUG] Вызов calculate
# [2024-01-15 10:30:45] [DEBUG] calculate завершилась успешно
```

## 💡 Лучшие практики

### ✅ Хорошие практики

1. **Используйте `@wraps` для сохранения метаданных**
   ```python
   from functools import wraps
   
   def my_decorator(func):
       @wraps(func)
       def wrapper(*args, **kwargs):
           return func(*args, **kwargs)
       return wrapper
   ```

2. **Используйте замыкания для создания конфигурируемых функций**
   ```python
   def create_validator(min_val, max_val):
       def validate(value):
           return min_val <= value <= max_val
       return validate
   ```

3. **Документируйте декораторы**
   ```python
   def my_decorator(function):
       """
       Описание того, что делает декоратор.
       
       Args:
           function: Функция для декорирования
       
       Returns:
           Декорированная функция
       """
       ...
   ```

### ❌ Плохие практики

1. **Не изменяйте глобальное состояние в замыканиях**
   ```python
   # ❌ Плохо
   global_counter = 0
   
   def create_counter():
       def count():
           global global_counter
           global_counter += 1
           return global_counter
       return count
   
   # ✅ Лучше
   def create_counter():
       counter = 0
       def count():
           nonlocal counter
           counter += 1
           return counter
       return count
   ```

2. **Не создавайте слишком сложные декораторы**
   ```python
   # ❌ Слишком сложно
   @decorator1
   @decorator2
   @decorator3(param1)
   @decorator4(param2, param3)
   def my_function():
       pass
   ```

## 🔍 Проверка на ходу

Эксперименты в REPL:

```python
>>> def create_multiplier(n):
...     return lambda x: x * n
...
>>> times_2 = create_multiplier(2)
>>> times_5 = create_multiplier(5)
>>>
>>> times_2(10)
20
>>> times_5(10)
50
>>>
>>> def simple_decorator(func):
...     def wrapper():
...         print("До")
...         func()
...         print("После")
...     return wrapper
...
>>> @simple_decorator
... def say_hello():
...     print("Привет!")
...
>>> say_hello()
До
Привет!
После
```

## Резюме

**Замыкания:**
- Функция "запоминает" переменные из внешней области видимости
- Используйте `nonlocal` для изменения внешних переменных
- Позволяют создавать функции с приватным состоянием
- Полезны для создания конфигурируемых функций

**Декораторы:**
- Модифицируют поведение функций
- Синтаксис: `@декоратор`
- Используйте `@wraps` для сохранения метаданных
- Могут принимать параметры через вложенные функции

**Преимущества:**
- Повторное использование кода
- Разделение ответственности
- Чистый и выразительный код
- Инкапсуляция состояния

**Применение:**
- Логирование
- Измерение производительности
- Кеширование
- Проверка прав доступа
- Валидация данных

## 📖 Связанные темы

- [[06_09_higher_order_functions|Функции высших порядков]] — декораторы построены на этой идее
- [[10_1_descriptors|Дескрипторы]] — другой механизм расширения поведения, на уровне атрибутов классов
- [[07_07_magic_methods|Магические методы]] — декораторы часто применяются к методам класса

---

[[06_10_recursion|← 🔄 Рекурсия]] | [[07_00_intro|🏛️ Основы ООП →]]
