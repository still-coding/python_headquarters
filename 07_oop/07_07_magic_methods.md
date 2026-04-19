[[07_06_polymorphism|← 🦆 Полиморфизм]] | [[07_08_class_and_static_methods|⚙️ `@classmethod` и `@staticmethod` →]]

---
# ✨ Магические методы
```toc
minLevel: 2
```

## Что такое магические методы?

**Магические методы** (dunder methods, от *double underscore*) — это специальные методы с двойными подчёркиваниями с обеих сторон: `__имя__`.

Python вызывает их **автоматически** в ответ на определённые операции. Вы уже знаете один такой метод — `__init__`. Но их гораздо больше.

```python
class MyClass:
    def __init__(self):       # Вызывается при создании объекта
        pass

    def __str__(self):        # Вызывается при print()
        pass

    def __len__(self):        # Вызывается при len()
        pass

    def __add__(self, other): # Вызывается при +
        pass
```

Магические методы позволяют вашим объектам работать со встроенными функциями и операторами Python — `print()`, `len()`, `+`, `==`, `[]` и другими.

## `__str__` и `__repr__`

Оба метода отвечают за строковое представление объекта, но используются в разных ситуациях.

**`__str__`** — для людей. Вызывается при `print()` и `str()`:

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __str__(self):
        return f"{self.name}, {self.age} лет"


p = Person("Анна", 25)
print(p)        # Анна, 25 лет
print(str(p))   # Анна, 25 лет
```

**`__repr__`** — для разработчика. Должен показывать, как воссоздать объект. Вызывается в REPL и при отладке:

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __str__(self):
        return f"{self.name}, {self.age} лет"

    def __repr__(self):
        return f"Person(name={self.name!r}, age={self.age!r})"


p = Person("Анна", 25)
print(p)    # Анна, 25 лет          — __str__
print(repr(p))  # Person(name='Анна', age=25)  — __repr__
```

Если `__str__` не определён, Python использует `__repr__`. Поэтому минимум — реализовать `__repr__`.

## `__len__` и `__bool__`

**`__len__`** — вызывается при `len()`:

```python
class Playlist:
    def __init__(self):
        self.tracks = []

    def add(self, track):
        self.tracks.append(track)

    def __len__(self):
        return len(self.tracks)


pl = Playlist()
pl.add("Track 1")
pl.add("Track 2")
pl.add("Track 3")

print(len(pl))  # 3
```

**`__bool__`** — вызывается при проверке в `if`. Если не определён, Python использует `__len__` (0 = False, остальное = True):

```python
class Playlist:
    def __init__(self):
        self.tracks = []

    def __len__(self):
        return len(self.tracks)

    def __bool__(self):
        return len(self.tracks) > 0


pl = Playlist()

if pl:
    print("Есть треки")
else:
    print("Плейлист пуст")  # Плейлист пуст

pl.tracks.append("Track 1")

if pl:
    print("Есть треки")     # Есть треки
```

## `__getitem__`, `__setitem__`, `__contains__`

Эти методы делают объект похожим на коллекцию — позволяют использовать `[]`, `in`.

```python
class Library:
    def __init__(self):
        self._books = {}

    def __setitem__(self, key, value):
        """Позволяет: library["Python"] = "Лутц" """
        self._books[key] = value

    def __getitem__(self, key):
        """Позволяет: library["Python"] """
        return self._books[key]

    def __contains__(self, key):
        """Позволяет: "Python" in library """
        return key in self._books

    def __len__(self):
        return len(self._books)


lib = Library()
lib["Python"] = "Марк Лутц"
lib["Clean Code"] = "Роберт Мартин"

print(lib["Python"])           # Марк Лутц
print("Python" in lib)         # True
print("Java" in lib)           # False
print(len(lib))                # 2
```

## Операторы сравнения

```python
class Temperature:
    def __init__(self, celsius):
        self.celsius = celsius

    def __eq__(self, other):    # ==
        return self.celsius == other.celsius

    def __lt__(self, other):    # <
        return self.celsius < other.celsius

    def __le__(self, other):    # <=
        return self.celsius <= other.celsius

    def __gt__(self, other):    # >
        return self.celsius > other.celsius

    def __str__(self):
        return f"{self.celsius}°C"


t1 = Temperature(100)
t2 = Temperature(20)
t3 = Temperature(100)

print(t1 == t3)  # True
print(t1 > t2)   # True
print(t2 < t1)   # True

# Теперь можно сортировать!
temps = [Temperature(50), Temperature(10), Temperature(30)]
print(sorted(temps))  # [10°C, 30°C, 50°C]
```

## Арифметические операторы

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):   # +
        return Vector(self.x + other.x, self.y + other.y)

    def __sub__(self, other):   # -
        return Vector(self.x - other.x, self.y - other.y)

    def __mul__(self, scalar):  # * (умножение на число)
        return Vector(self.x * scalar, self.y * scalar)

    def __neg__(self):          # унарный минус (- v)
        return Vector(-self.x, -self.y)

    def __abs__(self):          # abs()
        return (self.x ** 2 + self.y ** 2) ** 0.5

    def __repr__(self):
        return f"Vector({self.x}, {self.y})"


v1 = Vector(1, 2)
v2 = Vector(3, 4)

print(v1 + v2)   # Vector(4, 6)
print(v2 - v1)   # Vector(2, 2)
print(v1 * 3)    # Vector(3, 6)
print(-v1)       # Vector(-1, -2)
print(abs(v2))   # 5.0
```

## `__call__` — объект как функция

Позволяет вызывать объект как функцию:

```python
class Multiplier:
    def __init__(self, factor):
        self.factor = factor

    def __call__(self, value):
        return value * self.factor


times3 = Multiplier(3)
times10 = Multiplier(10)

print(times3(5))    # 15
print(times10(7))   # 70
print(callable(times3))  # True
```

## 🎯 Пример с реальной задачей

Класс для работы с деньгами:

```python
class Money:
    def __init__(self, amount, currency="RUB"):
        self.amount = round(amount, 2)
        self.currency = currency

    def __str__(self):
        return f"{self.amount:.2f} {self.currency}"

    def __repr__(self):
        return f"Money({self.amount!r}, {self.currency!r})"

    def __add__(self, other):
        if self.currency != other.currency:
            raise ValueError("Нельзя складывать разные валюты")
        return Money(self.amount + other.amount, self.currency)

    def __sub__(self, other):
        if self.currency != other.currency:
            raise ValueError("Нельзя вычитать разные валюты")
        return Money(self.amount - other.amount, self.currency)

    def __mul__(self, factor):
        return Money(self.amount * factor, self.currency)

    def __eq__(self, other):
        return self.amount == other.amount and self.currency == other.currency

    def __lt__(self, other):
        if self.currency != other.currency:
            raise ValueError("Нельзя сравнивать разные валюты")
        return self.amount < other.amount

    def __bool__(self):
        return self.amount > 0


price = Money(100)
tax = Money(20)
total = price + tax

print(total)          # 120.00 RUB
print(total * 2)      # 240.00 RUB
print(price < total)  # True
print(bool(Money(0))) # False

wallet = [Money(500), Money(100), Money(300)]
print(sorted(wallet)) # [100.00 RUB, 300.00 RUB, 500.00 RUB]
```

## Шпаргалка по магическим методам

| Метод | Когда вызывается |
|---|---|
| `__init__` | `obj = Class()` |
| `__str__` | `print(obj)`, `str(obj)` |
| `__repr__` | REPL, `repr(obj)` |
| `__len__` | `len(obj)` |
| `__bool__` | `if obj:` |
| `__getitem__` | `obj[key]` |
| `__setitem__` | `obj[key] = value` |
| `__contains__` | `x in obj` |
| `__eq__` | `obj == other` |
| `__lt__` | `obj < other` |
| `__add__` | `obj + other` |
| `__sub__` | `obj - other` |
| `__mul__` | `obj * other` |
| `__neg__` | `-obj` |
| `__abs__` | `abs(obj)` |
| `__call__` | `obj()` |

## 🔎 Проверка на ходу

```python
>>> class Box:
...     def __init__(self, items):
...         self.items = items
...     def __len__(self):
...         return len(self.items)
...     def __contains__(self, item):
...         return item in self.items
...     def __getitem__(self, index):
...         return self.items[index]
...
>>> b = Box(["яблоко", "груша", "банан"])
>>> len(b)
3
>>> "груша" in b
True
>>> b[0]
'яблоко'
```

## Резюме

- Магические методы — это `__имя__`, Python вызывает их автоматически
- `__str__` — читаемое представление для людей, `__repr__` — для разработчика
- `__len__` и `__bool__` — делают объект совместимым с `len()` и `if`
- `__getitem__`, `__setitem__`, `__contains__` — делают объект похожим на коллекцию
- Операторы сравнения и арифметики — позволяют использовать `==`, `<`, `+`, `-` и другие
- `__call__` — позволяет вызывать объект как функцию
- Через магические методы ваши классы органично встраиваются в Python

---

[[07_06_polymorphism|← 🦆 Полиморфизм]] | [[07_08_class_and_static_methods|⚙️ `@classmethod` и `@staticmethod` →]]
