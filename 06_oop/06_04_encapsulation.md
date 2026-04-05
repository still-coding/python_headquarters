[[06_03_init_and_self|← 🔧 `__init__` и `self`]] | [[06_05_inheritance|🧬 Наследование →]]

---
# 🔒 Инкапсуляция
```toc
minLevel: 2
```

## Что такое инкапсуляция?

**Инкапсуляция** — это принцип объединения данных и методов, работающих с этими данными, в одну сущность — класс.

Суть простая: объект **сам управляет своим состоянием**. Данные и логика работы с ними живут вместе, а не разбросаны по программе.

Посмотрим на разницу:

```python
# ❌ Без инкапсуляции — данные и логика живут отдельно
balance = 1000

def deposit(amount):
    global balance
    balance += amount

def withdraw(amount):
    global balance
    balance -= amount

deposit(500)
withdraw(200)
print(balance)  # 1300
```

```python
# ✅ С инкапсуляцией — данные и логика объединены в классе
class BankAccount:
    def __init__(self, balance):
        self.balance = balance   # Данные — внутри объекта

    def deposit(self, amount):   # Логика — тоже внутри объекта
        self.balance += amount

    def withdraw(self, amount):
        self.balance -= amount


account = BankAccount(1000)
account.deposit(500)
account.withdraw(200)
print(account.balance)  # 1300
```

Обратите внимание: во втором примере `balance` — **публичный** атрибут. Сокрытия здесь нет, но **инкапсуляция есть**: объект сам знает, как работать со своими данными.

## Инкапсуляция ≠ сокрытие данных

Это частая путаница. Разберём:

- **Инкапсуляция** — объединение данных и методов в классе. Это основа ООП.
- **Сокрытие данных** (information hiding) — ограничение прямого доступа к внутренним деталям. Это дополнительный инструмент поверх инкапсуляции.

Можно иметь инкапсуляцию без сокрытия — публичные атрибуты в классе.
Сокрытие имеет смысл только внутри инкапсуляции — скрывать нечто можно лишь внутри объекта.

В Python сокрытие реализуется через соглашения об именовании.

## Уровни доступа в Python

Python не запрещает доступ к атрибутам жёстко — всё построено на договорённостях.

### Публичные атрибуты — без подчёркивания

Доступны всем, читать и изменять свободно:

```python
class Car:
    def __init__(self, brand, speed):
        self.brand = brand   # Публичный
        self.speed = speed   # Публичный

car = Car("Toyota", 100)
print(car.brand)  # Toyota
car.speed = 120   # Ок
```

### Защищённые атрибуты — одно подчёркивание `_`

Сигнал: «внутренняя деталь, трогать не стоит». Технически доступны, но по договорённости — только для внутреннего использования:

```python
class BankAccount:
    def __init__(self, owner, balance):
        self.owner = owner
        self._balance = balance   # "Руками не трогать"

    def get_balance(self):
        return self._balance

account = BankAccount("Анна", 1000)
print(account.get_balance())  # 1000 — правильный способ
print(account._balance)       # 1000 — работает, но так не принято
```

### Приватные атрибуты — двойное подчёркивание `__`

Python применяет **name mangling** — переименовывает атрибут, чтобы к нему не обратились случайно снаружи:

```python
class BankAccount:
    def __init__(self, owner, balance):
        self.owner = owner
        self.__balance = balance   # Приватный

    def get_balance(self):
        return self.__balance      # Внутри класса — доступен

account = BankAccount("Анна", 1000)
print(account.get_balance())  # 1000 — работает
print(account.__balance)      # AttributeError!
```

Python переименовывает `__balance` в `_BankAccount__balance`:

```python
print(account._BankAccount__balance)  # 1000 — технически можно, но не нужно
```

## Свойства (`@property`)

`@property` — способ контролировать доступ к атрибутам с добавлением логики. Позволяет обращаться к методу **как к атрибуту** — без скобок.

### Геттер

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def radius(self):
        return self._radius

c = Circle(5)
print(c.radius)  # 5 — вызываем как атрибут, без скобок
```

### Геттер + сеттер

Сеттер позволяет добавить проверку при установке значения:

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def radius(self):
        return self._radius

    @radius.setter
    def radius(self, value):
        if value < 0:
            raise ValueError("Радиус не может быть отрицательным")
        self._radius = value

    @property
    def area(self):
        return 3.14159 * self._radius ** 2


c = Circle(5)
print(c.radius)  # 5
print(c.area)    # 78.53975

c.radius = 10    # Вызывает сеттер
print(c.area)    # 314.159

c.radius = -1    # ValueError: Радиус не может быть отрицательным
```

### Вычисляемые свойства (только геттер)

Свойство без сеттера — только для чтения:

```python
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height

    @property
    def area(self):
        """Вычисляется на лету, не хранится"""
        return self.width * self.height

    @property
    def perimeter(self):
        return 2 * (self.width + self.height)


r = Rectangle(4, 6)
print(r.area)       # 24
print(r.perimeter)  # 20

r.width = 10
print(r.area)       # 60 — пересчиталось автоматически

r.area = 100        # AttributeError: только для чтения
```

## 🎯 Пример с реальной задачей

Класс пользователя с валидацией данных:

```python
class User:
    def __init__(self, username, email, age):
        self.username = username
        self.email = email    # Вызовет сеттер
        self.age = age        # Вызовет сеттер

    @property
    def email(self):
        return self._email

    @email.setter
    def email(self, value):
        if "@" not in value:
            raise ValueError(f"Некорректный email: {value}")
        self._email = value.lower()

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, value):
        if not isinstance(value, int) or value < 0 or value > 150:
            raise ValueError(f"Некорректный возраст: {value}")
        self._age = value

    @property
    def is_adult(self):
        """Только для чтения — вычисляется автоматически"""
        return self._age >= 18

    def __str__(self):
        return f"{self.username} ({self.email}), {self.age} лет"


user = User("anna", "Anna@Example.com", 25)
print(user)            # anna (anna@example.com), 25 лет
print(user.is_adult)   # True

user.age = 16
print(user.is_adult)   # False

user.email = "bad"     # ValueError: Некорректный email
```

## 🔎 Проверка на ходу

```python
>>> class Temperature:
...     def __init__(self, celsius):
...         self._celsius = celsius
...     @property
...     def celsius(self):
...         return self._celsius
...     @celsius.setter
...     def celsius(self, value):
...         if value < -273.15:
...             raise ValueError("Ниже абсолютного нуля!")
...         self._celsius = value
...     @property
...     def fahrenheit(self):
...         return self._celsius * 9/5 + 32
...
>>> t = Temperature(100)
>>> t.fahrenheit
212.0
>>> t.celsius = 0
>>> t.fahrenheit
32.0
>>> t.celsius = -300
ValueError: Ниже абсолютного нуля!
```

## Резюме

- **Инкапсуляция** — объединение данных и методов в классе; объект управляет своим состоянием сам
- **Сокрытие данных** — дополнительный инструмент, ограничивающий прямой доступ к деталям реализации
- `атрибут` — публичный, доступен всем
- `_атрибут` — защищённый, соглашение «не трогать снаружи»
- `__атрибут` — приватный, Python переименовывает его (name mangling)
- `@property` — доступ к методу как к атрибуту, без скобок
- `@имя.setter` — добавляем проверку при установке значения
- Свойство без сеттера — только для чтения

---

[[06_03_init_and_self|← 🔧 `__init__` и `self`]] | [[06_05_inheritance|🧬 Наследование →]]
