[[06_13_dataclass|← 🗂️ `dataclass`]]

---
# 🔩 `__slots__`
```toc
minLevel: 2
```

## Как Python хранит атрибуты по умолчанию

Обычно атрибуты объекта хранятся в словаре `__dict__`:

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

p = Point(1, 2)
print(p.__dict__)   # {'x': 1, 'y': 2}

# Можно добавить любой атрибут в любой момент
p.z = 3
p.color = "red"
print(p.__dict__)   # {'x': 1, 'y': 2, 'z': 3, 'color': 'red'}
```

Словарь гибкий, но занимает память — особенно когда объектов миллионы.

## Что такое `__slots__`

`__slots__` — это явное объявление списка допустимых атрибутов. Python заменяет словарь `__dict__` на компактное внутреннее представление:

```python
class Point:
    __slots__ = ("x", "y")   # Только эти атрибуты разрешены

    def __init__(self, x, y):
        self.x = x
        self.y = y

p = Point(1, 2)
print(p.x)   # 1
print(p.y)   # 2

# Добавить новый атрибут — нельзя
p.z = 3      # AttributeError: 'Point' object has no attribute 'z'

# __dict__ больше нет
print(p.__dict__)   # AttributeError: 'Point' object has no attribute '__dict__'
```

## Зачем это нужно

### Экономия памяти

`__slots__` экономит память — особенно заметно при большом количестве объектов:

```python
import sys

class WithDict:
    def __init__(self, x, y):
        self.x = x
        self.y = y

class WithSlots:
    __slots__ = ("x", "y")
    def __init__(self, x, y):
        self.x = x
        self.y = y

obj_dict  = WithDict(1, 2)
obj_slots = WithSlots(1, 2)

print(sys.getsizeof(obj_dict))    # ~48 байт + размер __dict__ (~232 байт)
print(sys.getsizeof(obj_slots))   # ~56 байт — без словаря
```

На одном объекте разница невелика. Но если создать миллион объектов:

```python
# ~280 МБ vs ~56 МБ — разница в 5 раз
points_dict  = [WithDict(i, i) for i in range(1_000_000)]
points_slots = [WithSlots(i, i) for i in range(1_000_000)]
```

### Небольшое ускорение доступа

Доступ к атрибутам через `__slots__` чуть быстрее — Python не ищет в словаре, а обращается напрямую.

### Защита от опечаток

```python
class Config:
    __slots__ = ("host", "port", "timeout")

    def __init__(self, host, port):
        self.host = host
        self.port = port
        self.timeout = 30

cfg = Config("localhost", 5432)
cfg.timeuot = 60   # AttributeError — опечатка поймана сразу!
                   # Без __slots__ молча создался бы новый атрибут
```

## `__slots__` и наследование

### Дочерний класс без `__slots__`

Если дочерний класс не объявляет `__slots__` — у него снова появляется `__dict__`:

```python
class Base:
    __slots__ = ("x", "y")

class Child(Base):
    pass   # Нет __slots__ — __dict__ возвращается

c = Child()
c.x = 1       # Из __slots__ родителя
c.z = 99      # Из __dict__ ребёнка — работает
print(c.__dict__)   # {'z': 99}
```

### Дочерний класс со своими `__slots__`

Объявляйте только **новые** поля — родительские уже есть:

```python
class Animal:
    __slots__ = ("name", "age")

    def __init__(self, name, age):
        self.name = name
        self.age = age

class Dog(Animal):
    __slots__ = ("breed",)   # Только новое поле, не повторяем name и age

    def __init__(self, name, age, breed):
        super().__init__(name, age)
        self.breed = breed


dog = Dog("Рекс", 3, "Овчарка")
print(dog.name)    # Рекс
print(dog.breed)   # Овчарка
```

## `__slots__` и `dataclass`

Начиная с Python 3.10 можно комбинировать:

```python
from dataclasses import dataclass


@dataclass(slots=True)   # Python 3.10+
class Point:
    x: float
    y: float


p = Point(1.0, 2.0)
print(p)       # Point(x=1.0, y=2.0)
p.z = 3.0      # AttributeError — slots работают
```

До Python 3.10 нужно объявлять вручную:

```python
from dataclasses import dataclass


@dataclass
class Point:
    __slots__ = ("x", "y")
    x: float
    y: float
```

## Когда использовать `__slots__`

**Используйте `__slots__` когда:**
- Создаёте **очень много** экземпляров (тысячи и более)
- Нужна экономия памяти
- Хотите защититься от случайного добавления атрибутов
- Класс — простой контейнер данных с фиксированной структурой

**Не используйте `__slots__` когда:**
- Объектов немного — оптимизация не даст заметного эффекта
- Нужна гибкость — динамическое добавление атрибутов
- Используете миксины или сложное множественное наследование

## 🔎 Проверка на ходу

```python
>>> class Pixel:
...     __slots__ = ("x", "y", "color")
...     def __init__(self, x, y, color):
...         self.x = x
...         self.y = y
...         self.color = color
...
>>> px = Pixel(10, 20, "red")
>>> px.x
10
>>> px.color = "blue"   # Изменить существующий — можно
>>> px.alpha = 255      # Добавить новый — нельзя
AttributeError: 'Pixel' object has no attribute 'alpha'
>>> hasattr(px, '__dict__')
False
```

## Резюме

- По умолчанию атрибуты хранятся в `__dict__` — гибко, но расходует память
- `__slots__` заменяет словарь на компактное представление
- Даёт экономию памяти и небольшое ускорение — заметно при миллионах объектов
- Запрещает добавлять атрибуты, не указанные в `__slots__` — защита от опечаток
- В дочернем классе объявляйте только **новые** поля
- В Python 3.10+ можно использовать `@dataclass(slots=True)`
- Применяйте когда объектов много и структура фиксирована

---

[[06_13_dataclass|← 🗂️ `dataclass`]]
