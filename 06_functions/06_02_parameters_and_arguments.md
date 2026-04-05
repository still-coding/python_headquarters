[[06_01_basics|← ⚙️ Основы функций]] | [[06_03_scope_legb|🔍 Области видимости (LEGB) →]]

---
# 📦 Параметры и аргументы
```toc
minLevel: 2
```

## Терминология

**Параметры** — переменные в определении функции:
```python
def my_function(a, b):  # a и b — параметры
    return a + b
```

**Аргументы** — значения, передаваемые при вызове:
```python
result = my_function(3, 4)  # 3 и 4 — аргументы
```

## Способы передачи аргументов

В Python есть несколько способов передать аргументы в функцию.

### 1. Позиционные аргументы

Аргументы передаются **по порядку**:

```python
def divide(dividend, divisor):
    return dividend / divisor

result = divide(10, 2)  # 10 / 2 = 5.0
print(result)
```

**Важно:** порядок имеет значение!

```python
print(divide(10, 2))  # 5.0
print(divide(2, 10))  # 0.2 — совсем другой результат!
```

### 2. Ключевые (именованные) аргументы

Аргументы передаются **по имени параметра**:

```python
def describe_pet(name, animal_type):
    print(f"У меня есть {animal_type} по имени {name}")

# Можно передать в любом порядке
describe_pet(name="Шарик", animal_type="собака")
describe_pet(animal_type="кошка", name="Мурка")
```

**Преимущества:**
- Код понятнее — видно, что означает каждый аргумент
- Порядок не важен
- Меньше ошибок при большом количестве параметров

### 3. Смешанный подход

Можно комбинировать позиционные и ключевые аргументы:

```python
def create_profile(name, age, city="Not specified", job="Not specified"):
    print(f"{name}, {age} лет, {city}, {job}")

# Позиционные + ключевые
create_profile("Anna", 25, city="Moscow")
# Выведет: Anna, 25 лет, Moscow, Not specified

create_profile("Ivan", 30, job="Programmer")
# Выведет: Ivan, 30 лет, Not specified, Programmer
```

**Правило:** позиционные аргументы должны идти **перед** ключевыми!

```python
# ✅ Правильно
my_function(1, 2, c=3)

# ❌ Неправильно
my_function(1, b=2, 3)  # SyntaxError
```

## Параметры со значениями по умолчанию

Можно задать значения параметрам **по умолчанию**:

```python
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}!")

greet("Maria")                    # Hello, Maria!
greet("Peter", "Hi")              # Hi, Peter!
greet("Anna", greeting="Hey")     # Hey, Anna!
```

### ⚠️ Важные правила

**1. Параметры с значениями по умолчанию должны идти после обязательных:**

```python
# ✅ Правильно
def my_function(required, optional=10):
    pass

# ❌ Неправильно
def my_function(optional=10, required):  # SyntaxError
    pass
```

**2. Значения по умолчанию вычисляются один раз при определении функции:**

```python
def append_to_list(item, my_list=[]):  # ⚠️ Опасно!
    my_list.append(item)
    return my_list

print(append_to_list(1))  # [1]
print(append_to_list(2))  # [1, 2] — не [2]! Список общий!

# ✅ Правильный способ:
def append_to_list(item, my_list=None):
    if my_list is None:
        my_list = []
    my_list.append(item)
    return my_list
```

## Как передаются аргументы: по значению или по ссылке?

В Python аргументы передаются **по ссылке**, но в зависимости от типа объекта, поведение будет отличаться:

### Неизменяемые типы (числа, строки, кортежи)

```python
def change_number(x):
    x = x + 10
    print(f"Внутри функции: {x}")

number = 5
change_number(number)  # Внутри функции: 15
print(number)          # 5 — не изменилось!
```

**Объяснение:** 
- `x` внутри функции — это локальная переменная
- Присваивание `x = x + 10` создаёт новый объект
- Исходная переменная `number` не затрагивается

### Изменяемые типы (списки, словари)

```python
def change_list(lst):
    lst.append(4)
    print(f"Внутри функции: {lst}")

my_list = [1, 2, 3]
change_list(my_list)  # Внутри функции: [1, 2, 3, 4]
print(my_list)        # [1, 2, 3, 4] — изменился!
```

**Объяснение:**
- `lst` внутри функции ссылается на тот же объект
- Методы типа `append()` **изменяют** объект на месте
- Изменения видны снаружи функции

```python
def my_function(a, b):
    a = 1           # Создаётся новая локальная переменная
    b[0] = 'spam'   # Изменяется существующий объект
    return (a, b)

x = 3
y = [1, 2]

result = my_function(x, y)
print(x)  # 3 — не изменился
print(y)  # ['spam', 2] — изменился!
```

## 🎯 Практические примеры

### Пример 1: Функция с несколькими значениями по умолчанию

```python
def place_order(item, quantity=1, delivery="Pickup", urgent=False):
    """Оформляет заказ товара"""
    message = f"Order: {item}, {quantity} pcs, {delivery}"
    if urgent:
        message += " (urgent delivery)"
    return message

# Разные варианты вызова
print(place_order("Book"))
# Order: Book, 1 pcs, Pickup

print(place_order("Book", 3))
# Order: Book, 3 pcs, Pickup

print(place_order("Book", delivery="Courier"))
# Order: Book, 1 pcs, Courier

print(place_order("Book", 2, urgent=True))
# Order: Book, 2 pcs, Pickup (urgent delivery)
```

### Пример 2: Реальный случай использования

```python
def calculate_price(price, quantity, discount=0, tax=0.2):
    """
    Вычисляет итоговую стоимость с учётом скидки и налога.
    
    Параметры:
        price: цена за единицу товара
        quantity: количество товара
        discount: скидка в процентах (0-100)
        tax: налог в долях (например, 0.2 = 20%)
    
    Примеры:
        >>> calculate_price(1000, 5)
        6000.0
        >>> calculate_price(100, 5, discount=10)
        540.0
    """
    cost = price * quantity
    discounted_cost = cost * (1 - discount / 100)
    total = discounted_cost * (1 + tax)
    return round(total, 2)

# Примеры использования
print(calculate_price(100, 5))  
# 600.0 (без скидки)

print(calculate_price(100, 5, discount=10))  
# 540.0 (со скидкой 10%)

print(calculate_price(100, 5, discount=20, tax=0.18))  
# 472.0 (скидка 20%, налог 18%)
```

### Пример 3: Избегание ошибок с изменяемыми значениями по умолчанию

```python
# ❌ НЕПРАВИЛЬНО
def create_player_bad(name, inventory=[]):
    inventory.append("sword")
    return {"name": name, "inventory": inventory}

player1 = create_player_bad("Warrior")
player2 = create_player_bad("Mage")
print(player1)  # {'name': 'Warrior', 'inventory': ['sword', 'sword']}
print(player2)  # {'name': 'Mage', 'inventory': ['sword', 'sword']} — у обоих одинаковый!

# ✅ ПРАВИЛЬНО
def create_player(name, inventory=None):
    if inventory is None:
        inventory = []
    inventory.append("sword")
    return {"name": name, "inventory": inventory}

player1 = create_player("Warrior")
player2 = create_player("Mage")
print(player1)  # {'name': 'Warrior', 'inventory': ['sword']}
print(player2)  # {'name': 'Mage', 'inventory': ['sword']} — у каждого свой список
```

## 🔎 Проверка на ходу

Используй REPL для экспериментов:

```python
>>> def test(a, b=[]):
...     b.append(a)
...     return b
...
>>> test(1)
[1]
>>> test(2)
[1, 2]  # Ого! Список сохраняется между вызовами!
>>> id(test.__defaults__[0])  # Это один и тот же объект
140123456789
```

## Резюме

- **Позиционные аргументы** — передаются по порядку
- **Ключевые аргументы** — передаются по имени параметра (можно в любом порядке)
- **Значения по умолчанию** — используются, если аргумент не передан
- Позиционные аргументы должны идти перед ключевыми
- Параметры с значениями по умолчанию должны идти после обязательных
- ⚠️ Осторожно с изменяемыми значениями по умолчанию!
- Неизменяемые объекты не меняются внутри функции
- Изменяемые объекты могут измениться

---

[[06_01_basics|← ⚙️ Основы функций]] | [[06_03_scope_legb|🔍 Области видимости (LEGB) →]]
