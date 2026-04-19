[[10_0_intro|← ⚙️ Введение в продвинутое ООП]] | [[10_2_mixins|🧩 Миксины →]]

---
# 🔬 Дескрипторы
```toc
minLevel: 2
```

## Что такое дескриптор

**Дескриптор** — это объект, который управляет доступом к атрибуту другого объекта. Когда ты пишешь `obj.attr`, Python не всегда просто достаёт значение из словаря — если `attr` является дескриптором, Python вызывает его специальные методы.

Проще говоря: дескриптор — это способ сказать «когда кто-то обращается к этому атрибуту — сначала спроси меня».

Дескрипторы — **фундаментальный механизм Python**. На них построены:

- `@property`
- `@classmethod` и `@staticmethod`
- методы классов вообще
- многие вещи в Django ORM, SQLAlchemy, Pydantic

То есть ты уже пользовался дескрипторами — просто неявно.

## Протокол дескриптора

Объект становится дескриптором, если определяет хотя бы один из трёх методов:

```python
class Descriptor:
    def __get__(self, obj, objtype=None):
        # Вызывается при чтении: obj.attr
        ...

    def __set__(self, obj, value):
        # Вызывается при записи: obj.attr = value
        ...

    def __delete__(self, obj):
        # Вызывается при удалении: del obj.attr
        ...
```

> ⚠️ Важное условие: дескриптор должен быть **атрибутом класса**, а не экземпляра. Если положить дескриптор в экземпляр — его методы не сработают.

### Простейший пример

```python
class Logged:
    """Дескриптор, который логирует обращения."""

    def __get__(self, obj, objtype=None):
        print(f"Чтение атрибута")
        return "значение"

    def __set__(self, obj, value):
        print(f"Запись значения: {value}")


class MyClass:
    attr = Logged()       # ← дескриптор как атрибут КЛАССА


instance = MyClass()
instance.attr             # Чтение атрибута
instance.attr = 42        # Запись значения: 42
```

Заметь: `attr` объявлен один раз на уровне класса, но работает для любого экземпляра — `self.attr` каждого объекта проходит через один и тот же дескриптор.

## `__set_name__` — как узнать своё имя

Часто дескриптору нужно знать, под каким именем он объявлен в классе. Python автоматически вызывает специальный метод `__set_name__`, когда класс создаётся:

```python
class Named:
    def __set_name__(self, owner, name):
        # owner — класс, в котором объявлен дескриптор
        # name — имя, под которым его объявили
        print(f"Дескриптор объявлен как {owner.__name__}.{name}")
        self.name = name


class User:
    login = Named()
    email = Named()

# При создании класса User автоматически выведется:
# Дескриптор объявлен как User.login
# Дескриптор объявлен как User.email
```

Это избавляет от необходимости передавать имя руками: `name = Named("name")`.

## Практика: валидатор через дескриптор

Классический пример пользы дескрипторов — валидация данных.

```python
class PositiveNumber:
    """Дескриптор: разрешает только положительные числа."""

    def __set_name__(self, owner, name):
        self.private_name = f"_{name}"

    def __get__(self, obj, objtype=None):
        if obj is None:
            return self
        return getattr(obj, self.private_name)

    def __set__(self, obj, value):
        if not isinstance(value, (int, float)):
            raise TypeError(f"Ожидается число, получено {type(value).__name__}")
        if value <= 0:
            raise ValueError(f"Значение должно быть положительным, получено {value}")
        setattr(obj, self.private_name, value)


class Product:
    price = PositiveNumber()
    quantity = PositiveNumber()

    def __init__(self, name, price, quantity):
        self.name = name
        self.price = price          # пройдёт через __set__
        self.quantity = quantity    # пройдёт через __set__


milk = Product("Молоко", 89.9, 10)
print(milk.price)             # 89.9

milk.price = -10              # ValueError: Значение должно быть положительным
milk.quantity = "много"       # TypeError: Ожидается число, получено str
```

Прелесть в том, что **один дескриптор** `PositiveNumber` переиспользуется для любого поля, которому нужна такая валидация. Без дескрипторов пришлось бы писать `@property` для каждого поля по отдельности — и копипастить логику.

### Почему `obj is None`?

```python
def __get__(self, obj, objtype=None):
    if obj is None:
        return self
    return getattr(obj, self.private_name)
```

Если обратиться к дескриптору через класс (а не экземпляр), `obj` будет `None`:

```python
Product.price       # obj = None → возвращаем сам дескриптор
milk.price          # obj = milk → возвращаем значение
```

Это стандартное соглашение, оно позволяет обращаться к дескриптору для интроспекции.

## `@property` под капотом

`@property` — это **готовый дескриптор**. Когда ты пишешь:

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def area(self):
        return 3.14 * self._radius ** 2
```

— `property` создаёт объект-дескриптор, у которого `__get__` вызывает декорированную функцию. Эквивалент «вручную»:

```python
class ReadOnlyProperty:
    def __init__(self, fget):
        self.fget = fget

    def __get__(self, obj, objtype=None):
        if obj is None:
            return self
        return self.fget(obj)


class Circle:
    def __init__(self, radius):
        self._radius = radius

    @ReadOnlyProperty
    def area(self):
        return 3.14 * self._radius ** 2
```

Полный `property` ещё поддерживает `__set__` и `__delete__` для сеттеров и удаления — принцип тот же.

> 💡 Отсюда практический вывод: если один и тот же паттерн `property` повторяется в разных классах — заменяй его на дескриптор, будет чище и короче.

## Дескрипторы данных и не-данных

Дескрипторы делятся на две категории — это важно для понимания приоритетов поиска атрибутов.

- **Дескриптор данных** (data descriptor) — есть `__set__` или `__delete__` (или оба)
- **Дескриптор не-данных** (non-data descriptor) — есть только `__get__`

Разница — в том, что **сильнее** при конфликте с `__dict__` экземпляра:

```python
# Порядок поиска атрибута obj.attr:
# 1. Дескрипторы данных в классе         (имеют приоритет над __dict__)
# 2. __dict__ экземпляра
# 3. Дескрипторы не-данных в классе      (словарь имеет приоритет)
# 4. __getattr__
```

Практическое следствие:

```python
class DataDesc:
    def __get__(self, obj, objtype=None):
        return "из дескриптора"
    def __set__(self, obj, value):
        obj.__dict__["x"] = value


class NonDataDesc:
    def __get__(self, obj, objtype=None):
        return "из дескриптора"


class A:
    x = DataDesc()


class B:
    x = NonDataDesc()


a = A()
a.__dict__["x"] = "из словаря"
print(a.x)          # "из дескриптора" — дескриптор данных побеждает

b = B()
b.__dict__["x"] = "из словаря"
print(b.x)          # "из словаря" — словарь побеждает не-данные
```

Это не академическая деталь: обычные методы классов — это дескрипторы **не-данных**, и именно поэтому их можно «переопределить» на уровне экземпляра, подставив функцию в `instance.__dict__`.

## Когда использовать дескрипторы

**Используй дескрипторы, если:**

- 🟢 Одна и та же логика доступа повторяется для нескольких атрибутов (валидация, трансформация, логирование)
- 🟢 Пишешь библиотеку/фреймворк, где пользователи будут объявлять поля декларативно (как в Django `CharField()`, SQLAlchemy `Column()`, Pydantic)
- 🟢 Нужен контроль над атрибутом на уровне всего класса

**Не используй дескрипторы, если:**

- 🔴 Достаточно одного-двух `@property` — не плоди сложность
- 🔴 Логика специфична для конкретного атрибута в конкретном классе — пиши `@property`
- 🔴 Команда не понимает дескрипторы — ценность читаемости выше ценности «красивой абстракции»

## 💡 Главное

```python
# Дескриптор = объект с __get__ / __set__ / __delete__
# как АТРИБУТ КЛАССА (не экземпляра)

class Descriptor:
    def __set_name__(self, owner, name):
        self.name = f"_{name}"

    def __get__(self, obj, objtype=None):
        if obj is None:
            return self
        return getattr(obj, self.name)

    def __set__(self, obj, value):
        # логика проверки/трансформации
        setattr(obj, self.name, value)

# Применение:
class MyClass:
    field = Descriptor()    # один раз — работает для всех экземпляров

# @property — это готовый дескриптор
# Методы классов — тоже дескрипторы (не-данных)

# Дескриптор данных (__set__) имеет приоритет над __dict__ экземпляра
# Дескриптор не-данных (только __get__) — нет

# Используй, когда логика повторяется и её нужно вынести в один объект
# Для одного-двух полей хватит @property
```

---

[[10_0_intro|← ⚙️ Введение в продвинутое ООП]] | [[10_2_mixins|🧩 Миксины →]]
