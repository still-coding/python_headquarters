[[03_3_6_set_methods|← 🎯 Методы множеств]] | [[04_0_intro|🔄 Управляющие структуры →]]

---
# 🪆 Вложенные структуры данных
```toc
minLevel: 2
```

В реальных программах данные редко бывают «плоскими». Чаще всего мы работаем с **вложенными структурами** — списками внутри словарей, словарями внутри списков и так далее.

## Список списков (матрица)

```python
# Таблица оценок: строки — ученики, столбцы — предметы
grades = [
    [5, 4, 3],   # Иван
    [4, 5, 5],   # Петр
    [3, 3, 4],   # Анна
]

# Доступ по индексам: [строка][столбец]
grades[0]       # [5, 4, 3] — оценки Ивана
grades[0][1]    # 4 — вторая оценка Ивана
grades[2][0]    # 3 — первая оценка Анны
```

### Обход матрицы

```python
students = ["Иван", "Петр", "Анна"]
subjects = ["Математика", "Физика", "Химия"]

for i, student in enumerate(students):
    print(f"{student}: ", end="")
    for j, subject in enumerate(subjects):
        print(f"{subject}={grades[i][j]}", end="  ")
    print()

# Иван: Математика=5  Физика=4  Химия=3  
# Петр: Математика=4  Физика=5  Химия=5  
# Анна: Математика=3  Физика=3  Химия=4  
```

## Список словарей

Самый частый паттерн — список записей, где каждая запись — словарь:

```python
users = [
    {"name": "Иван", "age": 25, "city": "Москва"},
    {"name": "Анна", "age": 30, "city": "Питер"},
    {"name": "Петр", "age": 22, "city": "Москва"},
]

# Доступ к конкретной записи
users[0]["name"]   # "Иван"
users[1]["age"]    # 30

# Фильтрация
muscovites = [u for u in users if u["city"] == "Москва"]
# [{"name": "Иван", ...}, {"name": "Петр", ...}]

# Извлечение одного поля
names = [u["name"] for u in users]
# ["Иван", "Анна", "Петр"]

# Поиск
def find_user(users, name):
    for user in users:
        if user["name"] == name:
            return user
    return None

find_user(users, "Анна")  # {"name": "Анна", "age": 30, "city": "Питер"}
```

## Словарь со списками

```python
# Расписание по дням недели
schedule = {
    "Понедельник": ["Математика", "Физика", "Химия"],
    "Вторник": ["История", "Литература"],
    "Среда": ["Математика", "Информатика", "Физика"],
}

# Доступ
schedule["Понедельник"]      # ["Математика", "Физика", "Химия"]
schedule["Понедельник"][0]   # "Математика"

# Добавление предмета
schedule["Вторник"].append("Английский")

# Все предметы за неделю (без повторений)
all_subjects = set()
for day_subjects in schedule.values():
    all_subjects.update(day_subjects)
print(all_subjects)
# {'Математика', 'Физика', 'Химия', 'История', 'Литература', 'Информатика', 'Английский'}
```

## Словарь словарей

```python
# База пользователей по ID
database = {
    "user_001": {
        "name": "Иван",
        "email": "ivan@example.com",
        "settings": {"theme": "dark", "language": "ru"},
    },
    "user_002": {
        "name": "Анна",
        "email": "anna@example.com",
        "settings": {"theme": "light", "language": "en"},
    },
}

# Глубокий доступ
database["user_001"]["name"]                    # "Иван"
database["user_001"]["settings"]["theme"]       # "dark"

# Безопасный доступ (если ключа может не быть)
database.get("user_003", {}).get("name", "Неизвестный")  # "Неизвестный"
```

## Как обходить вложенные структуры

### Вложенные циклы

```python
# Словарь, где значения — списки
inventory = {
    "фрукты": ["яблоко", "банан", "вишня"],
    "овощи": ["морковь", "картофель"],
    "напитки": ["вода", "сок", "чай"],
}

for category, items in inventory.items():
    print(f"\n{category.upper()}:")
    for item in items:
        print(f"  - {item}")
```

### Рекурсивный обход (для произвольной глубины вложенности)

```python
def flatten(data, prefix=""):
    """Выводит все значения из вложенной структуры"""
    if isinstance(data, dict):
        for key, value in data.items():
            flatten(value, f"{prefix}{key}.")
    elif isinstance(data, list):
        for i, item in enumerate(data):
            flatten(item, f"{prefix}[{i}].")
    else:
        print(f"{prefix[:-1]} = {data}")

config = {
    "server": {
        "host": "localhost",
        "port": 8080,
        "debug": True,
    },
    "database": {
        "name": "mydb",
        "tables": ["users", "orders"],
    },
}

flatten(config)
# server.host = localhost
# server.port = 8080
# server.debug = True
# database.name = mydb
# database.tables.[0] = users
# database.tables.[1] = orders
```

## ⚠️ Ловушка: изменяемые объекты во вложенных структурах

Помни про [[03_0_names_and_binding|модель имён и связывания]] — при вложенных структурах это особенно важно:

```python
# ❌ Общая ссылка внутри списка
row = [0, 0, 0]
matrix = [row, row, row]   # Три ссылки на ОДИН список!

matrix[0][1] = 5
print(matrix)
# [[0, 5, 0], [0, 5, 0], [0, 5, 0]]  — изменились все строки!

# ✅ Независимые копии
matrix = [[0, 0, 0] for _ in range(3)]
matrix[0][1] = 5
print(matrix)
# [[0, 5, 0], [0, 0, 0], [0, 0, 0]]  — только первая строка
```

### Поверхностная vs глубокая копия

```python
import copy

original = [[1, 2], [3, 4]]

# Поверхностная копия — внешний список новый, но внутренние — те же объекты
shallow = original.copy()
shallow[0].append(99)
print(original)  # [[1, 2, 99], [3, 4]] — оригинал изменился!

# Глубокая копия — всё новое, на всех уровнях вложенности
original = [[1, 2], [3, 4]]
deep = copy.deepcopy(original)
deep[0].append(99)
print(original)  # [[1, 2], [3, 4]] — оригинал не тронут
```

## Практические примеры

### Подсчёт частоты слов

```python
text = "кот собака кот рыба собака кот"
words = text.split()

frequency = {}
for word in words:
    frequency[word] = frequency.get(word, 0) + 1

print(frequency)  # {'кот': 3, 'собака': 2, 'рыба': 1}
```

### Группировка данных

```python
students = [
    {"name": "Иван", "group": "A"},
    {"name": "Анна", "group": "B"},
    {"name": "Петр", "group": "A"},
    {"name": "Мария", "group": "B"},
    {"name": "Олег", "group": "A"},
]

# Группировка по группам
groups = {}
for student in students:
    group = student["group"]
    if group not in groups:
        groups[group] = []
    groups[group].append(student["name"])

print(groups)
# {'A': ['Иван', 'Петр', 'Олег'], 'B': ['Анна', 'Мария']}
```

### Обработка JSON-подобных данных

```python
# Типичный ответ API
api_response = {
    "status": "ok",
    "data": {
        "users": [
            {"id": 1, "name": "Иван", "posts": [101, 102]},
            {"id": 2, "name": "Анна", "posts": [103]},
        ],
        "total": 2,
    },
}

# Извлечение данных
for user in api_response["data"]["users"]:
    post_count = len(user["posts"])
    print(f"{user['name']}: {post_count} постов")
# Иван: 2 постов
# Анна: 1 постов
```

## 🔎 Проверка на ходу

```python
# Что выведет?
>>> d = {"a": [1, 2], "b": [3, 4]}
>>> d["a"].append(5)
>>> d
{'a': [1, 2, 5], 'b': [3, 4]}

# А здесь?
>>> m = [[0] * 3] * 3
>>> m[0][0] = 1
>>> m
[[1, 0, 0], [1, 0, 0], [1, 0, 0]]   # Все строки — один объект!

# Как правильно?
>>> m = [[0] * 3 for _ in range(3)]
>>> m[0][0] = 1
>>> m
[[1, 0, 0], [0, 0, 0], [0, 0, 0]]   # Теперь каждая строка независима
```

## 💡 Главное

```python
# Список словарей — самый частый паттерн для «таблицы данных»
users = [{"name": "Иван", "age": 25}, {"name": "Анна", "age": 30}]

# Доступ через цепочку: структура[ключ][индекс][ключ]...
data["users"][0]["name"]

# Используй .get() для безопасного доступа к словарям
data.get("missing_key", default_value)

# Остерегайся общих ссылок во вложенных структурах
# [[0]*3] * 3 — ТРИ ССЫЛКИ на один список
# [[0]*3 for _ in range(3)] — ТРИ НЕЗАВИСИМЫХ списка

# Для глубокого копирования — copy.deepcopy()
```

---

[[03_3_6_set_methods|← 🎯 Методы множеств]] | [[04_0_intro|🔄 Управляющие структуры →]]
