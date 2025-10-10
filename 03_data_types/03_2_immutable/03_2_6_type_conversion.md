[[03_2_5_str_methods|← 🔤 Методы строк]] | [[03_3_1_lists|📋 Списки →]]

---
# 📋 Преобразование типов
```toc
minLevel: 2
```
## Что такое преобразование типов?

Преобразование типов (type conversion) — это процесс изменения типа данных из одного в другой. В Python это также называется **приведением типов** (type casting).

## Явное преобразование типов

### Строка в число

```python
# Строка в целое число
int("42")      # 42
int("123")     # 123

# Строка в число с плавающей точкой
float("3.14")  # 3.14
float("2.5")   # 2.5

# Обработка ошибок
try:
    int("abc")  # ValueError: invalid literal for int()
except ValueError:
    print("Не удалось преобразовать строку в число")
```

### Число в строку

```python
# Целое число в строку
str(42)        # "42"
str(123)       # "123"

# Число с плавающей точкой в строку
str(3.14)      # "3.14"
str(2.5)       # "2.5"

# Форматирование чисел в строках
f"Число: {42}"           # "Число: 42"
f"Пи: {3.14159:.2f}"     # "Пи: 3.14"
```

### Преобразование в булевы значения

```python
# Числа в булевы значения
bool(0)        # False
bool(1)        # True
bool(42)       # True
bool(-5)       # True (даже отрицательные числа!)

# Строки в булевы значения
bool("")       # False (пустая строка)
bool("hello")  # True (непустая строка)
bool("False")  # True (строка "False" непустая!)

# Коллекции в булевы значения
bool([])       # False (пустой список)
bool([1, 2])   # True (непустой список)
bool({})       # False (пустой словарь)
bool({1: 2})   # True (непустой словарь)

# None в булево значение
bool(None)     # False
```

### Другие преобразования

```python
# Число с плавающей точкой в целое
int(5.9)       # 5 (отбрасывает дробную часть)
int(5.1)       # 5
int(-3.7)      # -3 (отбрасывает дробную часть)

# Целое число в число с плавающей точкой
float(5)       # 5.0
float(42)      # 42.0

# Строка в список символов
list("hello")  # ['h', 'e', 'l', 'l', 'o']

# Список в строку
"".join(['h', 'e', 'l', 'l', 'o'])  # "hello"
```

## Неявное преобразование типов

Python автоматически преобразует типы в некоторых операциях:

```python
# Арифметические операции
5 + 3.14       # 8.14 (int + float = float)
10 / 2         # 5.0 (int / int = float)

# Сравнения
5 == 5.0       # True (int == float)
10 == 10.0     # True

# Логические операции
1 and "hello"  # "hello" (возвращает последнее истинное значение)
0 or "default" # "default" (возвращает первое истинное значение)
```

## Практические примеры

### Валидация и преобразование пользовательского ввода

```python
def get_user_age():
    """Получает и валидирует возраст пользователя"""
    while True:
        try:
            age_input = input("Введи свой возраст: ")
            age = int(age_input)
            
            if age < 0:
                print("Возраст не может быть отрицательным")
                continue
            elif age > 150:
                print("Возраст слишком большой")
                continue
            
            return age
            
        except ValueError:
            print("Пожалуйста, введи корректное число")

# Использование
age = get_user_age()
print(f"Твой возраст: {age} лет")
```

### Обработка данных из файлов

```python
def process_csv_line(line):
    """Обрабатывает строку CSV файла"""
    parts = line.strip().split(',')
    
    if len(parts) != 3:
        return None
    
    try:
        # Преобразуем каждую часть
        name = parts[0].strip()
        age = int(parts[1].strip())
        salary = float(parts[2].strip())
        
        return {
            'name': name,
            'age': age,
            'salary': salary
        }
    except ValueError as e:
        print(f"Ошибка преобразования: {e}")
        return None

# Тестируем
csv_lines = [
    "Иван,25,50000.5",
    "Петр,30,60000",
    "Анна,abc,70000",  # Ошибка в возрасте
    "Мария,28"         # Недостаточно данных
]

for line in csv_lines:
    result = process_csv_line(line)
    if result:
        print(f"Обработано: {result}")
    else:
        print(f"Ошибка в строке: {line}")
```

### Конфигурация с автоматическим преобразованием

```python
def parse_config_value(value, expected_type):
    """Парсит значение конфигурации в нужный тип"""
    if expected_type == bool:
        # Специальная обработка для булевых значений
        if isinstance(value, str):
            return value.lower() in ('true', '1', 'yes', 'on')
        return bool(value)
    
    try:
        return expected_type(value)
    except (ValueError, TypeError):
        print(f"Не удалось преобразовать '{value}' в {expected_type.__name__}")
        return None

# Тестируем
config_data = {
    'debug': 'true',
    'port': '8080',
    'timeout': '30.5',
    'max_connections': '100'
}

parsed_config = {}
for key, value in config_data.items():
    if key == 'debug':
        parsed_config[key] = parse_config_value(value, bool)
    elif key == 'port' or key == 'max_connections':
        parsed_config[key] = parse_config_value(value, int)
    elif key == 'timeout':
        parsed_config[key] = parse_config_value(value, float)
    else:
        parsed_config[key] = value

print(parsed_config)
```

### Безопасное преобразование со значениями по умолчанию

```python
def safe_int(value, default=0):
    """Безопасно преобразует значение в целое число"""
    try:
        return int(value)
    except (ValueError, TypeError):
        return default

def safe_float(value, default=0.0):
    """Безопасно преобразует значение в число с плавающей точкой"""
    try:
        return float(value)
    except (ValueError, TypeError):
        return default

def safe_bool(value, default=False):
    """Безопасно преобразует значение в булево"""
    if isinstance(value, str):
        return value.lower() in ('true', '1', 'yes', 'on')
    try:
        return bool(value)
    except (ValueError, TypeError):
        return default

# Тестируем
test_values = ["42", "3.14", "true", "abc", None, "", 0, 1]

for value in test_values:
    print(f"'{value}' -> int: {safe_int(value)}, float: {safe_float(value)}, bool: {safe_bool(value)}")
```

### Преобразование для API

```python
def prepare_api_data(user_data):
    """Подготавливает данные пользователя для отправки в API"""
    api_data = {}
    
    # Обязательные поля
    api_data['name'] = str(user_data.get('name', ''))
    api_data['email'] = str(user_data.get('email', ''))
    
    # Опциональные поля с преобразованием
    if 'age' in user_data:
        api_data['age'] = int(user_data['age'])
    
    if 'salary' in user_data:
        api_data['salary'] = float(user_data['salary'])
    
    if 'is_active' in user_data:
        api_data['is_active'] = bool(user_data['is_active'])
    
    # Преобразование списка в строку
    if 'tags' in user_data:
        api_data['tags'] = ','.join(str(tag) for tag in user_data['tags'])
    
    return api_data

# Тестируем
user_data = {
    'name': 'Иван',
    'email': 'ivan@example.com',
    'age': '25',  # Строка
    'salary': '50000.5',  # Строка
    'is_active': 'true',  # Строка
    'tags': ['python', 'developer', 42]  # Список с разными типами
}

api_data = prepare_api_data(user_data)
print(api_data)
```

## Обработка ошибок преобразования

### Try-except для преобразований

```python
def convert_with_error_handling(value, target_type, field_name=""):
    """Преобразует значение с обработкой ошибок"""
    try:
        return target_type(value)
    except ValueError as e:
        print(f"Ошибка преобразования {field_name}: {e}")
        return None
    except TypeError as e:
        print(f"Неподдерживаемый тип для {field_name}: {e}")
        return None

# Тестируем
values = ["42", "abc", "3.14", None, [1, 2, 3]]

for value in values:
    result = convert_with_error_handling(value, int, f"'{value}'")
    print(f"'{value}' -> int: {result}")
```

### Валидация перед преобразованием

```python
def validate_and_convert(value, target_type, field_name=""):
    """Валидирует и преобразует значение"""
    
    # Проверяем, что значение не None
    if value is None:
        print(f"Поле {field_name} не может быть пустым")
        return None
    
    # Проверяем тип
    if not isinstance(value, (str, int, float)):
        print(f"Поле {field_name} должно быть строкой или числом")
        return None
    
    # Преобразуем
    try:
        return target_type(value)
    except ValueError:
        print(f"Некорректное значение для {field_name}: '{value}'")
        return None

# Тестируем
test_data = [
    ("42", int, "возраст"),
    ("abc", int, "возраст"),
    ("3.14", float, "цена"),
    (None, str, "имя"),
    ([1, 2], str, "описание")
]

for value, target_type, field_name in test_data:
    result = validate_and_convert(value, target_type, field_name)
    print(f"{field_name}: '{value}' -> {result}")
```

## 💡 Лучшие практики

### ✅ Хорошие практики

```python
# Явно указывай типы при преобразовании
age = int(user_input)
price = float(price_string)

# Обрабатывай ошибки преобразования
try:
    number = int(input_string)
except ValueError:
    print("Некорректное число")

# Используй функции для повторяющихся преобразований
def safe_convert(value, converter, default=None):
    try:
        return converter(value)
    except (ValueError, TypeError):
        return default
```

### ❌ Плохие практики

```python
# Не полагайся на неявные преобразования
result = "5" + 3  # TypeError!

# Не игнорируй ошибки преобразования
number = int(user_input)  # Может вызвать ValueError

# Не используй eval() для преобразования
result = eval("5 + 3")  # Опасно!
```

### 🔧 Полезные паттерны

```python
# Паттерн "Преобразование с проверкой"
def convert_with_validation(value, target_type, validator=None):
    """Преобразует значение с дополнительной валидацией"""
    try:
        converted = target_type(value)
        if validator and not validator(converted):
            return None
        return converted
    except (ValueError, TypeError):
        return None

# Использование с валидатором
def is_positive(n):
    return n > 0

age = convert_with_validation("25", int, is_positive)
print(age)  # 25

negative_age = convert_with_validation("-5", int, is_positive)
print(negative_age)  # None

# Паттерн "Преобразование списка"
def convert_list(items, target_type):
    """Преобразует список элементов в другой тип"""
    result = []
    for item in items:
        try:
            result.append(target_type(item))
        except (ValueError, TypeError):
            result.append(None)
    return result

numbers = convert_list(["1", "2", "abc", "4"], int)
print(numbers)  # [1, 2, None, 4]
```

---

[[03_2_5_str_methods|← 🔤 Методы строк]] | [[03_3_1_lists|📋 Списки →]]
