[[07_11_mro|← 🔀 MRO]]

---
# ♻️ Жизненный цикл объекта
```toc
minLevel: 2
```

## Три этапа жизни объекта

Каждый объект в Python проходит три этапа:

```
Создание → Использование → Удаление
__new__      (обычная        __del__
__init__      работа)
```

Мы уже хорошо знаем `__init__`. Теперь разберём остальные.

## `__new__` — конструктор

`__new__` вызывается **до** `__init__`. Его задача — создать и вернуть новый объект. `__init__` лишь заполняет уже созданный объект данными.

```python
class A:
    def __new__(cls):
        print("1. __new__: объект создаётся")
        instance = super().__new__(cls)
        return instance          # Обязательно вернуть объект!

    def __init__(self):
        print("2. __init__: объект инициализируется")


a = A()
# 1. __new__: объект создаётся
# 2. __init__: объект инициализируется
```

**Ключевые отличия:**

| | `__new__` | `__init__` |
|---|---|---|
| Когда вызывается | До создания объекта | После создания |
| Первый параметр | `cls` — класс | `self` — объект |
| Должен вернуть | Новый объект | Ничего (`None`) |
| Использование | Редко | Всегда |

В большинстве случаев `__new__` писать не нужно — Python делает всё сам. Его переопределяют только в особых ситуациях.

## `__new__` на практике: неизменяемые типы

Главный реальный случай `__new__` — наследование от неизменяемых типов (`int`, `str`, `tuple`). У них нельзя изменить значение в `__init__`, потому что оно задаётся при создании:

```python
class PositiveInt(int):
    """Целое число, которое всегда положительное"""

    def __new__(cls, value):
        if value <= 0:
            raise ValueError(f"Значение должно быть положительным, получено: {value}")
        return super().__new__(cls, value)


n = PositiveInt(5)
print(n)        # 5
print(n + 3)    # 8
print(type(n))  # <class '__main__.PositiveInt'>

PositiveInt(-1)  # ValueError: Значение должно быть положительным
```

```python
class UpperStr(str):
    """Строка, которая всегда в верхнем регистре"""

    def __new__(cls, value):
        return super().__new__(cls, value.upper())


s = UpperStr("hello")
print(s)         # HELLO
print(s + "!")   # HELLO!
```

## Синглтон через `__new__`

Классический паттерн — гарантировать, что класс имеет только один экземпляр:

```python
class Singleton:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance   # Всегда возвращаем один и тот же объект

    def __init__(self):
        self.value = 0


a = Singleton()
b = Singleton()

print(a is b)   # True — это один и тот же объект
a.value = 42
print(b.value)  # 42 — изменение видно через b, потому что это тот же объект
```

## `__del__` — деструктор

`__del__` вызывается когда объект уничтожается — сборщиком мусора или при явном `del`:

```python
class Resource:
    def __init__(self, name):
        self.name = name
        print(f"Ресурс '{self.name}' открыт")

    def __del__(self):
        print(f"Ресурс '{self.name}' закрыт")


r = Resource("файл")   # Ресурс 'файл' открыт
del r                  # Ресурс 'файл' закрыт
```

### Когда вызывается `__del__`

```python
class Tracked:
    def __init__(self, name):
        self.name = name

    def __del__(self):
        print(f"{self.name} удалён")


a = Tracked("A")
b = a              # b ссылается на тот же объект

del a              # Объект НЕ удаляется — b всё ещё ссылается на него
print("после del a")

del b              # Теперь ссылок нет — объект удаляется
print("после del b")

# после del a
# после del b
# A удалён        ← удалился только сейчас
```

Python использует подсчёт ссылок: объект живёт пока на него ссылается хотя бы одна переменная.

### Осторожно с `__del__`

`__del__` ненадёжен — не гарантируется когда именно он будет вызван:

```python
# ❌ Плохо — полагаться на __del__ для освобождения ресурсов
class FileWrapper:
    def __init__(self, path):
        self.file = open(path)

    def __del__(self):
        self.file.close()  # Может вызваться слишком поздно!
```

Для гарантированного освобождения ресурсов используйте контекстный менеджер (`with`):

```python
# ✅ Хорошо — with гарантирует закрытие
with open("file.txt") as f:
    data = f.read()
# Файл закрыт здесь, вне зависимости от ошибок
```

## Полная картина: порядок вызовов

```python
class MyClass:
    def __new__(cls, value):
        print(f"  __new__(cls={cls.__name__}, value={value})")
        instance = super().__new__(cls)
        return instance

    def __init__(self, value):
        print(f"  __init__(self, value={value})")
        self.value = value

    def __del__(self):
        print(f"  __del__(self.value={self.value})")


print("Создаём объект:")
obj = MyClass(42)

print("Используем объект:")
print(f"  obj.value = {obj.value}")

print("Удаляем объект:")
del obj

# Создаём объект:
#   __new__(cls=MyClass, value=42)
#   __init__(self, value=42)
# Используем объект:
#   obj.value = 42
# Удаляем объект:
#   __del__(self.value=42)
```

## 🔎 Проверка на ходу

```python
>>> class MySingleton:
...     _instance = None
...     def __new__(cls):
...         if cls._instance is None:
...             cls._instance = super().__new__(cls)
...         return cls._instance
...
>>> a = MySingleton()
>>> b = MySingleton()
>>> a is b
True
>>> id(a) == id(b)
True
```

## Резюме

- `__new__` — конструктор, создаёт объект, получает `cls`, должен вернуть новый экземпляр
- `__init__` — инициализатор, заполняет созданный объект данными, получает `self`
- `__new__` вызывается **до** `__init__`; в большинстве случаев его не нужно переопределять
- Главные случаи для `__new__`: наследование от неизменяемых типов (`int`, `str`), паттерн Синглтон
- `__del__` — деструктор, вызывается при удалении объекта; ненадёжен для освобождения ресурсов
- Объект живёт пока на него есть хотя бы одна ссылка (подсчёт ссылок)
- Для гарантированного освобождения ресурсов используйте `with`, а не `__del__`

---

[[07_11_mro|← 🔀 MRO]]
