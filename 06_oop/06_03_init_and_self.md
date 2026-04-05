[[06_02_attributes_and_methods|← 📦 Атрибуты и методы]] | [[06_04_encapsulation|🔒 Инкапсуляция →]]

---
# 🔧 `__init__` и `self`
```toc
minLevel: 2
```

## Что такое `__init__`?

`__init__` — это специальный метод, который Python вызывает **автоматически** в момент создания объекта. Его задача — инициализировать объект: задать начальные значения атрибутов.

```python
class Cat:
    def __init__(self, name, age):
        self.name = name
        self.age = age

# Python автоматически вызывает __init__ при создании объекта
cat = Cat("Мурка", 3)
#         ↑       ↑
#     name=...  age=...  — эти значения уходят в __init__

print(cat.name)  # Мурка
print(cat.age)   # 3
```

Название `__init__` происходит от слова *initialize* (инициализировать). Двойные подчёркивания с обеих сторон (`__`) означают, что это **магический метод** — Python вызывает его в особых ситуациях автоматически.

## Что такое `self`?

`self` — это ссылка на **сам объект**, с которым работает метод. Через `self` метод получает доступ к атрибутам и другим методам этого объекта.

```python
class Dog:
    def __init__(self, name):
        self.name = name   # self — это объект, которому присваивается имя

    def bark(self):
        print(f"{self.name} говорит: Гав!")  # self.name — читаем имя этого объекта

dog1 = Dog("Рекс")
dog2 = Dog("Бобик")

dog1.bark()  # Рекс говорит: Гав!
dog2.bark()  # Бобик говорит: Гав!
```

Когда вы пишете `dog1.bark()`, Python за кулисами делает это:

```python
Dog.bark(dog1)  # dog1 передаётся как self
```

То есть `self` — это способ сказать методу: «работай с **этим конкретным** объектом».

## Почему `self` — именно `self`?

Это просто соглашение. Технически можно назвать как угодно, но называть иначе — плохой тон:

```python
class Cat:
    def __init__(this, name):   # Работает, но так не делают
        this.name = name

    def meow(me):               # Работает, но так не делают
        print(f"{me.name}: Мяу!")
```

Всегда используйте `self` — это стандарт, который все понимают.

## Шаг за шагом: что происходит при создании объекта

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

p = Person("Анна", 25)
```

Вот что происходит по шагам:

1. Python видит `Person("Анна", 25)` — вызов класса
2. Создаётся новый пустой объект
3. Python вызывает `Person.__init__(новый_объект, "Анна", 25)`
4. Внутри `__init__`: `self` = новый объект, `name` = `"Анна"`, `age` = `25`
5. Выполняется `self.name = "Анна"` — объект получает атрибут `name`
6. Выполняется `self.age = 25` — объект получает атрибут `age`
7. `__init__` завершается, объект возвращается и записывается в `p`

## `__init__` с значениями по умолчанию

Как и у обычных функций, параметры `__init__` могут иметь значения по умолчанию:

```python
class Profile:
    def __init__(self, username, role="user", active=True):
        self.username = username
        self.role = role
        self.active = active

    def describe(self):
        status = "активен" if self.active else "заблокирован"
        return f"{self.username} ({self.role}) — {status}"


p1 = Profile("anna")
p2 = Profile("ivan", role="admin")
p3 = Profile("bot", role="bot", active=False)

print(p1.describe())  # anna (user) — активен
print(p2.describe())  # ivan (admin) — активен
print(p3.describe())  # bot (bot) — заблокирован
```

## Вычисляемые атрибуты в `__init__`

В `__init__` можно не только сохранять переданные значения, но и вычислять новые:

```python
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height
        self.area = width * height        # Вычисляем сразу
        self.perimeter = 2 * (width + height)

r = Rectangle(4, 6)
print(r.area)       # 24
print(r.perimeter)  # 20
```

## Вызов методов внутри `__init__`

`__init__` может вызывать другие методы класса через `self`:

```python
class Temperature:
    def __init__(self, celsius):
        self.celsius = celsius
        self.fahrenheit = self._to_fahrenheit()   # Вызываем метод
        self.kelvin = self._to_kelvin()

    def _to_fahrenheit(self):
        return self.celsius * 9/5 + 32

    def _to_kelvin(self):
        return self.celsius + 273.15

t = Temperature(100)
print(t.celsius)     # 100
print(t.fahrenheit)  # 212.0
print(t.kelvin)      # 373.15
```

## 🎯 Пример с реальной задачей

Класс для хранения студента и его оценок:

```python
class Student:
    def __init__(self, name, group):
        self.name = name
        self.group = group
        self.grades = []           # Начинаем с пустым списком оценок

    def add_grade(self, subject, grade):
        """Добавить оценку"""
        self.grades.append({"subject": subject, "grade": grade})

    def average(self):
        """Средняя оценка"""
        if not self.grades:
            return 0
        total = sum(g["grade"] for g in self.grades)
        return total / len(self.grades)

    def report(self):
        """Сводка по студенту"""
        print(f"Студент: {self.name}, группа: {self.group}")
        for g in self.grades:
            print(f"  {g['subject']}: {g['grade']}")
        print(f"  Средний балл: {self.average():.1f}")


student = Student("Иван", "ПИ-21")
student.add_grade("Python", 5)
student.add_grade("Математика", 4)
student.add_grade("English", 4)
student.report()
# Студент: Иван, группа: ПИ-21
#   Python: 5
#   Математика: 4
#   English: 4
#   Средний балл: 4.3
```

## Частые ошибки

### Забыть `self` в методе

```python
class Cat:
    def __init__(self, name):
        self.name = name

    def meow():        # ❌ Забыли self
        print("Мяу!")

cat = Cat("Мурка")
cat.meow()  # TypeError: meow() takes 0 positional arguments but 1 was given
```

```python
class Cat:
    def __init__(self, name):
        self.name = name

    def meow(self):    # ✅ Правильно
        print("Мяу!")
```

### Забыть `self.` при обращении к атрибуту

```python
class Dog:
    def __init__(self, name):
        self.name = name

    def bark(self):
        print(f"{name} говорит: Гав!")  # ❌ name — не определена в этой области

dog = Dog("Рекс")
dog.bark()  # NameError: name 'name' is not defined
```

```python
    def bark(self):
        print(f"{self.name} говорит: Гав!")  # ✅ self.name — атрибут объекта
```

## 🔎 Проверка на ходу

```python
>>> class Point:
...     def __init__(self, x, y):
...         self.x = x
...         self.y = y
...     def move(self, dx, dy):
...         self.x += dx
...         self.y += dy
...
>>> p = Point(1, 2)
>>> p.x, p.y
(1, 2)
>>> p.move(3, -1)
>>> p.x, p.y
(4, 1)
```

## Резюме

- `__init__` вызывается **автоматически** при создании объекта
- `__init__` задаёт начальное состояние объекта — его атрибуты
- `self` — ссылка на сам объект, первый параметр любого метода экземпляра
- Через `self.имя` создаём и читаем атрибуты объекта
- В `__init__` можно вычислять значения и вызывать другие методы
- Без `self.` Python ищет обычную локальную переменную, а не атрибут объекта

---

[[06_02_attributes_and_methods|← 📦 Атрибуты и методы]] | [[06_04_encapsulation|🔒 Инкапсуляция →]]
