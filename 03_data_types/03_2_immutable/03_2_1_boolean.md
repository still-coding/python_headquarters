[[03_2_0_numbers|← 🔢 Числа]] | [[03_2_2_none|# 🚫 None →]]

---
# ✅ Булевы значения
```toc
minLevel: 2
```
## Что такое булевы значения?

Тип `bool` имеет всего 2 значения - `True` и `False`:

```python
yes = True  # истина
no = False  # ложь
type(yes)   # <class 'bool'>
```

## Связь с числами

На самом деле за этими значениями скрываются обычные целые числа:

```python
>>> True + False  
1  
>>> False == 0  
True  
>>> True == 1  
True
```

Обрати внимание — значения типа `bool` являются результатами операций сравнения.

## 📊 Операции сравнения

```python
# Больше
5 > 3    # True

# Меньше
5 < 3    # False

# Больше или равно
5 >= 5   # True

# Меньше или равно
5 <= 4   # False

# Равно
5 == 5   # True

# Не равно
5 != 3   # True
```

## Логические операции

### `and`

Логическое **И**, также называется **логическим умножением**.
`1 * 1 = 1` работает как `True and True = True`.

|   `a`   |   `b`   | `a and b` |
| :-----: | :-----: | :-------: |
| `False` | `False` |  `False`  |
| `False` | `True`  |  `False`  |
| `True`  | `False` |  `False`  |
| `True`  | `True`  |  `True`   |

### `or`

Логическое **ИЛИ**, также называется **логическим сложением**.
`0 + 1 = 1` работает как `False or True = True`.

| `a`     | `b`     | `a or b` |
| ------- | ------- | -------- |
| `False` | `False` | `False`  |
| `False` | `True`  | `True`   |
| `True`  | `False` | `True`   |
| `True`  | `True`  | `True`   |

### `not`

Логическое **НЕ**, также называется **логическим отрицанием**.
Меняет значение на противоположное.

|`a`|`not a`|
|---|---|
|`False`|`True`|
|`True`|`False`|

## Особенности работы логических операторов

### Примеры с `and`

```python
# ⚙️ Все значения "истинные"
1 and 2 and 3        # Результат: 3
"a" and True and 42  # Результат: 42
```

В Python оператор `and` возвращает:
- первое ложное значение (если оно есть)
- последнее истинное значение (если все истинные)

```python
# ⚙️ Есть ложные значения
0 and False and ""   # Результат: 0 (первое ложное)
True and None and 1  # Результат: None (первое ложное)
5 and 0 and 10       # Результат: 0 (первое ложное)
True and "" and 1    # Результат: "" (первое ложное)
```

### Примеры с `or`

```python
# ⚙️ Все значения "истинные"
1 or 2 or 3        # Результат: 1
"a" or True or 42  # Результат: "a"
```

В Python оператор `or` возвращает:
- первое истинное значение (если оно есть)
- последнее ложное значение (если все ложные)

```python
# ⚙️ Есть ложные значения
0 or False or 42   # Результат: 42 (первое истинное)
False or "" or 0   # Результат: 0 (последнее ложное)
None or 0 or ""    # Результат: "" (последнее ложное)
```

> ⚠️ **Важно**: Операторы `and` и `or` **не создают** новые булевы значения `True` или `False`, а возвращают сами операнды (переменные или значения). Именно поэтому результатом может быть число, строка или любой другой объект, а не только `True` или `False`.

## Short-circuiting или "ленивые" логические выражения

```python
# 🔌 Python не вычисляет дальше, если результат уже ясен
False and print("Это не выполнится")  # Результат: False, print() не выполняется
True or print("Это не выполнится")    # Результат: True, print() не выполняется
```

## Использование как условных операторов

```python
# 🚦 В реальном коде
name = ""
default_name = "Гость"
display_name = name or default_name  # Если name пустой, используем default_name
print(display_name)  # Гость
```

## 🛑 Значения, которые считаются ложными

В Python следующие значения считаются "ложными" (`False`):

```python
# Числа
0          # ноль
0.0        # ноль с плавающей точкой
0j         # комплексный ноль

# Пустые коллекции (любые, не только перечисленные)
""         # пустая строка
[]         # пустой список
()         # пустой кортеж
{}         # пустой словарь
set()      # пустое множество

# Специальные значения
None       # отсутствие значения
False      # ложь
```

Все остальные значения считаются "истинными" (`True`):

```python
bool(42)        # True
bool("hello")   # True
bool([1, 2])    # True
bool(-1)        # True (даже отрицательные числа!)
```

## Практические примеры

### Проверка данных

```python
# Проверка на пустоту
def process_data(data):
    if data:  # Эквивалентно if data is not None and data != ""
        print(f"Обрабатываем: {data}")
    else:
        print("Нет данных для обработки")

process_data("Hello")  # Обрабатываем: Hello
process_data("")       # Нет данных для обработки
process_data([])       # Нет данных для обработки
process_data(None)     # Нет данных для обработки

# Проверка списка
items = [1, 2, 3]
if items:  # Эквивалентно if len(items) > 0
    print(f"В списке {len(items)} элементов")
else:
    print("Список пуст")
```

### Условные значения по умолчанию

```python
# Установка значения по умолчанию
def get_user_name(user_data):
    return user_data.get('name') or "Анонимный пользователь"

user1 = {'name': 'Иван'}
user2 = {'name': ''}
user3 = {}

print(get_user_name(user1))  # Иван
print(get_user_name(user2))  # Анонимный пользователь
print(get_user_name(user3))  # Анонимный пользователь
```
[[img/]]
### Валидация данных

```python
def validate_user(name, email, age):
    """Проверяет корректность данных пользователя"""
    
    # Проверяем, что все поля заполнены
    if not name or not email or not age:
        return False, "Все поля обязательны для заполнения"
    
    # Проверяем возраст
    if not isinstance(age, int) or age < 0:
        return False, "Возраст должен быть положительным числом"
    
    # Проверяем email (простая проверка)
    if '@' not in email or '.' not in email:
        return False, "Некорректный email"
    
    return True, "Данные корректны"

# Тестируем валидацию
test_cases = [
    ("Иван", "ivan@example.com", 25),
    ("", "test@example.com", 30),
    ("Петр", "invalid-email", 20),
    ("Анна", "anna@test.com", -5)
]

for name, email, age in test_cases:
    is_valid, message = validate_user(name, email, age)
    print(f"Валидация: {is_valid}, Сообщение: {message}")
```

## 💡 Полезные паттерны

### Тернарное выражение

```python
# Синтаксис: значение_если_истина if условие else значение_если_ложь
age = 20
status = "Совершеннолетний" if age >= 18 else "Несовершеннолетний"
print(status)  # Совершеннолетний

# Можно использовать с любыми типами
name = ""
display_name = name if name else "Гость"
print(display_name)  # Гость
```

### Цепочки проверок

```python
# Проверка нескольких условий
def can_drive(age, has_license, is_sober):
    return age >= 18 and has_license and is_sober

# Проверка с подробными сообщениями
def check_driving_eligibility(age, has_license, is_sober):
    if age < 18:
        return False, "Недостаточный возраст"
    if not has_license:
        return False, "Нет водительских прав"
    if not is_sober:
        return False, "Нельзя водить в нетрезвом виде"
    return True, "Можно водить"

# Тестируем
print(check_driving_eligibility(25, True, True))   # (True, "Можно водить")
print(check_driving_eligibility(16, True, True))   # (False, "Недостаточный возраст")
print(check_driving_eligibility(25, False, True))  # (False, "Нет водительских прав")
```

---

[[03_2_0_numbers|← 🔢 Числа]] | [[03_2_2_none|# 🚫 None →]]
