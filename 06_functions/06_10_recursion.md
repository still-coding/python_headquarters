[[06_09_higher_order_functions|← 🎭 Функции высших порядков]] | [[06_11_closures_and_decorators|🎁 Замыкания и декораторы →]]

---
# 🔄 Рекурсия
```toc
minLevel: 2
```

## Что такое рекурсия?

**Рекурсия** — это когда функция вызывает саму себя.

```python
def countdown(n):
    if n <= 0:
        print("Пуск!")
    else:
        print(n)
        countdown(n - 1)  # Функция вызывает сама себя!

countdown(3)
# 3
# 2
# 1
# Пуск!
```

**Рекурсивная функция должна иметь:**
1. **Базовый случай** (base case) — условие остановки
2. **Рекурсивный случай** (recursive case) — вызов самой себя с изменёнными аргументами

## Базовый случай и рекурсивный случай

### Пример: факториал

```python
def factorial(n):
    """
    Вычисляет факториал числа n рекурсивно.
    
    n! = n × (n-1) × (n-2) × ... × 2 × 1
    
    Examples:
        >>> factorial(5)
        120
        >>> factorial(0)
        1
    """
    # Базовый случай
    if n <= 1:
        return 1
    
    # Рекурсивный случай
    return n * factorial(n - 1)

print(factorial(5))  # 120
# 5! = 5 × 4 × 3 × 2 × 1 = 120
```

**Как это работает:**
```
factorial(5)
= 5 * factorial(4)
= 5 * (4 * factorial(3))
= 5 * (4 * (3 * factorial(2)))
= 5 * (4 * (3 * (2 * factorial(1))))
= 5 * (4 * (3 * (2 * 1)))
= 5 * (4 * (3 * 2))
= 5 * (4 * 6)
= 5 * 24
= 120
```

## Классические примеры рекурсии

### Пример 1: Числа Фибоначчи

```python
def fibonacci(n):
    """
    Возвращает n-е число Фибоначчи.
    
    Последовательность: 0, 1, 1, 2, 3, 5, 8, 13, 21, ...
    
    Examples:
        >>> fibonacci(0)
        0
        >>> fibonacci(7)
        13
    """
    # Базовые случаи
    if n <= 1:
        return n
    
    # Рекурсивный случай
    return fibonacci(n - 1) + fibonacci(n - 2)

for i in range(10):
    print(fibonacci(i), end=" ")
# 0 1 1 2 3 5 8 13 21 34
```

### Пример 2: Сумма чисел от 1 до n

```python
def sum_naturals(n):
    """
    Возвращает сумму натуральных чисел от 1 до n.
    
    Examples:
        >>> sum_naturals(10)
        55
        >>> sum_naturals(100)
        5050
    """
    # Базовый случай
    if n == 0:
        return 0
    
    # Рекурсивный случай: n + сумма остальных
    return n + sum_naturals(n - 1)

print(sum_naturals(10))  # 55
```

### Пример 3: Сумма элементов списка

```python
def sum_list(numbers):
    """
    Возвращает сумму элементов списка рекурсивно.
    
    Examples:
        >>> sum_list([1, 2, 3, 4, 5])
        15
    """
    # Базовый случай: пустой список
    if not numbers:
        return 0
    
    # Рекурсивный случай: первый элемент + сумма остальных
    return numbers[0] + sum_list(numbers[1:])

print(sum_list([1, 2, 3, 4, 5]))  # 15
```

## Взаимная рекурсия

**Взаимная рекурсия** — когда несколько функций вызывают друг друга.

```python
def is_even(n):
    """Проверяет, является ли число чётным через взаимную рекурсию"""
    if n == 0:
        return True
    return is_odd(n - 1)

def is_odd(n):
    """Проверяет, является ли число нечётным через взаимную рекурсию"""
    if n == 0:
        return False
    return is_even(n - 1)

print(is_even(4))   # True
print(is_odd(4))    # False
print(is_even(7))   # False
print(is_odd(7))    # True
```

**Как это работает:**
```
is_even(4)
→ is_odd(3)
  → is_even(2)
    → is_odd(1)
      → is_even(0)
        → True
```

## Древовидная рекурсия

**Древовидная рекурсия** — когда функция делает несколько рекурсивных вызовов.

### Числа Фибоначчи (древовидная структура)

```python
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

# Визуализация вызовов для fibonacci(5):
#                    fibonacci(5)
#                   /            \
#          fibonacci(4)          fibonacci(3)
#           /        \            /        \
#    fibonacci(3)  fibonacci(2)  fibonacci(2)  fibonacci(1)
#     /      \      /      \      /      \
#  ...       ...  ...     ...   ...     ...
```

**Проблема:** много повторяющихся вычислений!

## Оптимизация рекурсии

### 1. Мемоизация (кеширование результатов)

```python
def fibonacci_cached(n, cache=None):
    """Фибоначчи с кешированием для избежания повторных вычислений"""
    if cache is None:
        cache = {}
    
    # Проверяем кеш
    if n in cache:
        return cache[n]
    
    # Базовые случаи
    if n <= 1:
        return n
    
    # Вычисляем и сохраняем в кеш
    result = fibonacci_cached(n - 1, cache) + fibonacci_cached(n - 2, cache)
    cache[n] = result
    
    return result

print(fibonacci_cached(100))  # Работает быстро!
```

### 2. Использование декоратора `@lru_cache`

```python
from functools import lru_cache

@lru_cache(maxsize=None)
def fibonacci_fast(n):
    """Фибоначчи с автоматическим кешированием"""
    if n <= 1:
        return n
    return fibonacci_fast(n - 1) + fibonacci_fast(n - 2)

print(fibonacci_fast(100))  # Очень быстро!
```

### 3. Хвостовая рекурсия (tail recursion)

**Хвостовая рекурсия** — когда рекурсивный вызов — последняя операция в функции.

```python
# ❌ Обычная рекурсия (не хвостовая)
def factorial(n):
    if n <= 1:
        return 1
    return n * factorial(n - 1)  # После рекурсии ещё умножение

# ✅ Хвостовая рекурсия
def factorial_tail(n, accumulator=1):
    if n <= 1:
        return accumulator
    return factorial_tail(n - 1, n * accumulator)  # Рекурсия — последнее действие

print(factorial_tail(5))  # 120
```

**Преимущество:** некоторые языки оптимизируют хвостовую рекурсию (Python не оптимизирует автоматически).

## Рекурсия vs итерация

### Сравнение: факториал

```python
# Рекурсивная версия
def factorial_recursive(n):
    if n <= 1:
        return 1
    return n * factorial_recursive(n - 1)

# Итеративная версия
def factorial_iterative(n):
    result = 1
    for i in range(1, n + 1):
        result *= i
    return result

# Обе дают одинаковый результат
print(factorial_recursive(5))  # 120
print(factorial_iterative(5))  # 120
```

**Когда использовать рекурсию:**
- ✅ Задача естественно рекурсивна (деревья, фракталы)
- ✅ Код проще и понятнее
- ✅ Глубина рекурсии небольшая

**Когда использовать итерацию:**
- ✅ Нужна производительность
- ✅ Большая глубина вложенности
- ✅ Простые повторяющиеся операции

## 🎯 Практические примеры

### Пример 1: Обход дерева каталогов

```python
import os

def print_files(path, indent=0):
    """Рекурсивно выводит все файлы и папки"""
    try:
        items = os.listdir(path)
        for item in items:
            full_path = os.path.join(path, item)
            print("  " * indent + item)
            
            if os.path.isdir(full_path):
                # Рекурсивный вызов для подпапок
                print_files(full_path, indent + 1)
    except PermissionError:
        print("  " * indent + "[нет доступа]")
```

### Пример 2: Вычисление степени

```python
def power(base, exponent):
    """
    Вычисляет base^exponent рекурсивно.
    
    Examples:
        >>> power(2, 10)
        1024
        >>> power(5, 3)
        125
    """
    # Базовый случай
    if exponent == 0:
        return 1
    
    # Рекурсивный случай
    if exponent > 0:
        return base * power(base, exponent - 1)
    else:
        return 1 / power(base, -exponent)

print(power(2, 10))  # 1024
print(power(5, 3))   # 125
```

### Пример 3: Проверка палиндрома

```python
def is_palindrome(text):
    """
    Проверяет, является ли текст палиндромом (рекурсивно).
    
    Examples:
        >>> is_palindrome("радар")
        True
        >>> is_palindrome("python")
        False
    """
    # Убираем пробелы и приводим к нижнему регистру
    text = text.replace(" ", "").lower()
    
    # Базовые случаи
    if len(text) <= 1:
        return True
    
    # Рекурсивный случай: первый == последнему И середина — палиндром
    if text[0] != text[-1]:
        return False
    
    return is_palindrome(text[1:-1])

print(is_palindrome("радар"))      # True
print(is_palindrome("А роза упала на лапу Азора"))  # True
print(is_palindrome("python"))     # False
```

### Пример 4: Плоский список из вложенного

```python
def flatten(nested_list):
    """
    Преобразует вложенный список в плоский рекурсивно.
    
    Examples:
        >>> flatten([1, [2, 3], [[4], 5]])
        [1, 2, 3, 4, 5]
        >>> flatten([1, [2, [3, [4, [5]]]]])
        [1, 2, 3, 4, 5]
    """
    result = []
    
    for item in nested_list:
        if isinstance(item, list):
            # Рекурсивно распрямляем вложенный список
            result.extend(flatten(item))
        else:
            result.append(item)
    
    return result

print(flatten([1, [2, 3], [[4], 5]]))  # [1, 2, 3, 4, 5]
```

## Ограничения рекурсии в Python

Python имеет ограничение на **глубину рекурсии** (по умолчанию ~1000):

```python
import sys

print(sys.getrecursionlimit())  # 1000 (обычно)

# Можно изменить (осторожно!)
# sys.setrecursionlimit(5000)
```

**При превышении лимита:**
```python
def infinite_recursion(n):
    return infinite_recursion(n + 1)

# infinite_recursion(0)  # RecursionError: maximum recursion depth exceeded
```

## 💡 Лучшие практики

### ✅ Хорошие практики

1. **Всегда определяйте базовый случай**
2. **Убедитесь, что рекурсия приближается к базовому случаю**
3. **Используйте мемоизацию для древовидной рекурсии**
4. **Рассмотрите итеративное решение для простых задач**
5. **Добавляйте docstring с примерами**

### ❌ Чего избегать

1. Бесконечной рекурсии (отсутствие базового случая)
2. Слишком глубокой рекурсии без необходимости
3. Рекурсии там, где итерация проще и эффективнее
4. Древовидной рекурсии без кеширования

## Отладка рекурсивных функций

### Визуализация вызовов

```python
def factorial_debug(n, level=0):
    """Факториал с выводом каждого шага"""
    indent = "  " * level
    print(f"{indent}→ factorial({n})")
    
    if n <= 1:
        print(f"{indent}← возврат 1")
        return 1
    
    result = n * factorial_debug(n - 1, level + 1)
    print(f"{indent}← возврат {result}")
    return result

factorial_debug(4)
# → factorial(4)
#   → factorial(3)
#     → factorial(2)
#       → factorial(1)
#       ← возврат 1
#     ← возврат 2
#   ← возврат 6
# ← возврат 24
```

## 🔎 Проверка на ходу

Эксперименты в REPL:

```python
>>> def countdown(n):
...     if n <= 0:
...         print("Boom!")
...     else:
...         print(n)
...         countdown(n - 1)
...
>>> countdown(3)
3
2
1
Boom!

>>> def factorial(n):
...     return 1 if n <= 1 else n * factorial(n - 1)
...
>>> factorial(5)
120
```

## Резюме

**Рекурсия:**
- Функция вызывает сама себя
- Требует базовый случай (условие остановки)
- Требует рекурсивный случай (вызов с изменёнными аргументами)

**Типы рекурсии:**
- **Простая** — один рекурсивный вызов
- **Взаимная** — функции вызывают друг друга
- **Древовидная** — несколько рекурсивных вызовов

**Оптимизация:**
- Мемоизация (кеширование)
- `@lru_cache` декоратор
- Хвостовая рекурсия
- Переход к итерации

**Когда использовать:**
- ✅ Естественно рекурсивные задачи (деревья, графы)
- ✅ Делимые задачи (разделяй и властвуй)
- ✅ Математические определения
- ❌ Простые циклы (используйте итерацию)
- ❌ Очень глубокая вложенность

**Ограничения Python:**
- Лимит глубины рекурсии (~1000)
- Нет автоматической оптимизации хвостовой рекурсии

---

[[06_09_higher_order_functions|← 🎭 Функции высших порядков]] | [[06_11_closures_and_decorators|🎁 Замыкания и декораторы →]]
