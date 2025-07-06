# Методы множеств в Python

## Что такое методы множеств?

Методы множеств - это встроенные функции для работы с множествами. Множества **изменяемы** (mutable), содержат только **уникальные элементы** и **не имеют порядка**. Методы множеств часто изменяют исходное множество.

```python
# Синтаксис: множество.метод()
colors = {"красный", "синий"}
colors.add("зеленый")  # Изменяет исходное множество
print(colors)  # {"красный", "синий", "зеленый"}
```

---

## 1. Методы добавления элементов

### `add()` - Добавление одного элемента

```python
set.add(element: Any) -> None
```
- **`element`** - элемент для добавления

```python
fruits = {"яблоко", "банан"}
fruits.add("вишня")
print(fruits)  # {"яблоко", "банан", "вишня"}

# Если элемент уже есть, ничего не происходит
fruits.add("яблоко")
print(fruits)  # {"яблоко", "банан", "вишня"} - не изменилось

# Можно добавлять разные типы данных
mixed_set = {1, "hello"}
mixed_set.add(3.14)
mixed_set.add(True)  # True == 1, поэтому не добавится!
print(mixed_set)  # {1, 3.14, "hello"}

# Практический пример: сбор уникальных значений
seen_ids = set()
user_actions = [101, 102, 101, 103, 102, 104]

for user_id in user_actions:
    seen_ids.add(user_id)

print(f"Уникальные пользователи: {seen_ids}")  # {101, 102, 103, 104}
```

### `update()` - Добавление нескольких элементов из итерируемого объекта

```python
set.update(*iterables: Iterable) -> None
```
- **`*iterables`** - один или несколько итерируемых объектов

```python
fruits = {"яблоко", "банан"}

# Добавление из списка
fruits.update(["вишня", "груша"])
print(fruits)  # {"яблоко", "банан", "вишня", "груша"}

# Добавление из строки (каждый символ отдельно!)
letters = {"a", "b"}
letters.update("cde")
print(letters)  # {"a", "b", "c", "d", "e"}

# Добавление из нескольких источников
numbers = {1, 2}
numbers.update([3, 4], (5, 6), {7, 8})
print(numbers)  # {1, 2, 3, 4, 5, 6, 7, 8}

# Дубликаты игнорируются
original = {1, 2, 3}
original.update([2, 3, 4, 5])
print(original)  # {1, 2, 3, 4, 5}

# Практический пример: объединение тегов
user_tags = {"python", "programming"}
new_tags = ["web", "django", "python"]  # "python" дублируется
user_tags.update(new_tags)
print(user_tags)  # {"python", "programming", "web", "django"}
```

---

## 2. Методы удаления элементов

### `remove()` - Удаление элемента (с ошибкой, если элемента нет)

```python
set.remove(element: Any) -> None
```
- **`element`** - элемент для удаления

```python
fruits = {"яблоко", "банан", "вишня"}

fruits.remove("банан")
print(fruits)  # {"яблоко", "вишня"}

# Если элемента нет, будет KeyError!
try:
    fruits.remove("лимон")
except KeyError:
    print("Элемент 'лимон' не найден!")

# Практический пример: удаление обработанных задач
pending_tasks = {"task1", "task2", "task3"}
completed_task = "task1"

if completed_task in pending_tasks:
    pending_tasks.remove(completed_task)
    print(f"Задача {completed_task} выполнена")

print(f"Оставшиеся задачи: {pending_tasks}")
```

### `discard()` - Удаление элемента (без ошибки, если элемента нет)

```python
set.discard(element: Any) -> None
```
- **`element`** - элемент для удаления

```python
fruits = {"яблоко", "банан", "вишня"}

fruits.discard("банан")
print(fruits)  # {"яблоко", "вишня"}

# Если элемента нет, ничего не происходит
fruits.discard("лимон")  # Никакой ошибки!
print(fruits)  # {"яблоко", "вишня"} - не изменилось

# Практический пример: безопасное удаление
user_permissions = {"read", "write", "admin"}

def revoke_permission(permissions, permission):
    permissions.discard(permission)  # Безопасно, даже если прав нет
    print(f"Право '{permission}' отозвано")

revoke_permission(user_permissions, "admin")
revoke_permission(user_permissions, "delete")  # Такого права не было
print(f"Текущие права: {user_permissions}")
```

### `pop()` - Удаление и возврат произвольного элемента

```python
set.pop() -> Any
```

```python
colors = {"красный", "синий", "зеленый"}

# Удаляет и возвращает случайный элемент
removed_color = colors.pop()
print(f"Удален цвет: {removed_color}")
print(f"Остались: {colors}")

# Порядок удаления непредсказуем!
numbers = {1, 2, 3, 4, 5}
while numbers:
    removed = numbers.pop()
    print(f"Удален: {removed}, осталось: {numbers}")

# Если множество пустое, будет KeyError
empty_set = set()
try:
    empty_set.pop()
except KeyError:
    print("Множество пустое!")

# Практический пример: обработка очереди задач
tasks = {"task_a", "task_b", "task_c"}

print("Обработка задач:")
while tasks:
    current_task = tasks.pop()
    print(f"Выполняется: {current_task}")

print("Все задачи выполнены!")
```

### `clear()` - Удаление всех элементов

```python
set.clear() -> None
```

```python
colors = {"красный", "синий", "зеленый"}

print(f"До очистки: {colors}")
colors.clear()
print(f"После очистки: {colors}")  # set()

# Практический пример: сброс состояния
active_users = {"user1", "user2", "user3"}

def logout_all():
    active_users.clear()
    print("Все пользователи вышли из системы")

print(f"Активные пользователи: {active_users}")
logout_all()
print(f"Активные пользователи: {active_users}")
```

---

## 3. Операции с множествами (теоретико-множественные)

### `union()` / `|` - Объединение множеств

```python
set.union(*others: Iterable) -> set
set | other -> set
```
- **`*others`** - другие множества или итерируемые объекты

```python
set1 = {1, 2, 3}
set2 = {3, 4, 5}
set3 = {5, 6, 7}

# Методом
union_result = set1.union(set2)
print(union_result)  # {1, 2, 3, 4, 5}

# Оператором
union_result2 = set1 | set2
print(union_result2)  # {1, 2, 3, 4, 5}

# Объединение нескольких множеств
all_union = set1.union(set2, set3)
print(all_union)  # {1, 2, 3, 4, 5, 6, 7}

# Объединение с другими итерируемыми объектами
result = set1.union([4, 5], "67")
print(result)  # {1, 2, 3, 4, 5, '6', '7'}

# Практический пример: объединение групп пользователей
admins = {"alice", "bob"}
moderators = {"bob", "charlie"}
editors = {"charlie", "david"}

all_staff = admins.union(moderators, editors)
print(f"Все сотрудники: {all_staff}")  # {"alice", "bob", "charlie", "david"}
```

### `intersection()` / `&` - Пересечение множеств

```python
set.intersection(*others: Iterable) -> set
set & other -> set
```
- **`*others`** - другие множества или итерируемые объекты

```python
set1 = {1, 2, 3, 4}
set2 = {3, 4, 5, 6}
set3 = {4, 5, 6, 7}

# Методом
intersection_result = set1.intersection(set2)
print(intersection_result)  # {3, 4}

# Оператором
intersection_result2 = set1 & set2
print(intersection_result2)  # {3, 4}

# Пересечение нескольких множеств
common_all = set1.intersection(set2, set3)
print(common_all)  # {4}

# Пересечение с другими итерируемыми объектами
result = set1.intersection([2, 3, 8], "234")
print(result)  # {2, 3}

# Практический пример: поиск общих интересов
alice_interests = {"python", "music", "travel", "books"}
bob_interests = {"java", "music", "travel", "sports"}
charlie_interests = {"python", "travel", "photography"}

# Общие интересы у всех троих
common_interests = alice_interests.intersection(bob_interests, charlie_interests)
print(f"Общие интересы: {common_interests}")  # {"travel"}

# Общие интересы у Алисы и Боба
alice_bob_common = alice_interests & bob_interests
print(f"Общие у Алисы и Боба: {alice_bob_common}")  # {"music", "travel"}
```

### `difference()` / `-` - Разность множеств

```python
set.difference(*others: Iterable) -> set
set - other -> set
```
- **`*others`** - другие множества или итерируемые объекты

```python
set1 = {1, 2, 3, 4, 5}
set2 = {3, 4, 5, 6, 7}

# Методом
difference_result = set1.difference(set2)
print(difference_result)  # {1, 2} - элементы из set1, которых нет в set2

# Оператором
difference_result2 = set1 - set2
print(difference_result2)  # {1, 2}

# Обратная разность
reverse_diff = set2 - set1
print(reverse_diff)  # {6, 7}

# Разность с несколькими множествами
set3 = {1, 8, 9}
multiple_diff = set1.difference(set2, set3)
print(multiple_diff)  # {2}

# Практический пример: поиск уникальных элементов
current_users = {"alice", "bob", "charlie", "david"}
logged_out_users = {"bob", "david"}
premium_users = {"alice", "eve"}

# Пользователи онлайн (исключаем вышедших)
online_users = current_users - logged_out_users
print(f"Онлайн: {online_users}")  # {"alice", "charlie"}

# Обычные пользователи (исключаем премиум)
regular_users = current_users - premium_users
print(f"Обычные пользователи: {regular_users}")  # {"bob", "charlie", "david"}
```

### `symmetric_difference()` / `^` - Симметричная разность

```python
set.symmetric_difference(other: Iterable) -> set
set ^ other -> set
```
- **`other`** - другое множество или итерируемый объект

```python
set1 = {1, 2, 3, 4}
set2 = {3, 4, 5, 6}

# Методом - элементы, которые есть в одном множестве, но не в обоих
sym_diff = set1.symmetric_difference(set2)
print(sym_diff)  # {1, 2, 5, 6}

# Оператором
sym_diff2 = set1 ^ set2
print(sym_diff2)  # {1, 2, 5, 6}

# Это эквивалентно (A - B) | (B - A)
manual_sym_diff = (set1 - set2) | (set2 - set1)
print(manual_sym_diff)  # {1, 2, 5, 6}

# Практический пример: поиск изменений
old_permissions = {"read", "write", "execute"}
new_permissions = {"read", "admin", "delete"}

# Какие права изменились (добавились или убрались)
changed_permissions = old_permissions ^ new_permissions
print(f"Изменившиеся права: {changed_permissions}")  # {"write", "execute", "admin", "delete"}

# Что добавилось
added = new_permissions - old_permissions
print(f"Добавленные права: {added}")  # {"admin", "delete"}

# Что убралось
removed = old_permissions - new_permissions
print(f"Удаленные права: {removed}")  # {"write", "execute"}
```

---

## 4. Методы изменения множества

### `update()` (и его варианты с операциями)

```python
# update() - уже рассмотрен выше

# intersection_update() - изменяет множество, оставляя только пересечение
set.intersection_update(*others: Iterable) -> None
set &= other

# difference_update() - удаляет элементы, которые есть в других множествах
set.difference_update(*others: Iterable) -> None
set -= other

# symmetric_difference_update() - оставляет только симметричную разность
set.symmetric_difference_update(other: Iterable) -> None
set ^= other
```

```python
# intersection_update()
set1 = {1, 2, 3, 4, 5}
set2 = {3, 4, 5, 6, 7}

set1.intersection_update(set2)
print(set1)  # {3, 4, 5} - изменился исходный set1

# Альтернативно:
set1 = {1, 2, 3, 4, 5}
set1 &= set2
print(set1)  # {3, 4, 5}

# difference_update()
users = {"alice", "bob", "charlie", "david"}
banned_users = {"bob", "charlie"}

users.difference_update(banned_users)
print(users)  # {"alice", "david"}

# Альтернативно:
users = {"alice", "bob", "charlie", "david"}
users -= banned_users
print(users)  # {"alice", "david"}

# symmetric_difference_update()
old_tags = {"python", "web", "api"}
new_tags = {"python", "mobile", "database"}

old_tags.symmetric_difference_update(new_tags)
print(old_tags)  # {"web", "api", "mobile", "database"}

# Альтернативно:
old_tags = {"python", "web", "api"}
old_tags ^= new_tags
print(old_tags)  # {"web", "api", "mobile", "database"}
```

---

## 5. Методы проверки отношений между множествами

### `issubset()` / `<=` - Проверка подмножества

```python
set.issubset(other: Iterable) -> bool
set <= other -> bool
set < other -> bool  # строгое подмножество
```
- **`other`** - другое множество или итерируемый объект

```python
small_set = {1, 2}
large_set = {1, 2, 3, 4, 5}
equal_set = {1, 2}

# Проверка подмножества
print(small_set.issubset(large_set))  # True
print(small_set <= large_set)  # True

# Проверка равенства множеств
print(small_set <= equal_set)  # True (равные множества)
print(small_set < equal_set)   # False (строгое подмножество)

# Практический пример: проверка прав доступа
required_permissions = {"read", "write"}
user_permissions = {"read", "write", "admin"}

if required_permissions.issubset(user_permissions):
    print("Доступ разрешен")
else:
    missing = required_permissions - user_permissions
    print(f"Не хватает прав: {missing}")
```

### `issuperset()` / `>=` - Проверка надмножества

```python
set.issuperset(other: Iterable) -> bool
set >= other -> bool
set > other -> bool  # строгое надмножество
```
- **`other`** - другое множество или итерируемый объект

```python
large_set = {1, 2, 3, 4, 5}
small_set = {1, 2}
equal_set = {1, 2, 3, 4, 5}

# Проверка надмножества
print(large_set.issuperset(small_set))  # True
print(large_set >= small_set)  # True

# Проверка равенства множеств
print(large_set >= equal_set)  # True (равные множества)
print(large_set > equal_set)   # False (строгое надмножество)

# Практический пример: проверка полноты данных
available_data = {"name", "email", "age", "phone", "address"}
required_fields = {"name", "email"}
optional_fields = {"age", "phone"}

if available_data.issuperset(required_fields):
    print("Все обязательные поля присутствуют")
    
    if available_data >= (required_fields | optional_fields):
        print("Все поля заполнены")
```

### `isdisjoint()` - Проверка на отсутствие общих элементов

```python
set.isdisjoint(other: Iterable) -> bool
```
- **`other`** - другое множество или итерируемый объект

```python
set1 = {1, 2, 3}
set2 = {4, 5, 6}
set3 = {3, 4, 5}

print(set1.isdisjoint(set2))  # True (нет общих элементов)
print(set1.isdisjoint(set3))  # False (есть общий элемент 3)

# Практический пример: проверка конфликтов расписания
morning_classes = {"math", "physics"}
afternoon_classes = {"chemistry", "biology"}
evening_classes = {"math", "english"}  # конфликт с утренними!

if morning_classes.isdisjoint(afternoon_classes):
    print("Утренние и дневные занятия не пересекаются")

if not morning_classes.isdisjoint(evening_classes):
    conflicts = morning_classes & evening_classes
    print(f"Конфликт расписания: {conflicts}")
```

---

## 6. Методы копирования

### `copy()` - Создание поверхностной копии

```python
set.copy() -> set
```

```python
original = {1, 2, 3, 4, 5}

# Поверхностная копия
copy_set = original.copy()
copy_set.add(6)

print(f"Оригинал: {original}")  # {1, 2, 3, 4, 5}
print(f"Копия: {copy_set}")  # {1, 2, 3, 4, 5, 6}

# Альтернативные способы копирования
copy2 = set(original)
copy3 = original | set()  # объединение с пустым множеством

# Для множеств с изменяемыми элементами нужно глубокое копирование
# Но множества могут содержать только неизменяемые элементы!

# Практический пример: создание резервной копии состояния
active_connections = {"user1", "user2", "user3"}

def backup_connections():
    return active_connections.copy()

def restore_connections(backup):
    active_connections.clear()
    active_connections.update(backup)

# Создаем бэкап
backup = backup_connections()

# Изменяем исходное множество
active_connections.clear()
active_connections.add("user4")

print(f"Текущие соединения: {active_connections}")  # {"user4"}
print(f"Бэкап: {backup}")  # {"user1", "user2", "user3"}

# Восстанавливаем
restore_connections(backup)
print(f"Восстановленные соединения: {active_connections}")  # {"user1", "user2", "user3"}
```

---

## 7. Практические примеры

### Удаление дубликатов из списка

```python
# Простой способ
numbers_with_duplicates = [1, 2, 3, 2, 4, 1, 5, 3]
unique_numbers = list(set(numbers_with_duplicates))
print(unique_numbers)  # [1, 2, 3, 4, 5] (порядок может измениться!)

# Сохранение порядка (Python 3.7+)
def remove_duplicates_ordered(lst):
    seen = set()
    result = []
    for item in lst:
        if item not in seen:
            seen.add(item)
            result.append(item)
    return result

ordered_unique = remove_duplicates_ordered(numbers_with_duplicates)
print(ordered_unique)  # [1, 2, 3, 4, 5] (порядок сохранен)

# Альтернативный способ с dict (Python 3.7+)
ordered_unique2 = list(dict.fromkeys(numbers_with_duplicates))
print(ordered_unique2)  # [1, 2, 3, 4, 5]
```

### Анализ текста

```python
def analyze_text(text1, text2):
    """Анализ двух текстов на уровне слов"""
    
    # Преобразуем в множества слов
    words1 = set(text1.lower().split())
    words2 = set(text2.lower().split())
    
    # Общие слова
    common_words = words1 & words2
    
    # Уникальные слова в каждом тексте
    unique_to_text1 = words1 - words2
    unique_to_text2 = words2 - words1
    
    # Все уникальные слова
    all_words = words1 | words2
    
    return {
        "common": common_words,
        "unique_to_first": unique_to_text1,
        "unique_to_second": unique_to_text2,
        "total_unique": len(all_words),
        "similarity": len(common_words) / len(all_words) if all_words else 0
    }

text1 = "Python это мощный язык программирования"
text2 = "Java это популярный язык программирования"

analysis = analyze_text(text1, text2)
print(f"Общие слова: {analysis['common']}")
print(f"Уникальные в первом: {analysis['unique_to_first']}")
print(f"Уникальные во втором: {analysis['unique_to_second']}")
print(f"Коэффициент схожести: {analysis['similarity']:.2f}")
```

### Работа с тегами и категориями

```python
def manage_content_tags():
    """Система управления тегами контента"""
    
    # Предустановленные категории
    tech_tags = {"python", "javascript", "web", "api", "database"}
    design_tags = {"ui", "ux", "graphics", "colors", "typography"}
    business_tags = {"marketing", "sales", "strategy", "analytics"}
    
    # Статьи с тегами
    articles = {
        "article1": {"python", "api", "web"},
        "article2": {"ui", "ux", "colors"},
        "article3": {"python", "database", "analytics"},
        "article4": {"javascript", "web", "ui"},
        "article5": {"marketing", "analytics", "strategy"}
    }
    
    def categorize_article(tags):
        """Определить категории статьи по тегам"""
        categories = []
        if tags & tech_tags:
            categories.append("tech")
        if tags & design_tags:
            categories.append("design")
        if tags & business_tags:
            categories.append("business")
        return categories
    
    def find_related_articles(article_id, min_common_tags=1):
        """Найти связанные статьи по тегам"""
        if article_id not in articles:
            return []
        
        current_tags = articles[article_id]
        related = []
        
        for other_id, other_tags in articles.items():
            if other_id != article_id:
                common_tags = current_tags & other_tags
                if len(common_tags) >= min_common_tags:
                    related.append((other_id, common_tags))
        
        return related
    
    def get_trending_tags(min_articles=2):
        """Найти популярные теги"""
        tag_count = {}
        
        for tags in articles.values():
            for tag in tags:
                tag_count[tag] = tag_count.get(tag, 0) + 1
        
        return {tag for tag, count in tag_count.items() if count >= min_articles}
    
    # Анализ статей
    for article_id, tags in articles.items():
        categories = categorize_article(tags)
        print(f"{article_id}: {tags} -> категории: {categories}")
    
    print("\nСвязанные статьи:")
    related = find_related_articles("article1")
    for article_id, common_tags in related:
        print(f"  {article_id}: общие теги {common_tags}")
    
    print(f"\nПопулярные теги: {get_trending_tags()}")
    
    # Рекомендации для новой статьи
    new_article_tags = {"python", "machine-learning", "analytics"}
    print(f"\nНовая статья с тегами: {new_article_tags}")
    print(f"Категории: {categorize_article(new_article_tags)}")
    
    # Найти существующие теги для предложений
    all_existing_tags = set()
    for tags in articles.values():
        all_existing_tags.update(tags)
    
    suggested_tags = all_existing_tags & new_article_tags
    print(f"Предлагаемые существующие теги: {suggested_tags}")

# Запуск примера
manage_content_tags()
```

---

## 8. Полезные операции с множествами

### Проверка и подсчет

```python
# Базовые операции
numbers = {1, 2, 3, 4, 5}

# Размер множества
print(len(numbers))  # 5

# Проверка пустоты
empty_set = set()
if not empty_set:  # Пустое множество равно False
    print("Множество пустое")

# Проверка принадлежности
if 3 in numbers:
    print("3 присутствует в множестве")

if 10 not in numbers:
    print("10 отсутствует в множестве")

# Практический пример: валидация данных
def validate_user_input(user_data, required_fields, optional_fields=None):
    """Валидация пользовательского ввода"""
    if optional_fields is None:
        optional_fields = set()
    
    provided_fields = set(user_data.keys())
    required_set = set(required_fields)
    optional_set = set(optional_fields)
    
    # Проверка обязательных полей
    missing_required = required_set - provided_fields
    if missing_required:
        return False, f"Отсутствуют обязательные поля: {missing_required}"
    
    # Проверка на неизвестные поля
    allowed_fields = required_set | optional_set
    unknown_fields = provided_fields - allowed_fields
    if unknown_fields:
        return False, f"Неизвестные поля: {unknown_fields}"
    
    return True, "Валидация пройдена"

# Тест валидации
user_data = {"name": "Иван", "email": "ivan@example.com", "age": 25}
required = ["name", "email"]
optional = ["age", "phone"]

is_valid, message = validate_user_input(user_data, required, optional)
print(f"Результат валидации: {message}")
```

### Преобразования и фильтрация

```python
# Преобразование множеств
numbers = {1, 2, 3, 4, 5}

# Фильтрация с помощью set comprehension
even_numbers = {x for x in numbers if x % 2 == 0}
print(even_numbers)  # {2, 4}

# Преобразование элементов
squared_numbers = {x**2 for x in numbers}
print(squared_numbers)  # {1, 4, 9, 16, 25}

# Условные преобразования
processed = {x*2 if x % 2 == 0 else x for x in numbers}
print(processed)  # {1, 3, 4, 5, 8}

# Фильтрация с помощью filter()
large_numbers = set(filter(lambda x: x > 3, numbers))
print(large_numbers)  # {4, 5}

# Преобразование с помощью map()
string_numbers = set(map(str, numbers))
print(string_numbers)  # {'1', '2', '3', '4', '5'}

# Практический пример: обработка списка файлов
files = {
    "document.pdf", "image.jpg", "script.py", 
    "data.csv", "photo.png", "code.py", "text.txt"
}

# Разделение по типам файлов
image_extensions = {".jpg", ".png", ".gif", ".bmp"}
code_extensions = {".py", ".js", ".html", ".css"}
document_extensions = {".pdf", ".doc", ".txt", ".csv"}

def get_file_extension(filename):
    return "." + filename.split(".")[-1] if "." in filename else ""

# Группировка файлов
image_files = {f for f in files if get_file_extension(f) in image_extensions}
code_files = {f for f in files if get_file_extension(f) in code_extensions}
document_files = {f for f in files if get_file_extension(f) in document_extensions}

print(f"Изображения: {image_files}")
print(f"Код: {code_files}")
print(f"Документы: {document_files}")

# Файлы неизвестного типа
all_known = image_files | code_files | document_files
unknown_files = files - all_known
print(f"Неизвестный тип: {unknown_files}")
```

---

## 9. Множества и производительность

### Оптимизация поиска

```python
import time

# Сравнение производительности поиска
large_list = list(range(100000))
large_set = set(large_list)

search_value = 99999

# Поиск в списке (медленно)
start_time = time.time()
result = search_value in large_list
list_time = time.time() - start_time

# Поиск в множестве (быстро)
start_time = time.time()
result = search_value in large_set
set_time = time.time() - start_time

print(f"Поиск в списке: {list_time:.6f} секунд")
print(f"Поиск в множестве: {set_time:.6f} секунд")
print(f"Множество быстрее в {list_time/set_time:.1f} раз")
```

### Кэширование и мемоизация

```python
class MemoizedFunction:
    """Кэширование результатов функции с использованием множеств"""
    
    def __init__(self, func):
        self.func = func
        self.cache = {}
        self.cache_hits = set()
        self.cache_misses = set()
    
    def __call__(self, *args):
        # Конвертируем аргументы в hashable тип
        key = args
        
        if key in self.cache:
            self.cache_hits.add(key)
            return self.cache[key]
        
        # Вычисляем значение
        result = self.func(*args)
        self.cache[key] = result
        self.cache_misses.add(key)
        
        return result
    
    def get_stats(self):
        total_calls = len(self.cache_hits) + len(self.cache_misses)
        hit_rate = len(self.cache_hits) / total_calls if total_calls > 0 else 0
        
        return {
            "total_calls": total_calls,
            "cache_hits": len(self.cache_hits),
            "cache_misses": len(self.cache_misses),
            "hit_rate": hit_rate
        }

@MemoizedFunction
def fibonacci(n):
    """Вычисление чисел Фибоначчи с мемоизацией"""
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

# Тестирование
print("Вычисление Фибоначчи:")
for i in [10, 15, 10, 20, 15, 10]:
    result = fibonacci(i)
    print(f"fibonacci({i}) = {result}")

stats = fibonacci.get_stats()
print(f"\nСтатистика кэша:")
print(f"Всего вызовов: {stats['total_calls']}")
print(f"Попаданий в кэш: {stats['cache_hits']}")
print(f"Промахов: {stats['cache_misses']}")
print(f"Процент попаданий: {stats['hit_rate']:.2%}")
```

---

## Заключение

Методы множеств в Python предоставляют мощные инструменты для работы с уникальными элементами и выполнения теоретико-множественных операций. Помните:

1. **Множества содержат только уникальные элементы** - дубликаты автоматически удаляются
2. **Элементы должны быть неизменяемыми** (hashable) - строки, числа, кортежи можно, списки нельзя
3. **Порядок элементов не гарантирован** - множества неупорядочены
4. **Поиск в множествах очень быстрый** - O(1) в среднем случае
5. **Операции над множествами интуитивны** - объединение, пересечение, разность
6. **Используйте для удаления дубликатов**, быстрого поиска, анализа пересечений данных

Этот справочник поможет вам эффективно работать с множествами и использовать их для решения практических задач!