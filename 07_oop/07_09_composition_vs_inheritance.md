[[07_08_class_and_static_methods|← ⚙️ `@classmethod` и `@staticmethod`]]

---
# 🔗 Композиция vs наследование
```toc
minLevel: 2
```

## Два способа строить из частей

Когда один класс хочет использовать возможности другого, есть два пути:

- **Наследование** — «является» (is-a): `Dog` является `Animal`
- **Композиция** — «содержит» (has-a): `Car` содержит `Engine`

Выбор между ними — один из ключевых навыков проектирования.

## Наследование: «является»

Используйте наследование, когда дочерний класс **действительно является** частным случаем родителя:

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def eat(self):
        return f"{self.name} ест"


class Dog(Animal):       # Dog ЯВЛЯЕТСЯ Animal — наследование уместно
    def bark(self):
        return f"{self.name}: Гав!"


class Cat(Animal):       # Cat ЯВЛЯЕТСЯ Animal — наследование уместно
    def meow(self):
        return f"{self.name}: Мяу!"
```

Проверка: можно ли честно сказать «Dog — это Animal»? Да. Значит, наследование оправдано.

## Композиция: «содержит»

Используйте композицию, когда один класс **использует** другой, но не является его частным случаем:

```python
class Engine:
    def __init__(self, power):
        self.power = power

    def start(self):
        return f"Двигатель {self.power} л.с. запущен"

    def stop(self):
        return "Двигатель остановлен"


class Car:                        # Car СОДЕРЖИТ Engine — не "является двигателем"
    def __init__(self, brand, engine):
        self.brand = brand
        self.engine = engine      # Композиция — объект внутри объекта

    def start(self):
        return f"{self.brand}: {self.engine.start()}"


engine = Engine(150)
car = Car("Toyota", engine)
print(car.start())  # Toyota: Двигатель 150 л.с. запущен
```

## Проблемы наследования

Наследование кажется удобным, но легко создаёт проблемы.

### Хрупкая иерархия

```python
# ❌ Плохо: натянутая иерархия
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height


class Square(Rectangle):    # Квадрат — частный случай прямоугольника?
    def __init__(self, side):
        super().__init__(side, side)


sq = Square(5)
sq.width = 10   # Квадрат сломан — стороны теперь разные!
print(sq.area())  # 50 — это уже не квадрат
```

Квадрат математически является прямоугольником, но в программировании это создаёт проблемы. Лучше использовать композицию или общий родитель `Shape`.

### Глубокая иерархия

```python
# ❌ Плохо: слишком глубокая цепочка
class A:
    pass

class B(A):
    pass

class C(B):
    pass

class D(C):      # Чтобы понять D — нужно прочитать A, B, C, D
    pass
```

Глубже 2-3 уровней — сигнал пересмотреть архитектуру.

## Композиция решает эти проблемы

```python
# ✅ Лучше: композиция вместо наследования
class Shape:
    def area(self):
        raise NotImplementedError


class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height


class Square(Shape):        # Отдельный класс, не наследует Rectangle
    def __init__(self, side):
        self.side = side

    def area(self):
        return self.side ** 2


sq = Square(5)
print(sq.area())  # 25 — всегда корректно
```

## Практический пример: уведомления

Покажем, как один и тот же код лучше выглядит через композицию:

```python
# ❌ Через наследование — жёсткая привязка
class EmailNotifier:
    def send(self, message):
        print(f"[Email] {message}")

class SMSNotifier:
    def send(self, message):
        print(f"[SMS] {message}")

class OrderServiceWithEmail(EmailNotifier):    # Привязались к Email
    def process(self, order_id):
        self.send(f"Заказ {order_id} обработан")

# Хотим SMS — придётся создавать новый класс OrderServiceWithSMS
```

```python
# ✅ Через композицию — гибко
class EmailNotifier:
    def send(self, message):
        print(f"[Email] {message}")

class SMSNotifier:
    def send(self, message):
        print(f"[SMS] {message}")

class PushNotifier:
    def send(self, message):
        print(f"[Push] {message}")


class OrderService:
    def __init__(self, notifier):       # Принимаем любой notifier
        self.notifier = notifier        # Композиция

    def process(self, order_id):
        self.notifier.send(f"Заказ {order_id} обработан")


# Меняем поведение без изменения OrderService
OrderService(EmailNotifier()).process(1)   # [Email] Заказ 1 обработан
OrderService(SMSNotifier()).process(2)     # [SMS] Заказ 2 обработан
OrderService(PushNotifier()).process(3)    # [Push] Заказ 3 обработан
```

Это называется **внедрение зависимостей** (dependency injection) — один из главных принципов гибкого кода.

## Совмещение наследования и композиции

На практике оба подхода часто используются вместе:

```python
class Address:
    def __init__(self, city, street):
        self.city = city
        self.street = street

    def __str__(self):
        return f"{self.city}, {self.street}"


class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __str__(self):
        return f"{self.name}, {self.age} лет"


class Employee(Person):                  # Наследование: Employee ЯВЛЯЕТСЯ Person
    def __init__(self, name, age, position, address):
        super().__init__(name, age)
        self.position = position
        self.address = address           # Композиция: Employee СОДЕРЖИТ Address

    def __str__(self):
        return f"{super().__str__()}, {self.position}, {self.address}"


emp = Employee("Анна", 30, "Разработчик", Address("Москва", "Льва Толстого 16"))
print(emp)  # Анна, 30 лет, Разработчик, Москва, Льва Толстого 16
```

## Правило выбора

Задайте себе вопрос о связи между классами:

```
«Б является А?»  →  Наследование
«Б содержит А?»  →  Композиция
```

| Ситуация | Подход |
|---|---|
| `Dog` и `Animal` | Наследование — собака является животным |
| `Car` и `Engine` | Композиция — машина содержит двигатель |
| `Employee` и `Person` | Наследование — сотрудник является человеком |
| `User` и `Address` | Композиция — пользователь содержит адрес |
| `Admin` и `User` | Наследование — администратор является пользователем |
| `OrderService` и `Notifier` | Композиция — сервис использует уведомлятель |

Если сомневаетесь — выбирайте композицию. Её легче изменить.

## 🔎 Проверка на ходу

```python
>>> class Battery:
...     def __init__(self, capacity):
...         self.capacity = capacity
...     def charge_level(self):
...         return f"{self.capacity}%"
...
>>> class Phone:
...     def __init__(self, model, battery):
...         self.model = model
...         self.battery = battery     # Композиция
...     def status(self):
...         return f"{self.model}: заряд {self.battery.charge_level()}"
...
>>> phone = Phone("iPhone", Battery(85))
>>> phone.status()
'iPhone: заряд 85%'
```

## Резюме

- **Наследование** — «является» (is-a): используйте когда дочерний класс — частный случай родителя
- **Композиция** — «содержит» (has-a): используйте когда класс использует другой класс как часть
- Наследование создаёт жёсткую связь — изменение родителя влияет на всех потомков
- Композиция даёт гибкость — легко заменить одну часть на другую
- Глубокая иерархия наследования (3+ уровней) — сигнал пересмотреть архитектуру
- Если сомневаетесь — предпочитайте композицию

---

[[07_08_class_and_static_methods|← ⚙️ `@classmethod` и `@staticmethod`]]
