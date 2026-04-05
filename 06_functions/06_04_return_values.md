[[06_03_scope_legb|← 🔍 Области видимости (LEGB)]] | [[06_05_pure_functions|✨ Чистые функции →]]

---
# 🔙 Возвращаемые значения
```toc
minLevel: 2
```

## Оператор `return`

Оператор `return` выполняет две функции:
1. **Возвращает значение** из функции
2. **Завершает выполнение** функции

```python
def add(a, b):
    return a + b  # Возвращает результат и выходит из функции

result = add(3, 5)
print(result)  # 8
```

## Функция без `return`

Если в функции нет `return`, она автоматически возвращает `None`:

```python
def greet(name):
    print(f"Привет, {name}!")
    # Нет return

result = greet("Anna")  # Привет, Anna!
print(result)           # None
```

**Когда это полезно:**
- Функции, которые только выводят информацию
- Функции, которые изменяют данные на месте
- Функции с побочными эффектами (запись в файл, отправка данных)

## Явный `return` без значения

Можно написать `return` без значения — это тоже вернёт `None`:

```python
def check_age(age):
    if age < 0:
        print("Ошибка: возраст не может быть отрицательным")
        return  # Выходим из функции досрочно
    
    print(f"Возраст: {age} лет")

check_age(-5)  # Ошибка: возраст не может быть отрицательным
check_age(25)  # Возраст: 25 лет
```

## Возврат нескольких значений

Python позволяет вернуть несколько значений через **кортеж**:

```python
def get_coordinates():
    x = 10
    y = 20
    return x, y  # На самом деле возвращает кортеж (10, 20)

# Распаковка результата
coord_x, coord_y = get_coordinates()
print(coord_x)  # 10
print(coord_y)  # 20

# Или можно получить как кортеж
coords = get_coordinates()
print(coords)        # (10, 20)
print(type(coords))  # <class 'tuple'>
```

### Практический пример: статистика списка

```python
def get_stats(numbers):
    """Возвращает минимум, максимум и среднее значение"""
    if not numbers:
        return None, None, None
    
    minimum = min(numbers)
    maximum = max(numbers)
    average = sum(numbers) / len(numbers)
    
    return minimum, maximum, average

# Использование
data = [5, 2, 8, 1, 9, 3]
min_val, max_val, avg = get_stats(data)
print(f"Min: {min_val}, Max: {max_val}, Avg: {avg:.2f}")
# Min: 1, Max: 9, Avg: 4.67
```

### Возврат словаря для именованных результатов

Когда значений много, удобнее вернуть **словарь**:

```python
def analyze_text(text):
    """Анализирует текст и возвращает статистику"""
    words = text.split()
    
    return {
        "word_count": len(words),
        "char_count": len(text),
        "first_word": words[0] if words else None,
        "last_word": words[-1] if words else None
    }

result = analyze_text("Python is an amazing programming language")
print(result["word_count"])  # 6
print(result["first_word"])  # Python
```

## Несколько операторов `return`

Функция может иметь несколько `return` в разных ветках:

```python
def check_sign(number):
    """Возвращает описание знака числа"""
    if number > 0:
        return "positive"
    elif number < 0:
        return "negative"
    else:
        return "zero"

print(check_sign(5))   # positive
print(check_sign(-3))  # negative
print(check_sign(0))   # zero
```

**Важно:** как только Python встречает `return`, функция завершается — код после `return` не выполняется!

```python
def my_function():
    return "Result"
    print("Это никогда не выполнится")  # Недостижимый код

result = my_function()
# print не выполнился
```

## Ранний выход из функции

`return` часто используется для **ранней проверки условий**:

```python
def divide(a, b):
    if b == 0:
        return None  # Ранний выход при ошибке
    
    return a / b

print(divide(10, 2))  # 5.0
print(divide(10, 0))  # None
```

### Guard clauses (защитные условия)

Хорошая практика — проверять "плохие" случаи в начале:

```python
# ❌ Вложенные условия (хуже)
def process_order(order):
    if order is not None:
        if order.quantity > 0:
            if order.price > 0:
                # Основная логика здесь
                return order.quantity * order.price
    return None

# ✅ Guard clauses (лучше)
def process_order(order):
    if order is None:
        return None
    
    if order.quantity <= 0:
        return None
    
    if order.price <= 0:
        return None
    
    # Основная логика на верхнем уровне
    return order.quantity * order.price
```

## 🎯 Практические примеры

### Пример 1: Поиск элемента

```python
def find_index(items, target):
    """
    Находит индекс первого вхождения элемента в списке.
    Возвращает индекс или -1, если элемент не найден.
    """
    for i, value in enumerate(items):
        if value == target:
            return i  # Нашли — сразу выходим
    
    return -1  # Не нашли

numbers = [10, 20, 30, 40, 50]
print(find_index(numbers, 30))   # 2
print(find_index(numbers, 100))  # -1
```

### Пример 2: Валидация данных

```python
def validate_email(email):
    """
    Проверяет корректность email.
    Возвращает (True, None) если ок, (False, сообщение) если ошибка.
    """
    if not email:
        return False, "Email не может быть пустым"
    
    if "@" not in email:
        return False, "Email должен содержать @"
    
    if "." not in email.split("@")[1]:
        return False, "Неверный домен"
    
    return True, None

# Использование
is_valid, error = validate_email("user@example.com")
if is_valid:
    print("Email корректный")
else:
    print(f"Ошибка: {error}")
```

### Пример 3: Рекурсивная функция

```python
def factorial(n):
    """Вычисляет факториал числа"""
    if n == 0 or n == 1:
        return 1  # Базовый случай
    
    return n * factorial(n - 1)  # Рекурсивный случай

print(factorial(5))  # 120
# 5! = 5 * 4 * 3 * 2 * 1 = 120
```

## ⚠️ Типичные ошибки

### Ошибка 1: Забыть `return`

```python
# ❌ Неправильно
def add(a, b):
    result = a + b
    # Забыли return!

print(add(3, 5))  # None

# ✅ Правильно
def add(a, b):
    return a + b
```

### Ошибка 2: Код после `return`

```python
# ❌ Код после return не выполняется
def my_function():
    return 10
    print("Это не выполнится")
    return 20  # Это тоже не выполнится
```

### Ошибка 3: Возврат в цикле

```python
# ❌ Возвращает только первый элемент
def get_even(numbers):
    for num in numbers:
        if num % 2 == 0:
            return num  # Выходит сразу!

# ✅ Правильно: собираем все результаты
def get_even(numbers):
    result = []
    for num in numbers:
        if num % 2 == 0:
            result.append(num)
    return result
```

## Резюме

- `return` возвращает значение и завершает функцию
- Без `return` функция возвращает `None`
- Можно вернуть несколько значений через кортеж: `return a, b, c`
- Можно иметь несколько `return` в разных ветках
- Код после `return` не выполняется
- Guard clauses делают код чище
- Функции могут возвращать другие функции
- Лучше быть последовательным в типах возвращаемых значений

---

[[06_03_scope_legb|← 🔍 Области видимости (LEGB)]] | [[06_05_pure_functions|✨ Чистые функции →]]
