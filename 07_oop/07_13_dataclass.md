[[07_12_object_lifecycle|← ♻️ Жизненный цикл объекта]] | [[07_14_slots|🔩 `__slots__` →]]

---
# 🗂️ `dataclass`
```toc
minLevel: 2
```

## Проблема: бойлерплейт в классах

Обычный класс с данными требует много повторяющегося кода:

```python
class Point:
    def __init__(self, x, y, z):
        self.x = x
        self.y = y
        self.z = z

    def __repr__(self):
        return f"Point(x={self.x}, y={self.y}, z={self.z})"

    def __eq__(self, other):
        return self.x == other.x and self.y == other.y and self.z == other.z
```

Три метода, и каждый просто перечисляет одни и те же поля. Это утомительно и легко допустить ошибку.

`@dataclass` генерирует этот код автоматически.

## Базовый пример

```python
from dataclasses import dataclass


@dataclass
class Point:
    x: float
    y: float
    z: float


p1 = Point(1.0, 2.0, 3.0)
p2 = Point(1.0, 2.0, 3.0)

print(p1)        # Point(x=1.0, y=2.0, z=3.0)  — __repr__ бесплатно
print(p1 == p2)  # True  — __eq__ бесплатно
```

`@dataclass` автоматически генерирует `__init__`, `__repr__` и `__eq__` на основе аннотаций полей.

## Значения по умолчанию

```python
from dataclasses import dataclass


@dataclass
class User:
    username: str
    email: str
    role: str = "user"        # Простое значение по умолчанию
    active: bool = True


u1 = User("anna", "anna@example.com")
u2 = User("ivan", "ivan@example.com", role="admin")

print(u1)  # User(username='anna', email='anna@example.com', role='user', active=True)
print(u2)  # User(username='ivan', email='ivan@example.com', role='admin', active=True)
```

### Изменяемые значения по умолчанию — `field()`

Нельзя использовать список или словарь как значение по умолчанию напрямую — это классическая ловушка Python:

```python
# ❌ Ошибка — все объекты будут делить один список
@dataclass
class Team:
    name: str
    members: list = []   # ValueError: mutable default is not allowed
```

Используйте `field(default_factory=...)`:

```python
from dataclasses import dataclass, field


@dataclass
class Team:
    name: str
    members: list = field(default_factory=list)    # Новый список для каждого объекта
    metadata: dict = field(default_factory=dict)


t1 = Team("Backend")
t2 = Team("Frontend")

t1.members.append("Анна")
t2.members.append("Иван")

print(t1.members)  # ['Анна']
print(t2.members)  # ['Иван']  — независимые списки
```

## Параметры `@dataclass`

```python
@dataclass(order=True, frozen=True)
class Version:
    major: int
    minor: int
    patch: int
```

Основные параметры:

| Параметр | По умолчанию | Что даёт |
|---|---|---|
| `repr=True` | `True` | Генерирует `__repr__` |
| `eq=True` | `True` | Генерирует `__eq__` |
| `order=False` | `False` | Генерирует `__lt__`, `__le__`, `__gt__`, `__ge__` |
| `frozen=False` | `False` | Делает объект неизменяемым |

### `order=True` — сортировка

```python
from dataclasses import dataclass


@dataclass(order=True)
class Version:
    major: int
    minor: int
    patch: int


versions = [Version(1, 10, 0), Version(2, 0, 1), Version(1, 9, 5)]
print(sorted(versions))
# [Version(major=1, minor=9, patch=5),
#  Version(major=1, minor=10, patch=0),
#  Version(major=2, minor=0, patch=1)]
```

### `frozen=True` — неизменяемый объект

```python
from dataclasses import dataclass


@dataclass(frozen=True)
class Point:
    x: float
    y: float


p = Point(1.0, 2.0)
p.x = 5.0   # FrozenInstanceError: cannot assign to field 'x'

# Замороженный dataclass можно использовать как ключ словаря или элемент множества
locations = {Point(0, 0), Point(1, 1)}
cache = {Point(3, 4): "some value"}
```

## `field()` — настройка отдельных полей

```python
from dataclasses import dataclass, field


@dataclass
class Product:
    name: str
    price: float
    tags: list = field(default_factory=list)

    # Поле не попадает в __init__ и __repr__
    _discount: float = field(default=0.0, init=False, repr=False)

    # Поле не попадает в __repr__ (например, пароль)
    internal_id: str = field(default="", repr=False)

    def set_discount(self, percent):
        self._discount = percent / 100

    def final_price(self):
        return self.price * (1 - self._discount)


p = Product("Книга", 500.0)
print(p)            # Product(name='Книга', price=500.0, tags=[])
p.set_discount(10)
print(p.final_price())  # 450.0
```

## Методы в `dataclass`

`@dataclass` не запрещает добавлять свои методы:

```python
from dataclasses import dataclass
from math import sqrt


@dataclass
class Vector:
    x: float
    y: float

    def length(self) -> float:
        return sqrt(self.x ** 2 + self.y ** 2)

    def __add__(self, other: "Vector") -> "Vector":
        return Vector(self.x + other.x, self.y + other.y)

    def __mul__(self, scalar: float) -> "Vector":
        return Vector(self.x * scalar, self.y * scalar)


v1 = Vector(3.0, 4.0)
v2 = Vector(1.0, 2.0)

print(v1.length())   # 5.0
print(v1 + v2)       # Vector(x=4.0, y=6.0)
print(v1 * 2)        # Vector(x=6.0, y=8.0)
```

## `__post_init__` — логика после инициализации

Если нужна валидация или вычисляемые поля — используйте `__post_init__`:

```python
from dataclasses import dataclass, field


@dataclass
class Rectangle:
    width: float
    height: float
    area: float = field(init=False)  # Вычисляется, не передаётся в __init__

    def __post_init__(self):
        if self.width <= 0 or self.height <= 0:
            raise ValueError("Стороны должны быть положительными")
        self.area = self.width * self.height


r = Rectangle(4.0, 6.0)
print(r)        # Rectangle(width=4.0, height=6.0, area=24.0)
print(r.area)   # 24.0

Rectangle(-1, 5)  # ValueError: Стороны должны быть положительными
```

## `dataclass` vs обычный класс

```python
# Обычный класс — много кода вручную
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    def __repr__(self):
        return f"Point(x={self.x}, y={self.y})"
    def __eq__(self, other):
        return self.x == other.x and self.y == other.y

# dataclass — то же самое, в 3 строки
@dataclass
class Point:
    x: float
    y: float
```

Используйте `@dataclass` когда класс — это **контейнер данных**: хранит поля, нужен `__repr__` и сравнение. Для классов с богатым поведением обычный класс может быть понятнее.

## 🎯 Пример с реальной задачей

```python
from dataclasses import dataclass, field
from datetime import datetime


@dataclass
class Message:
    sender: str
    text: str
    timestamp: datetime = field(default_factory=datetime.now)
    read: bool = field(default=False, repr=False)

    def mark_read(self):
        self.read = True

    def preview(self) -> str:
        status = "✓" if self.read else "○"
        return f"{status} [{self.sender}]: {self.text[:30]}"


@dataclass
class Chat:
    name: str
    messages: list = field(default_factory=list)

    def send(self, sender: str, text: str):
        self.messages.append(Message(sender, text))

    def unread_count(self) -> int:
        return sum(1 for m in self.messages if not m.read)


chat = Chat("Рабочий чат")
chat.send("Анна", "Всем привет!")
chat.send("Иван", "Привет! Как дела?")

print(chat.unread_count())          # 2
print(chat.messages[0].preview())   # ○ [Анна]: Всем привет!

chat.messages[0].mark_read()
print(chat.unread_count())          # 1
print(chat.messages[0].preview())   # ✓ [Анна]: Всем привет!
```

## 🔎 Проверка на ходу

```python
>>> from dataclasses import dataclass
>>> @dataclass
... class Color:
...     r: int
...     g: int
...     b: int
...     def to_hex(self):
...         return f"#{self.r:02x}{self.g:02x}{self.b:02x}"
...
>>> red = Color(255, 0, 0)
>>> red
Color(r=255, g=0, b=0)
>>> red.to_hex()
'#ff0000'
>>> red == Color(255, 0, 0)
True
```

## Резюме

- `@dataclass` автоматически генерирует `__init__`, `__repr__`, `__eq__`
- Поля объявляются через аннотации типов: `name: str`
- Значения по умолчанию: простые — напрямую, изменяемые — через `field(default_factory=...)`
- `order=True` — даёт сортировку; `frozen=True` — делает объект неизменяемым и хешируемым
- `field()` — тонкая настройка отдельных полей (`init=False`, `repr=False`)
- `__post_init__` — валидация и вычисляемые поля после инициализации
- Используйте `@dataclass` для классов-контейнеров данных

---

[[07_12_object_lifecycle|← ♻️ Жизненный цикл объекта]] | [[07_14_slots|🔩 `__slots__` →]]
