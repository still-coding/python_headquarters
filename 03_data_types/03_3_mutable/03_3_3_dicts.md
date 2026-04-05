[[03_3_2_tuples|← 📦 Кортежи]] | [[03_3_4_dict_methods|📚 Методы словарей →]]

---
# 📖 Словари `dict`
```toc
minLevel: 2
```
Словарь (dictionary, dict) - это **изменяемая** коллекция пар **ключ-значение**. Словари позволяют быстро находить значения по ключу.

> ⚠️ **Важно:** С Python 3.7+ словари сохраняют порядок добавления элементов!

## Создание словарей

```python
# Пустой словарь
empty = {}
also_empty = dict()

# Словарь с элементами
person = {
    "name": "Иван",
    "age": 25,
    "city": "Москва"
}

# Из списка кортежей
pairs = [("a", 1), ("b", 2), ("c", 3)]
from_pairs = dict(pairs)

# С помощью именованных аргументов
student = dict(name="Анна", age=20, course=2)

# Из двух списков с помощью zip()
keys = ["name", "age", "city"]
values = ["Петр", 30, "СПб"]
person2 = dict(zip(keys, values))

# С помощью dict comprehension
squares = {x: x**2 for x in range(5)}
print(squares)  # {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}
```

## Доступ к элементам

```python
person = {"name": "Иван", "age": 25, "city": "Москва"}

# Получение значения по ключу
name = person["name"]        # "Иван"
age = person["age"]          # 25

# Безопасное получение с помощью get()
city = person.get("city")                    # "Москва"
job = person.get("job")                      # None
job = person.get("job", "Не указано")        # "Не указано"

# Если ключа нет, будет KeyError
try:
    phone = person["phone"]  # KeyError: 'phone'
except KeyError as e:
    print(f"Ключ не найден: {e}")
```

## Изменение и добавление элементов

```python
person = {"name": "Иван", "age": 25}

# Изменение существующего значения
person["age"] = 26
print(person)  # {"name": "Иван", "age": 26}

# Добавление нового ключа
person["city"] = "Москва"
person["job"] = "Программист"
print(person)  # {"name": "Иван", "age": 26, "city": "Москва", "job": "Программист"}

# Использование setdefault() - добавляет только если ключа нет
person.setdefault("age", 30)         # Не изменит, т.к. "age" уже есть
person.setdefault("hobby", "Спорт")  # Добавит новый ключ
print(person)
```

## Удаление элементов

```python
person = {"name": "Иван", "age": 25, "city": "Москва", "temp": "удалить"}

# del - удаление по ключу
del person["temp"]
print(person)  # {"name": "Иван", "age": 25, "city": "Москва"}

# pop() - удаление с возвратом значения
age = person.pop("age")
print(age)     # 25
print(person)  # {"name": "Иван", "city": "Москва"}

# pop() с значением по умолчанию
job = person.pop("job", "Не найдено")  # "Не найдено"

# popitem() - удаление и возврат последней пары
last_key, last_value = person.popitem()
print(f"{last_key}: {last_value}")

# clear() - удаление всех элементов
person.clear()
print(person)  # {}
```

## Проверка наличия ключа

```python
person = {"name": "Иван", "age": 25, "city": "Москва"}

# Проверка с помощью in
if "name" in person:
    print("Ключ 'name' существует")

if "job" not in person:
    print("Ключ 'job' отсутствует")

# Проверка в значениях
if "Иван" in person.values():
    print("Значение 'Иван' найдено")

# Проверка пары ключ-значение
if ("name", "Иван") in person.items():
    print("Пара найдена")
```

## Итерация по словарю

```python
person = {"name": "Иван", "age": 25, "city": "Москва"}

# По умолчанию - итерация по ключам
for key in person:
    print(key)
# name
# age
# city

# Явная итерация по ключам
for key in person.keys():
    print(f"{key}: {person[key]}")

# Итерация по значениям
for value in person.values():
    print(value)
# Иван
# 25
# Москва

# Итерация по парам ключ-значение (самый популярный способ!)
for key, value in person.items():
    print(f"{key}: {value}")
# name: Иван
# age: 25
# city: Москва
```

## Представления: keys(), values(), items()

```python
person = {"name": "Иван", "age": 25, "city": "Москва"}

# Получение представлений (views)
keys = person.keys()      # dict_keys(['name', 'age', 'city'])
values = person.values()  # dict_values(['Иван', 25, 'Москва'])
items = person.items()    # dict_items([('name', 'Иван'), ('age', 25), ('city', 'Москва')])
```

## Преобразование в списки

```python
keys_list = list(person.keys())
values_list = list(person.values())
items_list = list(person.items())
```

## Методы словарей

Словари имеют множество полезных методов. Подробное описание см. в [[03_3_4_dict_methods|Методы словарей]].

Краткий обзор:

```python
person = {"name": "Иван", "age": 25}

# Безопасное получение значения
name = person.get("name", "Неизвестно")
city = person.get("city", "Не указано")  # Вернет "Не указано"

# Установка значения по умолчанию
person.setdefault("city", "Москва")  # Добавит, если ключа нет

# Обновление словаря
person.update({"job": "Программист", "age": 26})

# Удаление с возвратом
age = person.pop("age")           # Удаляет и возвращает значение
key, value = person.popitem()     # Удаляет и возвращает последнюю пару

# Очистка
person.clear()  # Удаляет все элементы
```

Подробнее о методах: [[03_3_4_dict_methods|Методы словарей →]]

## Dictionary Comprehensions (Включения словарей)

```python
# Создание словаря
squares = {x: x**2 for x in range(5)}
print(squares)  # {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# С условием
even_squares = {x: x**2 for x in range(10) if x % 2 == 0}
print(even_squares)  # {0: 0, 2: 4, 4: 16, 6: 36, 8: 64}

# Обращение словаря
original = {"a": 1, "b": 2, "c": 3}
reversed_dict = {v: k for k, v in original.items()}
print(reversed_dict)  # {1: 'a', 2: 'b', 3: 'c'}

# Из двух списков
keys = ["name", "age", "city"]
values = ["Иван", 25, "Москва"]
person = {k: v for k, v in zip(keys, values)}
```

## Вложенные словари

```python
students = {
    "Анна": {
        "age": 20,
        "grades": {"математика": 5, "физика": 4},
        "city": "Москва"
    },
    "Иван": {
        "age": 21,
        "grades": {"математика": 4, "физика": 5},
        "city": "СПб"
    }
}

# Доступ к вложенным значениям
anna_math = students["Анна"]["grades"]["математика"]
print(anna_math)  # 5

# Безопасный доступ
def safe_get(d, *keys, default=None):
    """Безопасное получение значения по цепочке ключей"""
    for key in keys:
        if isinstance(d, dict) and key in d:
            d = d[key]
        else:
            return default
    return d

# Использование
math_grade = safe_get(students, "Анна", "grades", "математика")
missing = safe_get(students, "Петр", "age", default="Не найдено")
```

## Когда использовать словари?

✅ **Используйте словари для:**
- Хранения пар ключ-значение
- Быстрого поиска по ключу (O(1) в среднем)
- Структурированных данных (как записи/объекты)
- Подсчета элементов
- Кэширования результатов
- Конфигураций и настроек

❌ **Не используйте словари когда:**
- Важен порядок элементов (хотя с Python 3.7+ порядок сохраняется)
- Нужен доступ по индексу
- Ключи должны быть изменяемыми (ключи должны быть hashable)

## Особенности ключей

```python
# ✅ Допустимые типы ключей (неизменяемые/hashable)
valid = {
    "string_key": 1,
    42: 2,
    3.14: 3,
    (1, 2): 4,
    True: 5,
    frozenset([1, 2]): 6
}

# ❌ Недопустимые типы ключей (изменяемые)
# invalid = {
#     [1, 2]: "list",        # TypeError: unhashable type: 'list'
#     {1, 2}: "set",          # TypeError: unhashable type: 'set'
#     {"a": 1}: "dict"        # TypeError: unhashable type: 'dict'
# }

# Проверка hashable
from collections.abc import Hashable
print(isinstance("text", Hashable))    # True
print(isinstance([1, 2], Hashable))    # False
```

## Объединение словарей

```python
# Python 3.9+ - оператор |
dict1 = {"a": 1, "b": 2}
dict2 = {"c": 3, "d": 4}
dict3 = {"b": 20, "e": 5}

# Объединение без изменения исходных
merged = dict1 | dict2 | dict3
print(merged)  # {'a': 1, 'b': 20, 'c': 3, 'd': 4, 'e': 5}

# Объединение с изменением первого
dict1 |= dict2
print(dict1)  # {'a': 1, 'b': 2, 'c': 3, 'd': 4}

# Универсальный способ (для старых версий)
merged = {**dict1, **dict2, **dict3}

# Через update()
result = dict1.copy()
result.update(dict2)
result.update(dict3)
```

## Практические примеры

### Подсчет элементов

```python
# Ручной подсчет
text = "hello world"
char_count = {}
for char in text:
    char_count[char] = char_count.get(char, 0) + 1
print(char_count)

# С использованием Counter
from collections import Counter
char_count = Counter(text)
print(char_count.most_common(3))  # 3 самых частых
```

### Группировка данных

```python
students = [
    ("Анна", "Москва"),
    ("Иван", "СПб"),
    ("Петр", "Москва"),
    ("Мария", "СПб")
]

# Группировка по городам
by_city = {}
for name, city in students:
    by_city.setdefault(city, []).append(name)

print(by_city)
# {'Москва': ['Анна', 'Петр'], 'СПб': ['Иван', 'Мария']}
```

### Инвертирование словаря

```python
grades = {"математика": 5, "физика": 4, "химия": 5, "биология": 4}

# Группировка по значениям
by_grade = {}
for subject, grade in grades.items():
    by_grade.setdefault(grade, []).append(subject)

print(by_grade)
# {5: ['математика', 'химия'], 4: ['физика', 'биология']}
```

### Кэширование результатов

```python
# Простой кэш
cache = {}

def expensive_function(n):
    if n in cache:
        print(f"Возврат из кэша для {n}")
        return cache[n]
    
    print(f"Вычисление для {n}")
    result = n ** 2  # Долгие вычисления
    cache[n] = result
    return result

print(expensive_function(5))  # Вычисление для 5
print(expensive_function(5))  # Возврат из кэша для 5
```

## Сортировка словарей

```python
grades = {"математика": 5, "физика": 3, "химия": 4, "биология": 2}

# По ключам
sorted_by_keys = dict(sorted(grades.items()))
print(sorted_by_keys)

# По значениям
sorted_by_values = dict(sorted(grades.items(), key=lambda item: item[1]))
print(sorted_by_values)

# По значениям (убывание)
sorted_desc = dict(sorted(grades.items(), key=lambda x: x[1], reverse=True))
print(sorted_desc)
```

## Проверка и сравнение

```python
dict1 = {"a": 1, "b": 2}
dict2 = {"b": 2, "a": 1}  # Порядок не важен
dict3 = {"a": 1, "b": 2, "c": 3}

# Равенство
print(dict1 == dict2)  # True (одинаковые ключи и значения)
print(dict1 == dict3)  # False

# Проверка подмножества ключей
def is_subset(subset_dict, main_dict):
    """Проверяет, содержатся ли все ключи и значения subset_dict в main_dict"""
    for key, value in subset_dict.items():
        if key not in main_dict or main_dict[key] != value:
            return False
    return True

print(is_subset(dict1, dict3))  # True
print(is_subset(dict3, dict1))  # False
```

---

[[03_3_2_tuples|← 📦 Кортежи]] | [[03_3_4_dict_methods|📚 Методы словарей →]]