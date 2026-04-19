[[07_01_classes_and_objects|← 🧱 Классы и объекты]] | [[07_03_init_and_self|🔧 `__init__` и `self` →]]

---
# 📦 Атрибуты и методы
```toc
minLevel: 2
```

## Атрибуты — данные объекта

**Атрибут** — это переменная, которая принадлежит объекту или классу. Атрибуты хранят состояние.

Есть два вида атрибутов:
- **Атрибуты экземпляра** — у каждого объекта свои
- **Атрибуты класса** — общие для всех объектов

## Атрибуты экземпляра

Создаются внутри `__init__` через `self.имя`:

```python
class Dog:
    def __init__(self, name, breed):
        self.name = name    # Атрибут экземпляра
        self.breed = breed  # Атрибут экземпляра

dog1 = Dog("Рекс", "Овчарка")
dog2 = Dog("Бобик", "Дворняга")

print(dog1.name)   # Рекс
print(dog2.name)   # Бобик  — у каждого объекта своё значение
```

Каждый объект хранит **свою копию** атрибутов — изменение одного не затрагивает другой:

```python
dog1.name = "Макс"
print(dog1.name)  # Макс
print(dog2.name)  # Бобик  — не изменился
```

## Атрибуты класса

Создаются прямо в теле класса, **вне** методов. Общие для всех экземпляров:

```python
class Dog:
    species = "Canis lupus familiaris"  # Атрибут класса

    def __init__(self, name):
        self.name = name  # Атрибут экземпляра

dog1 = Dog("Рекс")
dog2 = Dog("Бобик")

# Доступны и через класс, и через объект
print(Dog.species)    # Canis lupus familiaris
print(dog1.species)   # Canis lupus familiaris
print(dog2.species)   # Canis lupus familiaris
```

Изменение атрибута класса через класс затрагивает **все** объекты:

```python
Dog.species = "Собака"
print(dog1.species)  # Собака
print(dog2.species)  # Собака
```

### Атрибут класса как счётчик объектов

Классический пример полезного атрибута класса:

```python
class Dog:
    count = 0  # Сколько объектов создано

    def __init__(self, name):
        self.name = name
        Dog.count += 1  # Увеличиваем при каждом создании

dog1 = Dog("Рекс")
dog2 = Dog("Бобик")
dog3 = Dog("Шарик")

print(Dog.count)  # 3
```

## Методы — поведение объекта

**Метод** — это функция внутри класса. Методы описывают, что объект умеет делать.

### Методы экземпляра

Самый распространённый вид. Первый параметр всегда `self` — ссылка на сам объект:

```python
class Circle:
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        """Площадь круга"""
        return 3.14159 * self.radius ** 2

    def perimeter(self):
        """Длина окружности"""
        return 2 * 3.14159 * self.radius

    def scale(self, factor):
        """Изменить радиус в factor раз"""
        self.radius *= factor

c = Circle(5)
print(c.area())       # 78.53975
print(c.perimeter())  # 31.4159

c.scale(2)
print(c.radius)       # 10
print(c.area())       # 314.159
```

### Метод изменяет состояние объекта

Методы могут не только читать атрибуты, но и изменять их:

```python
class Counter:
    def __init__(self):
        self.value = 0

    def increment(self):
        self.value += 1

    def decrement(self):
        self.value -= 1

    def reset(self):
        self.value = 0

    def get(self):
        return self.value


c = Counter()
c.increment()
c.increment()
c.increment()
print(c.get())  # 3

c.decrement()
print(c.get())  # 2

c.reset()
print(c.get())  # 0
```

## Разница: атрибут vs метод

```python
class Car:
    def __init__(self, brand, speed):
        self.brand = brand       # Атрибут — хранит данные
        self.speed = speed

    def accelerate(self, delta):  # Метод — выполняет действие
        self.speed += delta
        return self.speed

car = Car("Toyota", 60)

# Атрибут — просто значение, без скобок
print(car.brand)   # Toyota

# Метод — вызываем со скобками
print(car.accelerate(20))  # 80
```

## 🎯 Пример с реальной задачей

Моделируем корзину товаров в интернет-магазине:

```python
class ShoppingCart:
    """Корзина покупателя"""

    discount_rate = 0.0  # Атрибут класса — скидка для всех

    def __init__(self, owner):
        self.owner = owner
        self.items = []       # Список товаров
        self.total = 0        # Сумма

    def add_item(self, name, price):
        """Добавить товар в корзину"""
        self.items.append({"name": name, "price": price})
        self.total += price

    def remove_item(self, name):
        """Удалить товар по имени"""
        for item in self.items:
            if item["name"] == name:
                self.total -= item["price"]
                self.items.remove(item)
                return True
        return False

    def get_total(self):
        """Итого с учётом скидки"""
        discount = self.total * ShoppingCart.discount_rate
        return self.total - discount

    def show(self):
        """Показать содержимое корзины"""
        print(f"Корзина {self.owner}:")
        for item in self.items:
            print(f"  {item['name']}: {item['price']} руб.")
        print(f"  Итого: {self.get_total()} руб.")


cart1 = ShoppingCart("Анна")
cart1.add_item("Книга", 500)
cart1.add_item("Ручка", 50)
cart1.show()
# Корзина Анна:
#   Книга: 500 руб.
#   Ручка: 50 руб.
#   Итого: 550 руб.

# Включаем скидку 10% для всех
ShoppingCart.discount_rate = 0.1
cart1.show()
# Итого: 495.0 руб.
```

## Когда использовать атрибут класса?

| Ситуация | Вид атрибута |
|---|---|
| У каждого объекта своё значение (имя, возраст) | Атрибут экземпляра |
| Значение одинаково для всех объектов (вид, константа) | Атрибут класса |
| Нужно считать количество объектов | Атрибут класса |
| Настройка, общая для всего класса (скидка, лимит) | Атрибут класса |

## 🔎 Проверка на ходу

```python
>>> class Point:
...     dimensions = 2          # Атрибут класса
...     def __init__(self, x, y):
...         self.x = x          # Атрибут экземпляра
...         self.y = y
...     def distance_to_origin(self):
...         return (self.x**2 + self.y**2) ** 0.5
...
>>> p = Point(3, 4)
>>> p.x
3
>>> p.distance_to_origin()
5.0
>>> Point.dimensions
2
>>> p.dimensions
2
```

## Резюме

- **Атрибуты экземпляра** — создаются в `__init__`, уникальны для каждого объекта
- **Атрибуты класса** — создаются в теле класса, общие для всех объектов
- **Методы** — функции внутри класса, первый параметр всегда `self`
- Методы вызываются со скобками: `obj.method()`, атрибуты — без: `obj.attr`
- Методы могут как читать, так и изменять атрибуты объекта

---

[[07_01_classes_and_objects|← 🧱 Классы и объекты]] | [[07_03_init_and_self|🔧 `__init__` и `self` →]]
