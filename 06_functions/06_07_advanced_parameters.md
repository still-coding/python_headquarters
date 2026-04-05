[[06_06_docstrings_and_doctests|← 📝 Документирование функций]] | [[06_08_lambda_functions|λ Lambda-функции →]]

---
# ⚙️ Продвинутые параметры
```toc
minLevel: 2
```

## Введение

Python предоставляет мощные механизмы для работы с параметрами функций. В этом разделе мы изучим продвинутые техники, которые делают функции более гибкими и удобными.

## `*args` — произвольное количество позиционных аргументов

`*args` позволяет функции принимать **любое количество** позиционных аргументов:

```python
def sum_all(*numbers):
    """Возвращает сумму всех переданных чисел"""
    return sum(numbers)

print(sum_all(1, 2, 3))           # 6
print(sum_all(1, 2, 3, 4, 5))     # 15
print(sum_all(10, 20, 30, 40))    # 100
```

**Как это работает:**
- `*numbers` собирает все позиционные аргументы в **кортеж**
- Имя может быть любым, но принято использовать `args`

```python
def print_items(*items):
    """Выводит все переданные элементы"""
    print(f"Получено {len(items)} элементов:")
    for i, item in enumerate(items, 1):
        print(f"  {i}. {item}")

print_items("яблоко", "банан", "апельсин")
# Получено 3 элементов:
#   1. яблоко
#   2. банан
#   3. апельсин
```

## `**kwargs` — произвольное количество ключевых аргументов

`**kwargs` позволяет функции принимать **любое количество** ключевых аргументов:

```python
def create_profile(name, **additional):
    """Создаёт профиль пользователя с дополнительными полями"""
    profile = {"name": name}
    profile.update(additional)
    return profile

user1 = create_profile("Anna", age=25, city="Moscow")
print(user1)  
# {'name': 'Anna', 'age': 25, 'city': 'Moscow'}

user2 = create_profile("Ivan", age=30, job="Programmer", hobby="Chess")
print(user2)
# {'name': 'Ivan', 'age': 30, 'job': 'Programmer', 'hobby': 'Chess'}
```

**Как это работает:**
- `**additional` собирает все ключевые аргументы в **словарь**
- Имя может быть любым, но принято использовать `kwargs`

## Комбинирование `*args` и `**kwargs`

Можно использовать оба механизма вместе:

```python
def logger(level, message, *details, **metadata):
    """
    Универсальная функция логирования.
    
    Args:
        level: Уровень важности (INFO, WARNING, ERROR)
        message: Текст сообщения
        *details: Дополнительные детали
        **metadata: Метаданные (пользователь, время и т.д.)
    """
    print(f"[{level}] {message}")
    
    if details:
        print("Детали:", " | ".join(str(d) for d in details))
    
    if metadata:
        print("Метаданные:", metadata)

# Использование
logger("ERROR", "Ошибка подключения", "Порт 5432", "Таймаут", 
       user="admin", attempt=3)
# [ERROR] Ошибка подключения
# Детали: Порт 5432 | Таймаут
# Метаданные: {'user': 'admin', 'attempt': 3}
```

## Keyword-only параметры (после `*`)

Параметры **после** `*` можно передавать **только по имени**:

```python
def place_order(item, quantity, *, urgent_delivery=False, gift_wrap=False):
    """
    Оформляет заказ товара.
    
    Args:
        item: Название товара
        quantity: Количество
        urgent_delivery: Срочная доставка (только keyword)
        gift_wrap: Подарочная упаковка (только keyword)
    """
    print(f"Заказ: {item}, {quantity} шт.")
    if urgent_delivery:
        print("+ Срочная доставка")
    if gift_wrap:
        print("+ Подарочная упаковка")

# ✅ Правильно
place_order("Книга", 2, urgent_delivery=True)

# ❌ Неправильно
place_order("Книга", 2, True)  # TypeError
```

### Зачем это нужно?

**Повышает читаемость и предотвращает ошибки:**

```python
# Пример из реальной практики
def place_order(*, ticker, price, quantity):
    """Размещает заказ на бирже"""
    print(f'{quantity} units of {ticker} at {price} price')

# ❌ Эта ошибка предотвращена:
place_order('SPX', 3500, 1000)  # TypeError - забыли имена параметров!

# ✅ Правильно и понятно:
place_order(ticker='SPX', price=3500, quantity=1000)
```

Без `*` легко перепутать порядок:
```python
# БЕЗ keyword-only (плохо)
def place_order_bad(ticker, price, quantity):
    print(f'{quantity} units of {ticker} at {price} price')

place_order_bad('SPX', 3500, 1000)  # Правильно
place_order_bad('SPX', 1000, 3500)  # Ошибка! Перепутали price и quantity
```

## Positional-only параметры (до `/`)

Параметры **до** `/` можно передавать **только позиционно**:

```python
def divide(a, b, /):
    """
    Делит a на b.
    
    Args:
        a: Делимое (только позиционный)
        b: Делитель (только позиционный)
    """
    return a / b

# ✅ Правильно
print(divide(10, 2))  # 5.0

# ❌ Неправильно
print(divide(a=10, b=2))  # TypeError
```

### Зачем это нужно?

**Когда имена параметров не важны или могут измениться:**

```python
# Пример из стандартной библиотеки
def pow(x, y, z=None, /):
    """Возводит x в степень y, опционально по модулю z"""
    pass

pow(2, 3)        # ✅ OK
pow(x=2, y=3)    # ❌ TypeError
```

### Практический пример

```python
def power_mod(x, y, /, mod):
    """
    Возводит x в степень y по модулю mod.
    
    x и y — только позиционные (имена не важны).
    mod — можно передать по имени (более понятно).
    """
    return (x ** y) % mod

# ✅ Работает
power_mod(3, 5, 17)
power_mod(3, 5, mod=17)

# ❌ Не работает
power_mod(x=3, y=5, mod=17)  # TypeError
```

## Комбинирование `/` и `*`

Можно использовать оба маркера:

```python
def function(only_pos, /, pos_or_key, *, only_key):
    """
    Args:
        only_pos: Только позиционный
        pos_or_key: Можно и так, и так
        only_key: Только ключевой
    """
    print(f"{only_pos}, {pos_or_key}, {only_key}")

# ✅ Правильные вызовы:
function(1, 2, only_key=3)
function(1, pos_or_key=2, only_key=3)

# ❌ Неправильные:
function(only_pos=1, pos_or_key=2, only_key=3)  # TypeError
function(1, 2, 3)  # TypeError
```

## Комбинирование всех видов параметров

Можно использовать **все виды параметров вместе**, но в строгом порядке:

```python
def serious_function(
    only_pos, /,              # Только позиционный
    pos_or_key,               # Позиционный или ключевой
    default=10,               # Со значением по умолчанию
    *args,                    # Дополнительные позиционные
    only_key,                 # Keyword-only обязательный
    key_default=20,           # Keyword-only со значением
    **kwargs                  # Дополнительные ключевые
):
    """Демонстрация всех возможностей"""
    print(f"only_pos: {only_pos}")
    print(f"pos_or_key: {pos_or_key}")
    print(f"default: {default}")
    print(f"args: {args}")
    print(f"only_key: {only_key}")
    print(f"key_default: {key_default}")
    print(f"kwargs: {kwargs}")

# Использование
serious_function(
    1,                          # only_pos
    2,                          # pos_or_key
    3, 4,                       # args
    only_key=5,                 # only_key
    key_default=6,              # key_default
    additional=7                # kwargs
)
```

**Порядок параметров:**
1. Positional-only (до `/`)
2. Обычные позиционные
3. Позиционные со значениями по умолчанию
4. `*args`
5. Keyword-only параметры
6. `**kwargs`

## Распаковка аргументов

### Распаковка списков/кортежей с `*`

```python
def add(a, b, c):
    return a + b + c

numbers = [1, 2, 3]

# ❌ Неправильно
print(add(numbers))  # TypeError: missing 2 required arguments

# ✅ Правильно - распаковываем список
print(add(*numbers))  # 6
# Эквивалентно: add(1, 2, 3)
```

### Распаковка словарей с `**`

```python
def create_user(name, age, city):
    print(f"{name}, {age} лет, {city}")

data = {"name": "Anna", "age": 25, "city": "Moscow"}

# ✅ Распаковываем словарь
create_user(**data)
# Эквивалентно: create_user(name="Anna", age=25, city="Moscow")
```

### Практический пример

```python
def send_email(to, subject, text, *, cc=None, bcc=None):
    """Отправляет email с дополнительными параметрами"""
    print(f"Кому: {to}")
    print(f"Тема: {subject}")
    print(f"Текст: {text}")
    if cc:
        print(f"Копия: {cc}")
    if bcc:
        print(f"Скрытая копия: {bcc}")

# Базовые параметры
basic = ["user@example.com", "Важно", "Срочное сообщение"]

# Дополнительные параметры
extra = {
    "cc": "manager@example.com",
    "bcc": "admin@example.com"
}

# Отправка с распаковкой
send_email(*basic, **extra)
```

## 🎯 Практические примеры

### Пример 1: Универсальный декоратор

```python
def repeat(times=3, *, delay=0):
    """
    Декоратор, который повторяет функцию несколько раз.
    
    Args:
        times: Сколько раз повторить
        delay: Задержка между повторениями (keyword-only)
    """
    def decorator(func):
        def wrapper(*args, **kwargs):
            results = []
            for i in range(times):
                if i > 0 and delay > 0:
                    import time
                    time.sleep(delay)
                results.append(func(*args, **kwargs))
            return results
        return wrapper
    return decorator

@repeat(times=3, delay=0.5)
def get_random():
    import random
    return random.randint(1, 100)

print(get_random())  # [42, 73, 15] (три случайных числа)
```

### Пример 2: Построитель HTML

```python
def create_tag(name, *content, **attributes):
    """
    Создаёт HTML-тег с атрибутами и содержимым.
    
    Args:
        name: Название тега
        *content: Содержимое тега
        **attributes: HTML-атрибуты
    """
    # Формируем атрибуты
    attr_str = ""
    if attributes:
        attr_str = " " + " ".join(f'{k}="{v}"' for k, v in attributes.items())
    
    # Формируем содержимое
    content_str = " ".join(str(c) for c in content)
    
    return f"<{name}{attr_str}>{content_str}</{name}>"

# Использование
print(create_tag("div", "Привет, мир!", id="main", class_="container"))
# <div id="main" class_="container">Привет, мир!</div>

print(create_tag("a", "Ссылка", href="https://example.com", target="_blank"))
# <a href="https://example.com" target="_blank">Ссылка</a>
```

### Пример 3: Конфигурация приложения

```python
def configure_app(name, version, /, *, debug=False, **extra):
    """
    Настраивает приложение с обязательными и дополнительными параметрами.
    
    Args:
        name: Название приложения (только позиционный)
        version: Версия приложения (только позиционный)
        debug: Режим отладки (keyword-only)
        **extra: Любые дополнительные настройки
    """
    config = {
        "name": name,
        "version": version,
        "debug": debug
    }
    
    # Добавляем дополнительные настройки
    config.update(extra)
    
    return config

# Использование
settings = configure_app(
    "MyApp", 
    "1.0.0",
    debug=True,
    port=8000,
    host="localhost",
    database="postgresql://..."
)

print(settings)
```

## 💡 Лучшие практики

### ✅ Хорошие практики

1. **Используйте `*args` для функций с переменным числом аргументов**
   ```python
   def max_value(*numbers):
       return max(numbers)
   ```

2. **Используйте `**kwargs` для гибкой конфигурации**
   ```python
   def create_connection(host, port, **options):
       pass
   ```

3. **Применяйте keyword-only для важных параметров**
   ```python
   def process_data(data, *, validate=True, strict=False):
       pass
   ```

4. **Positional-only для низкоуровневых функций**
   ```python
   def distance(x1, y1, x2, y2, /):
       pass
   ```

### ❌ Плохие практики

1. Слишком много `*args` и `**kwargs` — код становится непонятным
2. Использование только `*args` и `**kwargs` без явных параметров
3. Непоследовательное использование механизмов

## 🔎 Проверка на ходу

Эксперименты в REPL:

```python
>>> def test(*args, **kwargs):
...     print("args:", args)
...     print("kwargs:", kwargs)
...
>>> test(1, 2, 3, a=4, b=5)
args: (1, 2, 3)
kwargs: {'a': 4, 'b': 5}

>>> def test2(a, /, b, *, c):
...     print(f"a={a}, b={b}, c={c}")
...
>>> test2(1, 2, c=3)
a=1, b=2, c=3
>>> test2(a=1, b=2, c=3)  # TypeError
```

## Резюме

**Виды параметров:**
- **Обычные** — `def f(a, b)`
- **Со значениями по умолчанию** — `def f(a, b=10)`
- **`*args`** — любое количество позиционных аргументов (кортеж)
- **`**kwargs`** — любое количество ключевых аргументов (словарь)
- **Keyword-only** (после `*`) — только по имени
- **Positional-only** (до `/`) — только позиционно

**Порядок параметров:**
```python
def func(pos_only, /, normal, default=10, *args, key_only, **kwargs):
    pass
```

**Распаковка:**
- `*список` — распаковка в позиционные аргументы
- `**словарь` — распаковка в ключевые аргументы

**Когда использовать:**
- `*args` — когда количество аргументов заранее неизвестно (sum, max, min)
- `**kwargs` — для гибкой конфигурации, опциональных параметров
- Keyword-only (`*`) — когда важна читаемость и предотвращение ошибок
- Positional-only (`/`) — когда имена параметров не важны

---

[[06_06_docstrings_and_doctests|← 📝 Документирование функций]] | [[06_08_lambda_functions|λ Lambda-функции →]]
