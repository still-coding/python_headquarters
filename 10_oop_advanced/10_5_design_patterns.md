[[10_4_enums|← 🏷️ Перечисления (`Enum`)]] | [[00_table_of_contents|☰ Оглавление →]]

---
# 🎨 Паттерны проектирования
```toc
minLevel: 2
```

## Что такое паттерны проектирования

**Паттерны проектирования** — это типовые, проверенные решения для часто встречающихся проблем в объектно-ориентированном дизайне. Не библиотеки и не готовый код, а скорее «грамматические обороты» объектного мышления: способы организовать классы и их взаимодействие.

Классический источник — книга «Банды четырёх» (Gang of Four, 1994) с 23 паттернами. Большинство из них были придуманы для языков вроде C++ и Java, где у программиста меньше динамических средств.

### Python и паттерны: важное уточнение

Многие «классические» паттерны в Python либо **уже встроены в язык**, либо реализуются проще и короче, чем в оригинальных описаниях. Например:

- **Iterator** — встроен (`__iter__`, `__next__`, генераторы)
- **Decorator** — встроен (`@decorator`, см. [[06_11_closures_and_decorators]])
- **Strategy** — часто сводится к передаче функции как аргумента
- **Command** — функция первого класса решает задачу

Поэтому в Python мы не копируем решения из книг дословно — берём идею и записываем «на языке Python». В этом файле рассмотрим четыре паттерна, которые действительно полезны: **Singleton**, **Factory**, **Observer**, **Strategy**.

## Singleton — единственный экземпляр

**Идея:** гарантировать, что класс имеет ровно один экземпляр, и предоставить к нему глобальный доступ.

**Когда уместно:** конфигурация приложения, пул соединений, логгер, кэш — то, что по смыслу должно существовать в единственном числе.

**Когда НЕ уместно:** почти всегда. Singleton по сути — глобальное состояние, а глобальное состояние затрудняет тестирование, параллелизм и подмену в тестах. Прежде чем применять, спроси себя: может, достаточно модуля?

### Пример через `__new__`

```python
class Config:
    _instance = None

    def __new__(cls, *args, **kwargs):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance

    def __init__(self):
        # ⚠️ __init__ вызовется при каждом Config() — осторожно
        if not hasattr(self, "_initialized"):
            self.debug = False
            self.db_url = ""
            self._initialized = True


a = Config()
b = Config()
print(a is b)       # True — один и тот же объект
```

### Самый pythonic Singleton — модуль

В Python каждый импортированный модуль — уже синглтон: Python кэширует его в `sys.modules` и отдаёт один и тот же объект при повторных импортах (см. [[09_2_how_import_works]]).

```python
# config.py
debug = False
db_url = ""

def setup(**kwargs):
    globals().update(kwargs)
```

```python
# main.py
import config
config.setup(debug=True, db_url="postgres://...")

# where_ever.py
import config
print(config.debug)     # True — та же самая "сущность"
```

Никакой магии с `__new__`, не нужно бояться повторного `__init__`, не нужно думать про потокобезопасность первой инициализации — модуль уже синглтон по природе.

> 💡 В 90% случаев, когда тянет сделать Singleton, достаточно обычного модуля.

## Factory — фабрика объектов

**Идея:** вместо прямого создания объектов через `SomeClass()` использовать специальную функцию или метод, которая сама решает, какой класс создать и с какими параметрами.

**Когда уместно:** нужно создать подходящий объект в зависимости от входных данных; создание объекта само по себе нетривиально; класс выбирается динамически.

### Простая фабрика-функция

```python
class Circle:
    def __init__(self, radius): self.radius = radius
    def area(self): return 3.14 * self.radius ** 2


class Square:
    def __init__(self, side): self.side = side
    def area(self): return self.side ** 2


class Triangle:
    def __init__(self, base, height): self.base = base; self.height = height
    def area(self): return 0.5 * self.base * self.height


def create_shape(kind: str, **params):
    shapes = {
        "circle": Circle,
        "square": Square,
        "triangle": Triangle,
    }
    if kind not in shapes:
        raise ValueError(f"Неизвестная фигура: {kind}")
    return shapes[kind](**params)


shape = create_shape("circle", radius=5)
print(shape.area())      # 78.5
```

Код-клиент не знает и не должен знать про конкретные классы — достаточно интерфейса `create_shape(kind, **params)`.

### Фабричный метод класса

Другая форма — когда сам класс предоставляет альтернативные конструкторы через [[07_08_class_and_static_methods|`@classmethod`]]:

```python
from datetime import datetime


class Event:
    def __init__(self, title, timestamp):
        self.title = title
        self.timestamp = timestamp

    @classmethod
    def from_dict(cls, data: dict):
        return cls(
            title=data["title"],
            timestamp=datetime.fromisoformat(data["timestamp"]),
        )

    @classmethod
    def now(cls, title):
        return cls(title, datetime.now())


event_a = Event.from_dict({"title": "Запуск", "timestamp": "2026-04-01T10:00:00"})
event_b = Event.now("Здесь и сейчас")
```

Это один из самых pythonic способов применения паттерна Factory — вместо отдельного класса-фабрики добавляем фабричные методы в сам класс.

## Observer — наблюдатель

**Идея:** один объект (subject) уведомляет других (observers) об изменениях своего состояния, не зная о них конкретно — только через общий интерфейс подписки.

**Когда уместно:** система событий, реактивные интерфейсы, уведомления, логирование действий.

```python
class EventBus:
    def __init__(self):
        self._subscribers: dict[str, list] = {}

    def subscribe(self, event: str, handler):
        self._subscribers.setdefault(event, []).append(handler)

    def publish(self, event: str, *args, **kwargs):
        for handler in self._subscribers.get(event, []):
            handler(*args, **kwargs)


# Использование
bus = EventBus()


def on_user_registered(user):
    print(f"[email] Отправляем приветствие для {user}")


def on_user_registered_analytics(user):
    print(f"[analytics] Фиксируем регистрацию: {user}")


bus.subscribe("user.registered", on_user_registered)
bus.subscribe("user.registered", on_user_registered_analytics)

bus.publish("user.registered", "Иван")
# [email] Отправляем приветствие для Иван
# [analytics] Фиксируем регистрацию: Иван
```

Подписчики не знают друг о друге. Publisher не знает, кто подписан. Новый подписчик добавляется одной строкой, существующие при этом не ломаются.

В Python подписчиком может быть **любой вызываемый объект** — функция, метод, лямбда, экземпляр класса с `__call__`. Городить специальный класс `Observer` с методом `update()`, как в книге GoF, не нужно.

## Strategy — стратегия

**Идея:** алгоритм можно выбрать в рантайме, подставив разные реализации с одним интерфейсом.

**Когда уместно:** есть несколько способов сделать одно и то же, и выбор зависит от контекста.

### Канонический вариант с классами

```python
class DiscountStrategy:
    def apply(self, price: float) -> float: ...


class NoDiscount(DiscountStrategy):
    def apply(self, price): return price


class PercentDiscount(DiscountStrategy):
    def __init__(self, percent): self.percent = percent
    def apply(self, price): return price * (1 - self.percent / 100)


class FixedDiscount(DiscountStrategy):
    def __init__(self, amount): self.amount = amount
    def apply(self, price): return max(0, price - self.amount)


class Order:
    def __init__(self, price, discount: DiscountStrategy = NoDiscount()):
        self.price = price
        self.discount = discount

    def final_price(self) -> float:
        return self.discount.apply(self.price)


order = Order(1000, PercentDiscount(15))
print(order.final_price())      # 850.0
```

### Pythonic-вариант: функция как стратегия

В Python функции — полноценные объекты первого класса, поэтому отдельные классы часто избыточны:

```python
def no_discount(price): return price
def percent_discount(percent): return lambda price: price * (1 - percent / 100)
def fixed_discount(amount): return lambda price: max(0, price - amount)


class Order:
    def __init__(self, price, discount=no_discount):
        self.price = price
        self.discount = discount

    def final_price(self):
        return self.discount(self.price)


order = Order(1000, percent_discount(15))
print(order.final_price())      # 850.0
```

Короче, гибче, проще в тестировании. Классы нужны только если стратегия имеет собственное состояние, которое меняется между вызовами.

## Как выбирать паттерн

Несколько общих соображений, которые важнее знания всех 23 паттернов наизусть:

- 🟢 **Сначала простое решение.** Паттерн — это инвестиция в сложность. Если двух функций достаточно, не надо делать Factory.
- 🟢 **Язык решает половину.** В Python генераторы заменяют Iterator, декораторы — Decorator, модули — Singleton, функции — Strategy и Command. Прежде чем применять паттерн из книги, проверь, нет ли встроенного инструмента.
- 🟢 **Паттерны — словарь.** Главная ценность — общий язык для обсуждения дизайна. «Давай это будет фабрика» быстрее, чем рисовать всё на доске.
- 🔴 **Не ищи, куда бы применить паттерн.** Паттерн появляется, когда задача к нему приводит, а не наоборот.

Уильям Цинссер о писательском ремесле говорил: «Главная проблема начинающего — избыток слов». С паттернами то же самое: начинающий хочет применить все сразу. Опытный — убрать всё, что можно убрать.

## 💡 Главное

```python
# Singleton — единственный экземпляр
#   В Python — чаще всего просто модуль.
#   Через __new__ — если очень нужно поведение на уровне класса.

# Factory — выбор класса/конструктора в рантайме
def create_shape(kind, **params):
    return {"circle": Circle, "square": Square}[kind](**params)

#   Часто — @classmethod как альтернативный конструктор:
class Event:
    @classmethod
    def from_dict(cls, data): ...


# Observer — подписка/уведомление без прямых связей
bus.subscribe("event", handler)
bus.publish("event", data)
#   Подписчик = любой callable. Никаких специальных классов.


# Strategy — алгоритм подставляется в рантайме
class Order:
    def __init__(self, discount=no_discount): self.discount = discount
#   Часто — просто функция, а не класс.


# Общий принцип:
# Python предоставляет много паттернов бесплатно — через язык.
# Паттерн применяется, когда задача к нему приводит,
# а не для того, чтобы применить паттерн.
```

---

[[10_4_enums|← 🏷️ Перечисления (`Enum`)]] | [[00_table_of_contents|☰ Оглавление →]]
