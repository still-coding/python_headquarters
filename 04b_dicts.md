# Методы словарей в Python

## Что такое методы словарей?

Методы словарей - это встроенные функции для работы со словарями. Словари **изменяемы** (mutable), поэтому многие методы изменяют сам словарь, а не создают новый.

```python
# Синтаксис: словарь.метод()
student = {"name": "Анна", "age": 25}
student.update({"city": "Москва"})  # Изменяет исходный словарь
print(student)  # {"name": "Анна", "age": 25, "city": "Москва"}
```

---

## 1. Методы доступа к элементам

### `get()` - Получение значения по ключу с возможностью указать значение по умолчанию

```python
dict.get(key: Any, default: Any = None) -> Any
```
- **`key`** - ключ для поиска
- **`default`** - значение, которое возвращается, если ключ не найден

```python
student = {"name": "Анна", "age": 25, "city": "Москва"}

# Обычный доступ
name = student.get("name")
print(name)  # Анна

# Ключ не найден - возвращает None
grade = student.get("grade")
print(grade)  # None

# Ключ не найден - возвращает значение по умолчанию
grade = student.get("grade", "Не указано")
print(grade)  # Не указано

# Сравнение с прямым обращением
# student["grade"]  # KeyError!
print(student.get("grade", 0))  # 0 - безопасно

# Практический пример: подсчет элементов
text = "hello world"
char_count = {}
for char in text:
    char_count[char] = char_count.get(char, 0) + 1
print(char_count)  # {'h': 1, 'e': 1, 'l': 3, 'o': 2, ' ': 1, 'w': 1, 'r': 1, 'd': 1}
```

### `setdefault()` - Получение значения или установка значения по умолчанию
```python
dict.setdefault(key: Any, default: Any = None) -> Any
```
- **`key`** - ключ для поиска/создания
- **`default`** - значение для установки, если ключ не найден

```python
student = {"name": "Анна", "age": 25}

# Ключ существует - возвращает значение
name = student.setdefault("name", "Неизвестно")
print(name)  # Анна
print(student)  # {"name": "Анна", "age": 25}

# Ключ не существует - создает с значением по умолчанию
city = student.setdefault("city", "Москва")
print(city)  # Москва
print(student)  # {"name": "Анна", "age": 25, "city": "Москва"}

# Практический пример: группировка данных
students_by_city = {}
students = [
    ("Анна", "Москва"),
    ("Иван", "СПб"),
    ("Петр", "Москва"),
    ("Мария", "СПб")
]

for name, city in students:
    students_by_city.setdefault(city, []).append(name)

print(students_by_city)
# {'Москва': ['Анна', 'Петр'], 'СПб': ['Иван', 'Мария']}
```

---

## 2. Методы получения ключей, значений и пар

### `keys()` - Получение всех ключей

```python
dict.keys() -> dict_keys
```

```python
student = {"name": "Анна", "age": 25, "city": "Москва"}

# Получение ключей
keys = student.keys()
print(keys)  # dict_keys(['name', 'age', 'city'])

# Преобразование в список
keys_list = list(keys)
print(keys_list)  # ['name', 'age', 'city']

# Итерация по ключам
for key in student.keys():
    print(f"Ключ: {key}")

# Проверка наличия ключа
if "name" in student.keys():
    print("Ключ 'name' существует")

# Более эффективная проверка (без .keys())
if "name" in student:
    print("Ключ 'name' существует")
```

### `values()` - Получение всех значений

```python
dict.values() -> dict_values
```

```python
student = {"name": "Анна", "age": 25, "city": "Москва"}

# Получение значений
values = student.values()
print(values)  # dict_values(['Анна', 25, 'Москва'])

# Преобразование в список
values_list = list(values)
print(values_list)  # ['Анна', 25, 'Москва']

# Итерация по значениям
for value in student.values():
    print(f"Значение: {value}")

# Практический пример: проверка наличия значения
grades = {"математика": 5, "физика": 4, "химия": 5}
if 5 in grades.values():
    print("Есть отличные оценки!")

# Подсчет среднего балла
average = sum(grades.values()) / len(grades)
print(f"Средний балл: {average}")
```

### `items()` - Получение пар ключ-значение

```python
dict.items() -> dict_items
```

```python
student = {"name": "Анна", "age": 25, "city": "Москва"}

# Получение пар
items = student.items()
print(items)  # dict_items([('name', 'Анна'), ('age', 25), ('city', 'Москва')])

# Преобразование в список кортежей
items_list = list(items)
print(items_list)  # [('name', 'Анна'), ('age', 25), ('city', 'Москва')]

# Итерация по парам (самый популярный способ)
for key, value in student.items():
    print(f"{key}: {value}")

# Практический пример: создание отчета
def create_report(data):
    report = "=== ОТЧЕТ ===\n"
    for key, value in data.items():
        report += f"{key.upper()}: {value}\n"
    return report

print(create_report(student))
```

---

## 3. Методы добавления и обновления

### `update()` - Обновление словаря данными из другого словаря или итерируемого объекта

```python
dict.update(other: dict | Iterable = (), **kwargs) -> None
```
- **`other`** - словарь или итерируемый объект с парами ключ-значение
- **`**kwargs`** - именованные аргументы

```python
student = {"name": "Анна", "age": 25}

# Обновление другим словарем
additional_info = {"city": "Москва", "university": "МГУ"}
student.update(additional_info)
print(student)  # {'name': 'Анна', 'age': 25, 'city': 'Москва', 'university': 'МГУ'}

# Обновление именованными аргументами
student.update(course=3, specialty="Программирование")
print(student)  # {'name': 'Анна', 'age': 25, 'city': 'Москва', 'university': 'МГУ', 'course': 3, 'specialty': 'Программирование'}

# Обновление списком кортежей
grades = {"математика": 4, "физика": 3}
new_grades = [("химия", 5), ("биология", 4)]
grades.update(new_grades)
print(grades)  # {'математика': 4, 'физика': 3, 'химия': 5, 'биология': 4}

# Перезапись существующих ключей
student.update(age=26, city="СПб")
print(student['age'])  # 26
print(student['city'])  # СПб

# Практический пример: слияние конфигураций
default_config = {"host": "localhost", "port": 8000, "debug": False}
user_config = {"port": 3000, "debug": True, "database": "mydb"}

config = default_config.copy()
config.update(user_config)
print(config)  # {'host': 'localhost', 'port': 3000, 'debug': True, 'database': 'mydb'}
```

---

## 4. Методы удаления элементов

### `pop()` - Удаление и возврат значения по ключу

```python
dict.pop(key: Any, [default: Any]) -> Any
```
- **`key`** - ключ для удаления
- **`default`** - значение по умолчанию, если ключ не найден

```python
student = {"name": "Анна", "age": 25, "city": "Москва", "temp": "удалить"}

# Удаление существующего ключа
temp_value = student.pop("temp")
print(temp_value)  # удалить
print(student)  # {'name': 'Анна', 'age': 25, 'city': 'Москва'}

# Удаление с значением по умолчанию
grade = student.pop("grade", "Нет оценки")
print(grade)  # Нет оценки

# Без значения по умолчанию вызовет KeyError
try:
    student.pop("nonexistent")
except KeyError:
    print("Ключ не найден!")

# Практический пример: извлечение и обработка данных
def process_user_data(data):
    # Извлекаем временные поля
    session_id = data.pop("session_id", None)
    csrf_token = data.pop("csrf_token", None)
    
    # Обрабатываем основные данные
    print(f"Обработка данных: {data}")
    
    # Возвращаем служебную информацию
    return {"session": session_id, "token": csrf_token}

user_data = {
    "name": "Иван", 
    "email": "ivan@example.com",
    "session_id": "abc123",
    "csrf_token": "xyz789"
}

service_info = process_user_data(user_data)
print(f"Служебная информация: {service_info}")
print(f"Данные пользователя: {user_data}")
```

### `popitem()` - Удаление и возврат последней пары ключ-значение

```python
dict.popitem() -> tuple[Any, Any]
```

```python
student = {"name": "Анна", "age": 25, "city": "Москва"}

# Удаление последнего элемента (в Python 3.7+ порядок гарантирован)
last_item = student.popitem()
print(last_item)  # ('city', 'Москва')
print(student)  # {'name': 'Анна', 'age': 25}

# Еще одно удаление
another_item = student.popitem()
print(another_item)  # ('age', 25)

# Если словарь пуст, будет KeyError
empty_dict = {}
try:
    empty_dict.popitem()
except KeyError:
    print("Словарь пуст!")

# Практический пример: обработка стека операций
operations = {"op1": "save", "op2": "edit", "op3": "delete"}

while operations:
    op_name, op_type = operations.popitem()
    print(f"Выполняется операция {op_name}: {op_type}")

print("Все операции выполнены!")
```

### `clear()` - Удаление всех элементов

```python
dict.clear() -> None
```

```python
student = {"name": "Анна", "age": 25, "city": "Москва"}

print(f"До очистки: {student}")
student.clear()
print(f"После очистки: {student}")  # {}

# Практический пример: сброс кэша
cache = {"user1": "data1", "user2": "data2", "user3": "data3"}

def clear_cache():
    cache.clear()
    print("Кэш очищен")

print(f"Кэш: {cache}")
clear_cache()
print(f"Кэш после очистки: {cache}")
```

---

## 5. Методы копирования

### `copy()` - Создание поверхностной копии словаря

```python
dict.copy() -> dict
```

```python
original = {"name": "Анна", "age": 25, "grades": [4, 5, 3]}

# Поверхностная копия
shallow_copy = original.copy()
shallow_copy["name"] = "Мария"  # Изменяем простое значение

print(f"Оригинал: {original}")  # {'name': 'Анна', 'age': 25, 'grades': [4, 5, 3]}
print(f"Копия: {shallow_copy}")  # {'name': 'Мария', 'age': 25, 'grades': [4, 5, 3]}

# Но изменение вложенного объекта влияет на оба словаря!
shallow_copy["grades"].append(5)
print(f"Оригинал после изменения списка: {original}")  # {'name': 'Анна', 'age': 25, 'grades': [4, 5, 3, 5]}
print(f"Копия после изменения списка: {shallow_copy}")  # {'name': 'Мария', 'age': 25, 'grades': [4, 5, 3, 5]}

# Альтернативные способы поверхностного копирования
copy1 = dict(original)
copy2 = {**original}

# Для глубокого копирования нужен модуль copy
import copy
deep_copy = copy.deepcopy(original)
deep_copy["grades"].append(4)

print(f"Оригинал: {original}")  # Не изменился
print(f"Глубокая копия: {deep_copy}")  # Изменилась только она

# Практический пример: создание шаблонов
user_template = {
    "role": "user",
    "permissions": ["read"],
    "settings": {"theme": "light", "language": "ru"}
}

def create_user(name, email):
    user = user_template.copy()
    user.update({"name": name, "email": email})
    return user

user1 = create_user("Анна", "anna@example.com")
user2 = create_user("Иван", "ivan@example.com")

print(f"Пользователь 1: {user1}")
print(f"Пользователь 2: {user2}")
```

---

## 6. Методы сравнения и проверки

### Сравнение словарей с помощью операторов

```python
dict1 = {"a": 1, "b": 2}
dict2 = {"b": 2, "a": 1}  # Порядок не важен
dict3 = {"a": 1, "b": 2, "c": 3}

# Равенство
print(dict1 == dict2)  # True (одинаковые ключи и значения)
print(dict1 == dict3)  # False

# Неравенство
print(dict1 != dict3)  # True

# Проверка включения ключей (нет встроенных операторов <= и >=)
def is_subset(subset_dict, main_dict):
    """Проверяет, являются ли все ключи и значения subset_dict частью main_dict"""
    for key, value in subset_dict.items():
        if key not in main_dict or main_dict[key] != value:
            return False
    return True

print(is_subset(dict1, dict3))  # True
print(is_subset(dict3, dict1))  # False

# Проверка наличия ключей
student = {"name": "Анна", "age": 25}

if "name" in student:
    print(f"Имя студента: {student['name']}")

if "grade" not in student:
    print("Оценка не указана")

# Проверка наличия значений
if "Анна" in student.values():
    print("Студентка Анна найдена")
```

---

## 7. Практические примеры

### Объединение словарей

```python
# Python 3.9+ - оператор |
dict1 = {"a": 1, "b": 2}
dict2 = {"c": 3, "d": 4}
dict3 = {"b": 20, "e": 5}  # Перекрывающийся ключ

# Объединение без изменения исходных
merged = dict1 | dict2 | dict3
print(merged)  # {'a': 1, 'b': 20, 'c': 3, 'd': 4, 'e': 5}

# Объединение с изменением первого словаря
dict1 |= dict2
print(dict1)  # {'a': 1, 'b': 2, 'c': 3, 'd': 4}

# Универсальный способ (для старых версий Python)
def merge_dicts(*dicts):
    result = {}
    for d in dicts:
        result.update(d)
    return result

merged_old = merge_dicts({"a": 1}, {"b": 2}, {"c": 3})
print(merged_old)  # {'a': 1, 'b': 2, 'c': 3}

# Объединение с помощью **
merged_unpack = {**dict1, **dict2, **dict3}
print(merged_unpack)
```

### Инвертирование словаря

```python
original = {"name": "Анна", "age": "25", "city": "Москва"}

# Простое инвертирование
inverted = {value: key for key, value in original.items()}
print(inverted)  # {'Анна': 'name', '25': 'age', 'Москва': 'city'}

# Инвертирование с проверкой уникальности значений
def safe_invert(d):
    values = list(d.values())
    if len(values) != len(set(values)):
        raise ValueError("Значения должны быть уникальными для инвертирования")
    return {v: k for k, v in d.items()}

# Инвертирование для случая неуникальных значений (группировка)
grades = {"математика": 5, "физика": 4, "химия": 5, "биология": 4}

def group_by_values(d):
    result = {}
    for key, value in d.items():
        result.setdefault(value, []).append(key)
    return result

grouped = group_by_values(grades)
print(grouped)  # {5: ['математика', 'химия'], 4: ['физика', 'биология']}
```

### Фильтрация словаря

```python
student_grades = {
    "математика": 5,
    "физика": 3,
    "химия": 4,
    "биология": 2,
    "история": 5
}

# Фильтрация по значениям
good_grades = {subject: grade for subject, grade in student_grades.items() if grade >= 4}
print(good_grades)  # {'математика': 5, 'химия': 4, 'история': 5}

# Фильтрация по ключам
science_subjects = {subject: grade for subject, grade in student_grades.items() 
                   if subject in ['математика', 'физика', 'химия', 'биология']}
print(science_subjects)  # {'математика': 5, 'физика': 3, 'химия': 4, 'биология': 2}

# Функция фильтрации
def filter_dict(d, key_filter=None, value_filter=None):
    result = {}
    for k, v in d.items():
        key_ok = key_filter(k) if key_filter else True
        value_ok = value_filter(v) if value_filter else True
        if key_ok and value_ok:
            result[k] = v
    return result

# Использование функции фильтрации
excellent_science = filter_dict(
    student_grades,
    key_filter=lambda k: k in ['математика', 'физика', 'химия'],
    value_filter=lambda v: v == 5
)
print(excellent_science)  # {'математика': 5}
```

### Работа с вложенными словарями

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

# Безопасное получение вложенных значений
def safe_get(d, *keys, default=None):
    """Безопасное получение значения по цепочке ключей"""
    for key in keys:
        if isinstance(d, dict) and key in d:
            d = d[key]
        else:
            return default
    return d

# Примеры использования
anna_math_grade = safe_get(students, "Анна", "grades", "математика")
print(anna_math_grade)  # 5

nonexistent = safe_get(students, "Петр", "age", default="Не найдено")
print(nonexistent)  # Не найдено

# Обновление вложенных значений
def update_nested(d, keys, value):
    """Обновление значения по цепочке ключей"""
    for key in keys[:-1]:
        d = d.setdefault(key, {})
    d[keys[-1]] = value

# Добавляем новую оценку
update_nested(students, ["Анна", "grades", "химия"], 5)
print(students["Анна"]["grades"])  # {'математика': 5, 'физика': 4, 'химия': 5}

# Вычисление статистики
def calculate_average_grades(students_dict):
    averages = {}
    for name, info in students_dict.items():
        grades = info.get("grades", {})
        if grades:
            average = sum(grades.values()) / len(grades)
            averages[name] = round(average, 2)
    return averages

avg_grades = calculate_average_grades(students)
print(avg_grades)  # {'Анна': 4.67, 'Иван': 4.5}
```

---

## 8. Полезные операции со словарями

### Проверка и подсчет

```python
data = {"a": 1, "b": 2, "c": 3, "d": 2, "e": 1}

# Длина словаря
print(len(data))  # 5

# Проверка пустоты
empty_dict = {}
if not empty_dict:  # Пустой словарь равен False
    print("Словарь пуст")

# Подсчет уникальных значений
unique_values = len(set(data.values()))
print(f"Уникальных значений: {unique_values}")  # 3

# Поиск ключей по значению
def find_keys_by_value(d, target_value):
    return [key for key, value in d.items() if value == target_value]

keys_with_value_2 = find_keys_by_value(data, 2)
print(keys_with_value_2)  # ['b', 'd']
```

### Сортировка словарей

```python
grades = {"математика": 5, "физика": 3, "химия": 4, "биология": 2}

# Сортировка по ключам
sorted_by_keys = dict(sorted(grades.items()))
print(sorted_by_keys)  # {'биология': 2, 'математика': 5, 'физика': 3, 'химия': 4}

# Сортировка по значениям
sorted_by_values = dict(sorted(grades.items(), key=lambda item: item[1]))
print(sorted_by_values)  # {'биология': 2, 'физика': 3, 'химия': 4, 'математика': 5}

# Сортировка по значениям (убывание)
sorted_desc = dict(sorted(grades.items(), key=lambda item: item[1], reverse=True))
print(sorted_desc)  # {'математика': 5, 'химия': 4, 'физика': 3, 'биология': 2}

# Топ N элементов
def get_top_n(d, n=3, by_value=True):
    sorted_items = sorted(d.items(), 
                         key=lambda item: item[1] if by_value else item[0], 
                         reverse=True)
    return dict(sorted_items[:n])

top_subjects = get_top_n(grades, n=2)
print(top_subjects)  # {'математика': 5, 'химия': 4}
```

---

## Заключение

Методы словарей в Python предоставляют мощные инструменты для работы с данными типа ключ-значение. Помните:

1. **Словари изменяемы** - многие методы изменяют исходный словарь
2. **Используйте `get()` для безопасного доступа** к элементам
3. **`setdefault()` удобен для группировки** данных
4. **`update()` позволяет легко объединять** словари
5. **Методы `keys()`, `values()`, `items()`** возвращают представления, а не списки
6. **Порядок элементов сохраняется** в Python 3.7+

Этот справочник поможет вам быстро найти нужный метод и эффективно работать со словарями!