[[07_magic_methods|← ✨ Магические методы]] | [[09_composition_vs_inheritance|🔗 Композиция vs наследование →]]

---
# ⚙️ `@classmethod` и `@staticmethod`
```toc
minLevel: 2
```

## Три вида методов

В Python у класса может быть три вида методов:

| Вид | Декоратор | Первый параметр | Доступ |
|---|---|---|---|
| Метод экземпляра | нет | `self` — объект | Атрибуты объекта и класса |
| Метод класса | `@classmethod` | `cls` — класс | Только атрибуты класса |
| Статический метод | `@staticmethod` | нет | Ни к объекту, ни к классу |

```python
class MyClass:
    class_attr = "атрибут класса"

    def instance_method(self):
        """Обычный метод — знает об объекте и классе"""
        return f"self={self}, class_attr={self.class_attr}"

    @classmethod
    def class_method(cls):
        """Метод класса — знает о классе, но не об объекте"""
        return f"cls={cls}, class_attr={cls.class_attr}"

    @staticmethod
    def static_method():
        """Статический метод — не знает ни об объекте, ни о классе"""
        return "Просто функция внутри класса"
```

## Методы экземпляра — напоминание

Обычный метод, который мы уже хорошо знаем. Получает `self` — конкретный объект:

```python
class Circle:
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return 3.14159 * self.radius ** 2


c = Circle(5)
print(c.area())  # 78.53975
```

## `@classmethod` — метод класса

Получает `cls` — сам класс, а не объект. Вызывается через класс (или объект, но это редко нужно).

Главное применение — **альтернативные конструкторы**: создание объекта из данных другого формата.

```python
class Date:
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day

    @classmethod
    def from_string(cls, date_string):
        """Создать дату из строки '2024-01-15'"""
        year, month, day = map(int, date_string.split("-"))
        return cls(year, month, day)   # cls — это Date

    @classmethod
    def today(cls):
        """Создать дату из сегодняшней даты"""
        import datetime
        d = datetime.date.today()
        return cls(d.year, d.month, d.day)

    def __str__(self):
        return f"{self.year}-{self.month:02d}-{self.day:02d}"


# Обычный способ
d1 = Date(2024, 1, 15)

# Через альтернативный конструктор
d2 = Date.from_string("2024-06-20")
d3 = Date.today()

print(d1)  # 2024-01-15
print(d2)  # 2024-06-20
print(d3)  # сегодняшняя дата
```

### `@classmethod` и наследование

`cls` всегда указывает на тот класс, через который вызван метод — это делает `@classmethod` корректным при наследовании:

```python
class Animal:
    def __init__(self, name):
        self.name = name

    @classmethod
    def create(cls, name):
        return cls(name)   # cls — это тот класс, через который вызвали

    def __repr__(self):
        return f"{self.__class__.__name__}({self.name!r})"


class Dog(Animal):
    pass

class Cat(Animal):
    pass


dog = Dog.create("Рекс")
cat = Cat.create("Мурка")

print(dog)  # Dog('Рекс')   — создался Dog, не Animal
print(cat)  # Cat('Мурка')
```

### Счётчик объектов через `@classmethod`

```python
class Connection:
    _count = 0   # Атрибут класса

    def __init__(self, host):
        self.host = host
        Connection._count += 1

    @classmethod
    def get_count(cls):
        return cls._count

    @classmethod
    def reset_count(cls):
        cls._count = 0


c1 = Connection("db1")
c2 = Connection("db2")
c3 = Connection("db3")

print(Connection.get_count())  # 3
Connection.reset_count()
print(Connection.get_count())  # 0
```

## `@staticmethod` — статический метод

Не получает ни `self`, ни `cls`. По сути — обычная функция, которую логически разместили внутри класса.

Используется для **вспомогательных операций**, которые связаны с классом по смыслу, но не нуждаются в доступе к его данным:

```python
class Temperature:
    def __init__(self, celsius):
        self.celsius = celsius

    @staticmethod
    def celsius_to_fahrenheit(c):
        """Перевод градусов — не зависит от состояния объекта"""
        return c * 9/5 + 32

    @staticmethod
    def fahrenheit_to_celsius(f):
        return (f - 32) * 5/9

    def to_fahrenheit(self):
        return Temperature.celsius_to_fahrenheit(self.celsius)


# Можно вызывать без создания объекта
print(Temperature.celsius_to_fahrenheit(100))  # 212.0
print(Temperature.fahrenheit_to_celsius(32))   # 0.0

# Или через объект
t = Temperature(25)
print(t.to_fahrenheit())  # 77.0
```

### Валидация как статический метод

```python
class User:
    def __init__(self, username, email):
        if not self.is_valid_email(email):
            raise ValueError(f"Некорректный email: {email}")
        self.username = username
        self.email = email

    @staticmethod
    def is_valid_email(email):
        """Проверка формата email — не зависит от объекта"""
        return "@" in email and "." in email.split("@")[-1]

    @staticmethod
    def is_valid_username(username):
        """Проверка имени пользователя"""
        return len(username) >= 3 and username.isalnum()


# Используем до создания объекта
print(User.is_valid_email("anna@example.com"))  # True
print(User.is_valid_email("bad-email"))          # False
print(User.is_valid_username("an"))              # False — слишком короткое

user = User("anna", "anna@example.com")  # ок
# User("x", "bad")  # ValueError
```

## Когда что использовать?

```python
class Pizza:
    SIZES = ["small", "medium", "large"]

    def __init__(self, size, toppings):
        self.size = size
        self.toppings = toppings

    # Метод экземпляра — работает с конкретной пиццей
    def describe(self):
        tops = ", ".join(self.toppings)
        return f"Пицца {self.size}: {tops}"

    # Метод класса — альтернативный конструктор
    @classmethod
    def margherita(cls, size="medium"):
        return cls(size, ["томат", "моцарелла", "базилик"])

    @classmethod
    def pepperoni(cls, size="medium"):
        return cls(size, ["томат", "моцарелла", "пепперони"])

    # Статический метод — утилита, связанная с пиццей по смыслу
    @staticmethod
    def is_valid_size(size):
        return size in Pizza.SIZES


# Метод экземпляра — нужен объект
p1 = Pizza("large", ["грибы", "лук"])
print(p1.describe())       # Пицца large: грибы, лук

# Метод класса — создаёт объект
p2 = Pizza.margherita()
p3 = Pizza.pepperoni("large")
print(p2.describe())       # Пицца medium: томат, моцарелла, базилик
print(p3.describe())       # Пицца large: томат, моцарелла, пепперони

# Статический метод — не нужен объект
print(Pizza.is_valid_size("large"))   # True
print(Pizza.is_valid_size("giant"))   # False
```

## 🔎 Проверка на ходу

```python
>>> class Counter:
...     _total = 0
...     def __init__(self):
...         Counter._total += 1
...         self.id = Counter._total
...     @classmethod
...     def total(cls):
...         return cls._total
...     @staticmethod
...     def description():
...         return "Простой счётчик объектов"
...
>>> Counter.description()
'Простой счётчик объектов'
>>> a = Counter()
>>> b = Counter()
>>> Counter.total()
2
>>> a.id, b.id
(1, 2)
```

## Резюме

- **Метод экземпляра** — получает `self`, работает с конкретным объектом. Используется чаще всего
- **`@classmethod`** — получает `cls`, работает с классом. Главное применение — альтернативные конструкторы
- **`@staticmethod`** — не получает ничего. Используется для вспомогательных функций, логически связанных с классом
- `@classmethod` корректно работает при наследовании — `cls` всегда указывает на нужный класс
- Статический метод можно вызывать и через класс, и через объект

---

[[07_magic_methods|← ✨ Магические методы]] | [[09_composition_vs_inheritance|🔗 Композиция vs наследование →]]
