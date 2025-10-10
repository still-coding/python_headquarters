[[02_parameters_and_arguments|← 📦 Параметры и аргументы]] | [[04_return_values|🔙 Возвращаемые значения →]]

---
# 🔍 Области видимости (LEGB)
```toc
minLevel: 2
```

## Что такое область видимости?

**Область видимости** (scope) определяет, где в программе переменная доступна.

Python использует правило **LEGB** для поиска переменных:
- **L**ocal — локальная (внутри функции)
- **E**nclosing — объемлющая (во внешних функциях)
- **G**lobal — глобальная (на уровне модуля)
- **B**uilt-in — встроенная (стандартные функции Python)

## Local (Локальная область)

Переменные, созданные внутри функции, существуют только в этой функции:

```python
def my_function():
    x = 10  # Локальная переменная
    print(x)

my_function()  # 10
print(x)       # NameError: name 'x' is not defined
```

**Важно:** локальные переменные создаются при вызове функции и уничтожаются после её завершения.

## Global (Глобальная область)

Переменные, созданные на уровне модуля (вне функций):

```python
x = 10  # Глобальная переменная

def my_function():
    print(x)  # Читаем глобальную переменную

my_function()  # 10
print(x)       # 10
```

### Изменение глобальных переменных

Чтобы **изменить** глобальную переменную, нужен `global`:

```python
count = 0  # Глобальная переменная

def increment():
    global count  # Объявляем, что используем глобальную переменную
    count += 1

increment()
print(count)  # 1

increment()
print(count)  # 2
```

**Без `global` получим ошибку:**

```python
count = 0

def increment():
    count += 1  # ❌ UnboundLocalError

increment()
```

## Enclosing (Объемлющая область)

Переменные из **внешних функций** (для вложенных функций):

```python
def outer():
    x = "outer"  # Объемлющая переменная для inner()
    
    def inner():
        print(x)  # Читаем переменную из outer()
    
    inner()

outer()  # outer
```

### Изменение объемлющих переменных

Для изменения нужен `nonlocal`:

```python
def outer():
    count = 0
    
    def increment():
        nonlocal count  # Объявляем, что используем переменную из outer()
        count += 1
        return count
    
    print(increment())  # 1
    print(increment())  # 2
    print(increment())  # 3

outer()
```

**Разница между `global` и `nonlocal`:**
- `global` — ищет в глобальной области (модуле), **может создавать** переменные
- `nonlocal` — ищет в объемлющих функциях, **НЕ может создавать** переменные

## Built-in (Встроенная область)

Встроенные имена Python (`print`, `len`, `sum` и т.д.):

```python
# Используем встроенную функцию
print(len([1, 2, 3]))  # 3
print(sum([1, 2, 3]))  # 6
```

**Осторожно:** можно случайно **затенить** встроенные имена:

```python
# ❌ Плохо - затеняем встроенную функцию
sum = 10
result = sum([1, 2, 3])  # TypeError: 'int' object is not callable

# ✅ Хорошо - используем другое имя
total = 10
result = sum([1, 2, 3])  # 6
```

## 🎯 Порядок поиска LEGB

Когда Python встречает переменную, он ищет её в таком порядке:

```python
# 4. Built-in

x = "global"  # 3. Global

def outer():
    x = "enclosing"  # 2. Enclosing
    
    def inner():
        x = "local"  # 1. Local (найдено здесь, дальше не ищем!)
        print(x)
    
    inner()

outer()  # local
```

**Поиск останавливается**, как только переменная найдена!

## Практические примеры

### Пример 1: Счётчик вызовов функции

```python
def create_counter():
    """Создаёт функцию-счётчик с сохранением состояния"""
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

### Пример 2: Конфигурация через глобальные переменные

```python
# Глобальная конфигурация
DEBUG = True
MAX_RETRIES = 3

def process_data(data):
    if DEBUG:
        print(f"[DEBUG] Processing data: {data}")
    
    attempts = 0
    while attempts < MAX_RETRIES:
        # ... попытка выполнить операцию ...
        attempts += 1
    
    if DEBUG:
        print(f"[DEBUG] Completed in {attempts} attempts")

process_data([1, 2, 3])
```

### Пример 3: Избегание global (лучшая практика)

```python
# ❌ С global (хуже)
count = 0

def increment_count():
    global count
    count += 1

def get_count():
    return count

# ✅ Без global (лучше)
def increment_count(current_count):
    return current_count + 1

count = 0
count = increment_count(count)
count = increment_count(count)
print(count)  # 2
```

## ⚠️ Типичные ошибки

### Ошибка 1: Забыли `global`

```python
x = 10

def modify():
    x = x + 1  # ❌ UnboundLocalError

# Правильно:
def modify():
    global x
    x = x + 1
```

### Ошибка 2: `nonlocal` без объемлющей переменной

```python
def my_function():
    nonlocal x  # ❌ SyntaxError
    x = 10

# Правильно:
def outer():
    x = 5
    
    def inner():
        nonlocal x
        x = 10
```

### Ошибка 3: Затенение встроенных имён

```python
# ❌ Плохо
def my_function():
    sum = 10  # Теперь sum — не функция!
    result = sum([1, 2, 3])  # TypeError

# ✅ Хорошо
def my_function():
    total = 10
    result = sum([1, 2, 3])
```

## 💡 Визуализация поиска переменных

Когда Python встречает переменную, он ищет её так:

```
def outer():
    x = "enclosing"
    
    def inner():
        # Python ищет 'x':
        # 1. Local (inner) — нет
        # 2. Enclosing (outer) — нашёл! x = "enclosing"
        # (дальше не ищет)
        print(x)
```

Если переменная найдена на каком-то уровне, поиск останавливается.

## 🔎 Практические советы

### ✅ Хорошие практики

1. **Минимизируйте использование `global`** — передавайте данные через параметры
2. **Используйте понятные имена** — избегайте `x`, `y`, `z` для важных переменных
3. **Не затеняйте встроенные имена** — избегайте `list`, `dict`, `sum` как имена переменных
4. **Документируйте зависимости** — если функция использует глобальные переменные, укажите это в документации

### ❌ Плохие практики

1. Изменение глобальных переменных из многих функций
2. Создание очень глубоких вложенных функций (3+ уровня)
3. Использование одинаковых имён на разных уровнях без необходимости

## Тестирование понимания

Что выведет этот код?

```python
x = 1  # Global

def f2():
    x = 2  # Enclosing для f1
    
    def f1():
        x = 3  # Local
        print(f"f1: {x}")
    
    f1()
    print(f"f2: {x}")

f2()
print(f"global: {x}")
```

## Резюме

- **LEGB** — порядок поиска переменных: Local → Enclosing → Global → Built-in
- **Локальные** переменные существуют только внутри функции
- **Глобальные** переменные доступны везде, но для изменения нужен `global`
- **Объемлющие** переменные — из внешних функций, для изменения нужен `nonlocal`
- **Встроенные** имена — стандартные функции Python
- `global` ищет в модуле, может создавать переменные
- `nonlocal` ищет в объемлющих функциях, НЕ может создавать переменные
- Старайтесь избегать `global` — используйте параметры и return

---

[[02_parameters_and_arguments|← 📦 Параметры и аргументы]] | [[04_return_values|🔙 Возвращаемые значения →]]
