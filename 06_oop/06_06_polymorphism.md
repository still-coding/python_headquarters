[[06_05_inheritance|← 🧬 Наследование]] | [[06_07_magic_methods|✨ Магические методы →]]

---
# 🦆 Полиморфизм
```toc
minLevel: 2
```

## Что такое полиморфизм?

**Полиморфизм** — это способность разных объектов отвечать на один и тот же вызов по-своему.

Слово происходит от греческого «много форм». Идея простая: один интерфейс — разное поведение.

Аналогия из жизни: команда «говори» работает для всех животных, но кошка мяукает, собака лает, корова мычит. Вы не думаете о том, кто перед вами — вы просто говорите «говори».

```python
class Cat:
    def speak(self):
        return "Мяу!"

class Dog:
    def speak(self):
        return "Гав!"

class Cow:
    def speak(self):
        return "Муу!"

animals = [Cat(), Dog(), Cow()]

for animal in animals:
    print(animal.speak())   # Каждый отвечает по-своему
# Мяу!
# Гав!
# Муу!
```

Цикл не знает, кто именно перед ним — он просто вызывает `speak()`. Каждый объект сам знает, как на это ответить.

## Полиморфизм через наследование

Самый распространённый вид: дочерние классы переопределяют метод родителя:

```python
class Shape:
    def area(self):
        return 0

    def describe(self):
        return f"Площадь: {self.area()}"   # Вызывает нужную версию автоматически


class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return 3.14159 * self.radius ** 2


class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height


class Triangle(Shape):
    def __init__(self, base, height):
        self.base = base
        self.height = height

    def area(self):
        return 0.5 * self.base * self.height


shapes = [Circle(5), Rectangle(4, 6), Triangle(3, 8)]

for shape in shapes:
    print(shape.describe())
# Площадь: 78.53975
# Площадь: 24
# Площадь: 12.0
```

`describe()` написан один раз в родителе — но работает правильно для каждого дочернего класса. Это и есть полиморфизм.

## Утиная типизация

В Python полиморфизм не требует общего родителя. Достаточно, чтобы у объектов был нужный метод или атрибут. Это называется **утиная типизация** (duck typing).

> «Если что-то ходит как утка и крякает как утка — это утка.»

```python
class Dog:
    def speak(self):
        return "Гав!"

class Robot:
    def speak(self):
        return "Бип-буп!"

class Person:
    def speak(self):
        return "Привет!"

# Никакого общего родителя — просто одинаковый интерфейс
speakers = [Dog(), Robot(), Person()]

for s in speakers:
    print(s.speak())
# Гав!
# Бип-буп!
# Привет!
```

Python не проверяет тип объекта — он просто вызывает метод. Если метод есть — всё работает.

### Практический пример утиной типизации

```python
class FileLogger:
    def log(self, message):
        with open("log.txt", "a") as f:
            f.write(message + "\n")

class ConsoleLogger:
    def log(self, message):
        print(f"[LOG] {message}")

class SilentLogger:
    def log(self, message):
        pass   # Ничего не делаем — но интерфейс соблюдён


def process_order(order_id, logger):
    logger.log(f"Начало обработки заказа {order_id}")
    # ... логика обработки ...
    logger.log(f"Заказ {order_id} обработан")


# Меняем поведение, не меняя функцию process_order
process_order(42, ConsoleLogger())
# [LOG] Начало обработки заказа 42
# [LOG] Заказ 42 обработан

process_order(43, SilentLogger())
# Тишина — но код работает без ошибок
```

## Полиморфизм со встроенными функциями

Python активно использует полиморфизм через магические методы. Встроенные функции работают с разными типами, потому что каждый тип реализует нужный интерфейс:

```python
# len() работает со всем, у чего есть __len__
print(len("hello"))       # 5
print(len([1, 2, 3]))     # 3
print(len({"a": 1}))      # 1

# + работает со всем, у чего есть __add__
print(1 + 2)              # 3
print("ab" + "cd")        # abcd
print([1, 2] + [3, 4])    # [1, 2, 3, 4]

# Свой класс — свои правила
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)

    def __len__(self):
        return 2

    def __str__(self):
        return f"Vector({self.x}, {self.y})"

v1 = Vector(1, 2)
v2 = Vector(3, 4)
print(v1 + v2)   # Vector(4, 6)
print(len(v1))   # 2
```

Подробнее о магических методах — в следующем файле [[06_07_magic_methods|Магические методы]].

## 🎯 Пример с реальной задачей

Система отчётов с разными форматами вывода:

```python
class Report:
    def __init__(self, title, data):
        self.title = title
        self.data = data

    def render(self):
        raise NotImplementedError("Подкласс должен реализовать render()")


class TextReport(Report):
    def render(self):
        lines = [f"=== {self.title} ==="]
        for key, value in self.data.items():
            lines.append(f"{key}: {value}")
        return "\n".join(lines)


class CSVReport(Report):
    def render(self):
        lines = [",".join(self.data.keys())]
        lines.append(",".join(str(v) for v in self.data.values()))
        return "\n".join(lines)


class HTMLReport(Report):
    def render(self):
        rows = "".join(
            f"<tr><td>{k}</td><td>{v}</td></tr>"
            for k, v in self.data.items()
        )
        return f"<h1>{self.title}</h1><table>{rows}</table>"


data = {"Продажи": 1500, "Расходы": 800, "Прибыль": 700}

reports = [
    TextReport("Финансы", data),
    CSVReport("Финансы", data),
    HTMLReport("Финансы", data),
]

for report in reports:
    print(report.render())
    print()
```

Функция `print(report.render())` одна — но каждый класс рендерит по-своему.

## 🔎 Проверка на ходу

```python
>>> class Square:
...     def __init__(self, side):
...         self.side = side
...     def area(self):
...         return self.side ** 2
...
>>> class Circle:
...     def __init__(self, radius):
...         self.radius = radius
...     def area(self):
...         return 3.14 * self.radius ** 2
...
>>> shapes = [Square(4), Circle(3), Square(2)]
>>> [s.area() for s in shapes]
[16, 28.26, 4]
```

## Резюме

- **Полиморфизм** — разные объекты отвечают на один вызов по-своему
- Через **наследование**: дочерние классы переопределяют методы родителя
- Через **утиную типизацию**: общий интерфейс без общего родителя — достаточно иметь нужный метод
- Python использует полиморфизм повсюду: `len()`, `+`, `print()` работают с разными типами через магические методы
- Полиморфизм позволяет писать универсальный код, который работает с любыми объектами нужного «вида»

---

[[06_05_inheritance|← 🧬 Наследование]] | [[06_07_magic_methods|✨ Магические методы →]]
