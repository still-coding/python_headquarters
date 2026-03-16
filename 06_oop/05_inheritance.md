[[04_encapsulation|← 🔒 Инкапсуляция]] | [[06_polymorphism|🦆 Полиморфизм →]]

---
# 🧬 Наследование
```toc
minLevel: 2
```

## Что такое наследование?

**Наследование** — это механизм, при котором один класс получает атрибуты и методы другого класса.

Класс, от которого наследуют, называется **родительским** (parent, base class).
Класс, который наследует, называется **дочерним** (child, subclass).

Аналогия из жизни: у родителей и детей есть общие черты (два глаза, одна голова), но у детей могут быть и свои особенности. Не нужно «переизобретать» базовую биологию для каждого человека.

```python
class Animal:          # Родительский класс
    def __init__(self, name):
        self.name = name

    def eat(self):
        return f"{self.name} ест"

class Dog(Animal):     # Дочерний класс — наследует Animal
    def bark(self):
        return f"{self.name} говорит: Гав!"


dog = Dog("Рекс")
print(dog.eat())   # Рекс ест   — метод унаследован от Animal
print(dog.bark())  # Рекс говорит: Гав!  — собственный метод Dog
```

## Синтаксис наследования

```python
class Child(Parent):
    pass
```

Дочерний класс автоматически получает всё, что есть у родителя:

```python
class Vehicle:
    def __init__(self, brand, speed):
        self.brand = brand
        self.speed = speed

    def describe(self):
        return f"{self.brand}, скорость: {self.speed} км/ч"

    def accelerate(self, delta):
        self.speed += delta


class Car(Vehicle):
    pass   # Ничего своего — только наследует


class Motorcycle(Vehicle):
    pass


car = Car("Toyota", 100)
moto = Motorcycle("Honda", 120)

print(car.describe())   # Toyota, скорость: 100 км/ч
car.accelerate(20)
print(car.describe())   # Toyota, скорость: 120 км/ч

print(moto.describe())  # Honda, скорость: 120 км/ч
```

## Расширение дочернего класса

Дочерний класс может добавлять **свои** атрибуты и методы:

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def eat(self):
        return f"{self.name} ест"


class Dog(Animal):
    def __init__(self, name, breed):   # Расширяем __init__
        self.name = name
        self.breed = breed             # Свой атрибут

    def bark(self):                    # Свой метод
        return f"{self.name} говорит: Гав!"

    def describe(self):
        return f"{self.name} — {self.breed}"


dog = Dog("Рекс", "Овчарка")
print(dog.eat())       # Рекс ест  — от родителя
print(dog.bark())      # Рекс говорит: Гав!
print(dog.describe())  # Рекс — Овчарка
```

## `super()` — вызов родительского метода

Когда дочерний класс переопределяет `__init__`, удобно не дублировать код родителя, а вызвать его через `super()`:

```python
class Animal:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def describe(self):
        return f"{self.name}, {self.age} лет"


class Dog(Animal):
    def __init__(self, name, age, breed):
        super().__init__(name, age)   # Вызываем __init__ родителя
        self.breed = breed            # Добавляем своё

    def describe(self):
        base = super().describe()     # Вызываем метод родителя
        return f"{base}, порода: {self.breed}"


dog = Dog("Рекс", 3, "Овчарка")
print(dog.describe())  # Рекс, 3 лет, порода: Овчарка
```

`super()` — это способ сказать: «вызови этот метод у моего родителя».

## Переопределение методов (Override)

Дочерний класс может **переопределить** метод родителя — заменить его своей версией:

```python
class Shape:
    def area(self):
        return 0

    def describe(self):
        return f"Фигура с площадью {self.area()}"


class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):                    # Переопределяем метод родителя
        return 3.14159 * self.radius ** 2


class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):                    # Переопределяем метод родителя
        return self.width * self.height


c = Circle(5)
r = Rectangle(4, 6)

print(c.area())      # 78.53975
print(r.area())      # 24

# describe() унаследован — но вызывает правильный area() для каждого объекта
print(c.describe())  # Фигура с площадью 78.53975
print(r.describe())  # Фигура с площадью 24
```

## Проверка принадлежности к классу

```python
class Animal:
    pass

class Dog(Animal):
    pass

class Cat(Animal):
    pass

dog = Dog()
cat = Cat()

print(isinstance(dog, Dog))     # True
print(isinstance(dog, Animal))  # True  — Dog наследует Animal
print(isinstance(dog, Cat))     # False

print(issubclass(Dog, Animal))  # True
print(issubclass(Cat, Animal))  # True
print(issubclass(Dog, Cat))     # False
```

## Множественное наследование

Python позволяет наследовать сразу от нескольких классов:

```python
class Flyable:
    def fly(self):
        return f"{self.name} летит"

class Swimmable:
    def swim(self):
        return f"{self.name} плывёт"

class Duck(Flyable, Swimmable):
    def __init__(self, name):
        self.name = name

    def quack(self):
        return f"{self.name}: Кря!"


duck = Duck("Дональд")
print(duck.fly())    # Дональд летит
print(duck.swim())   # Дональд плывёт
print(duck.quack())  # Дональд: Кря!
```

Множественное наследование мощное, но усложняет код — используйте осторожно. Альтернативу (композицию) разберём в файле [[09_composition_vs_inheritance|Композиция vs наследование]].

## 🎯 Пример с реальной задачей

Иерархия сотрудников компании:

```python
class Employee:
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

    def work(self):
        return f"{self.name} работает"

    def get_info(self):
        return f"{self.name}, зарплата: {self.salary} руб."


class Developer(Employee):
    def __init__(self, name, salary, language):
        super().__init__(name, salary)
        self.language = language

    def work(self):                          # Переопределяем
        return f"{self.name} пишет код на {self.language}"

    def get_info(self):
        base = super().get_info()
        return f"{base}, язык: {self.language}"


class Manager(Employee):
    def __init__(self, name, salary, team_size):
        super().__init__(name, salary)
        self.team_size = team_size

    def work(self):                          # Переопределяем
        return f"{self.name} управляет командой из {self.team_size} человек"


dev = Developer("Анна", 150000, "Python")
mgr = Manager("Иван", 200000, 8)

print(dev.work())      # Анна пишет код на Python
print(mgr.work())      # Иван управляет командой из 8 человек
print(dev.get_info())  # Анна, зарплата: 150000 руб., язык: Python
print(mgr.get_info())  # Иван, зарплата: 200000 руб.

# isinstance работает по иерархии
print(isinstance(dev, Developer))  # True
print(isinstance(dev, Employee))   # True
print(isinstance(mgr, Developer))  # False
```

## 🔎 Проверка на ходу

```python
>>> class A:
...     def hello(self):
...         return "Привет от A"
...
>>> class B(A):
...     pass
...
>>> class C(A):
...     def hello(self):
...         return "Привет от C"
...
>>> b = B()
>>> c = C()
>>> b.hello()   # Унаследовал от A
'Привет от A'
>>> c.hello()   # Переопределил
'Привет от C'
>>> issubclass(B, A)
True
>>> issubclass(C, A)
True
```

## Резюме

- Наследование позволяет создавать новый класс на основе существующего
- Дочерний класс получает все атрибуты и методы родителя
- `super()` вызывает метод родительского класса — удобно в `__init__` и при переопределении
- Дочерний класс может **добавлять** свои методы и **переопределять** родительские
- `isinstance(obj, Class)` — проверяет, является ли объект экземпляром класса или его наследника
- `issubclass(Child, Parent)` — проверяет иерархию классов
- Множественное наследование возможно, но используйте с осторожностью

---

[[04_encapsulation|← 🔒 Инкапсуляция]] | [[06_polymorphism|🦆 Полиморфизм →]]
