[[07_09_composition_vs_inheritance|← 🔗 Композиция vs наследование]] | [[07_11_mro|🔀 MRO →]]

---
# 🔷 ABC и Protocol
```toc
minLevel: 2
```

## Зачем нужны формальные интерфейсы?

Мы уже знаем утиную типизацию: если у объекта есть нужный метод — он подойдёт. Но иногда этого недостаточно.

```python
class TextReport:
    def render(self):
        return "текст"

class CSVReport:
    def export(self):      # Ой — не render(), а export()
        return "csv"


def print_report(report):
    print(report.render())   # Упадёт на CSVReport — но только в рантайме


print_report(TextReport())  # ок
print_report(CSVReport())   # AttributeError — узнаём только при запуске
```

Проблема: ошибка обнаруживается только во время выполнения. Хочется поймать её раньше — при создании класса.

Два инструмента для этого: **ABC** и **Protocol**.

## ABC — абстрактные базовые классы

`ABC` (Abstract Base Class) — класс, который нельзя инстанциировать. Он задаёт обязательный интерфейс для всех наследников.

```python
from abc import ABC, abstractmethod


class Report(ABC):
    """Абстрактный базовый класс для отчётов"""

    @abstractmethod
    def render(self):
        """Все наследники ОБЯЗАНЫ реализовать этот метод"""
        ...


# Попытка создать экземпляр ABC — ошибка сразу
r = Report()  # TypeError: Can't instantiate abstract class Report
```

Если наследник не реализует все абстрактные методы — тоже ошибка:

```python
class BrokenReport(Report):
    pass   # Забыли реализовать render()

b = BrokenReport()  # TypeError: Can't instantiate abstract class BrokenReport
                    # — ошибка при создании объекта, не при вызове метода
```

### Полный пример

```python
from abc import ABC, abstractmethod


class Shape(ABC):

    @abstractmethod
    def area(self) -> float:
        ...

    @abstractmethod
    def perimeter(self) -> float:
        ...

    def describe(self):
        """Конкретный метод — доступен всем наследникам"""
        return f"Площадь: {self.area():.2f}, периметр: {self.perimeter():.2f}"


class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return 3.14159 * self.radius ** 2

    def perimeter(self):
        return 2 * 3.14159 * self.radius


class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height

    def perimeter(self):
        return 2 * (self.width + self.height)


shapes = [Circle(5), Rectangle(4, 6)]
for s in shapes:
    print(s.describe())
# Площадь: 78.54, периметр: 31.42
# Площадь: 24.00, периметр: 20.00
```

### Абстрактные свойства

`@abstractmethod` можно комбинировать с `@property`:

```python
from abc import ABC, abstractmethod


class Animal(ABC):

    @property
    @abstractmethod
    def sound(self) -> str:
        """Каждый наследник должен определить звук"""
        ...

    def speak(self):
        return f"{self.__class__.__name__} говорит: {self.sound}"


class Cat(Animal):
    @property
    def sound(self):
        return "Мяу!"

class Dog(Animal):
    @property
    def sound(self):
        return "Гав!"


cat = Cat()
dog = Dog()
print(cat.speak())  # Cat говорит: Мяу!
print(dog.speak())  # Dog говорит: Гав!
```

### ABC с общей реализацией

ABC может содержать не только абстрактные методы, но и готовую реализацию:

```python
from abc import ABC, abstractmethod


class Logger(ABC):

    @abstractmethod
    def _write(self, message: str):
        """Низкоуровневая запись — реализует наследник"""
        ...

    def log(self, message: str):
        """Общий формат — реализован в ABC"""
        formatted = f"[LOG] {message}"
        self._write(formatted)

    def error(self, message: str):
        formatted = f"[ERROR] {message}"
        self._write(formatted)


class ConsoleLogger(Logger):
    def _write(self, message):
        print(message)

class FileLogger(Logger):
    def __init__(self, filename):
        self.filename = filename

    def _write(self, message):
        with open(self.filename, "a") as f:
            f.write(message + "\n")


logger = ConsoleLogger()
logger.log("Сервер запущен")    # [LOG] Сервер запущен
logger.error("Соединение упало") # [ERROR] Соединение упало
```

Наследник реализует только `_write` — а `log()` и `error()` достаются бесплатно.

## Protocol — структурная типизация

`Protocol` из модуля `typing` — формализованная утиная типизация. Не требует наследования: достаточно, чтобы у класса были нужные методы.

```python
from typing import Protocol


class Renderable(Protocol):
    def render(self) -> str:
        ...


class TextReport:
    def render(self) -> str:
        return "=== Текстовый отчёт ==="

class CSVReport:
    def render(self) -> str:
        return "col1,col2,col3"

class HTMLReport:
    def render(self) -> str:
        return "<html>...</html>"


def print_report(report: Renderable) -> None:
    print(report.render())


# Все три класса удовлетворяют Protocol — без наследования!
print_report(TextReport())
print_report(CSVReport())
print_report(HTMLReport())
```

Никакого `class TextReport(Renderable)` — просто наличие метода `render()` делает класс совместимым.

### Protocol с несколькими методами

```python
from typing import Protocol


class Drawable(Protocol):
    def draw(self) -> None:
        ...

    def resize(self, factor: float) -> None:
        ...

    @property
    def visible(self) -> bool:
        ...


class Button:
    def __init__(self, label):
        self.label = label
        self._visible = True

    def draw(self):
        print(f"[Button: {self.label}]")

    def resize(self, factor):
        print(f"Button resized x{factor}")

    @property
    def visible(self):
        return self._visible


class Icon:
    def __init__(self, path):
        self.path = path
        self._visible = True

    def draw(self):
        print(f"[Icon: {self.path}]")

    def resize(self, factor):
        print(f"Icon resized x{factor}")

    @property
    def visible(self):
        return self._visible


def render_ui(elements: list[Drawable]):
    for el in elements:
        if el.visible:
            el.draw()


render_ui([Button("OK"), Icon("logo.png")])
# [Button: OK]
# [Icon: logo.png]
```

## ABC vs Protocol

```python
# ABC — номинальная типизация
# Нужно явно унаследоваться

class Flyable(ABC):
    @abstractmethod
    def fly(self): ...

class Bird(Flyable):      # Явно объявляем: Bird — это Flyable
    def fly(self):
        return "машу крыльями"

class Plane:              # Нет наследования — не Flyable,
    def fly(self):        # даже если метод есть
        return "работают двигатели"


# Protocol — структурная типизация
# Достаточно иметь нужные методы

class Flyable(Protocol):
    def fly(self) -> str: ...

class Bird:               # Нет наследования — но метод fly() есть
    def fly(self):
        return "машу крыльями"

class Plane:              # Тоже нет наследования — но тоже подходит
    def fly(self):
        return "работают двигатели"
```

| | ABC | Protocol |
|---|---|---|
| Нужно наследоваться | ✅ Да | ❌ Нет |
| Ошибка при отсутствии метода | При создании объекта | При статической проверке (mypy) |
| Можно добавить общую реализацию | ✅ Да | ❌ Нет |
| Работает со сторонними классами | ❌ Нет | ✅ Да |
| Явная иерархия | ✅ Да | ❌ Нет |

## Когда что выбрать?

**Используйте ABC когда:**
- Есть общая реализация, которую хотите передать наследникам
- Нужна явная иерархия классов
- Все классы — ваши, вы контролируете их код

**Используйте Protocol когда:**
- Нужна утиная типизация с явным описанием интерфейса
- Работаете со сторонними классами, которые нельзя заставить наследоваться
- Хотите описать «что умеет делать», не диктуя иерархию

```python
# ABC — для иерархии с общей логикой
class BaseHandler(ABC):
    @abstractmethod
    def handle(self, request): ...

    def log(self, msg):       # Общая логика — для всех бесплатно
        print(f"[Handler] {msg}")

# Protocol — для описания интерфейса без иерархии
class Serializable(Protocol):
    def to_json(self) -> str: ...
    def to_dict(self) -> dict: ...
```

## 🔎 Проверка на ходу

```python
>>> from abc import ABC, abstractmethod
>>> class Vehicle(ABC):
...     @abstractmethod
...     def fuel_type(self) -> str: ...
...
>>> Vehicle()
TypeError: Can't instantiate abstract class Vehicle
>>> class Car(Vehicle):
...     def fuel_type(self):
...         return "бензин"
...
>>> Car().fuel_type()
'бензин'
>>> isinstance(Car(), Vehicle)
True
```

## Резюме

- **ABC** — нельзя создать экземпляр, наследники обязаны реализовать все `@abstractmethod`
- **`@abstractmethod`** можно комбинировать с `@property`
- ABC может содержать готовую реализацию — наследники получают её бесплатно
- **Protocol** — формализованная утиная типизация: не нужно наследоваться, достаточно иметь нужные методы
- ABC = номинальная типизация (явная иерархия), Protocol = структурная типизация (по наличию методов)
- ABC — когда контролируете иерархию и хотите общую логику; Protocol — когда описываете интерфейс без иерархии

---

[[07_09_composition_vs_inheritance|← 🔗 Композиция vs наследование]] | [[07_11_mro|🔀 MRO →]]
