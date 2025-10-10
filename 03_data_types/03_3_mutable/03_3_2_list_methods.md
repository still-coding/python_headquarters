[[03_3_1_lists|← 📋 Списки]] | [[03_3_3_tuples|📦 Кортежи →]]

---
# 📋Методы списков
```toc
minLevel: 2
```
## Что такое методы списков?

Методы списков - это встроенные функции для работы со списками. В отличие от строк, списки **изменяемы** (mutable), поэтому многие методы изменяют сам список, а не создают новый.

```python
# Синтаксис: список.метод()
fruits = ["яблоко", "банан"]
fruits.append("вишня")  # Изменяет исходный список
print(fruits)  # ["яблоко", "банан", "вишня"]
```

---

## 1. Методы добавления элементов

### `append()` - Добавление одного элемента в конец

```python
list.append(object: Any) -> None
```
- **`object`** - элемент для добавления

```python
fruits = ["яблоко", "банан"]
fruits.append("вишня")
print(fruits)  # ["яблоко", "банан", "вишня"]

# Можно добавить любой объект
numbers = [1, 2, 3]
numbers.append([4, 5])  # Добавляет список как один элемент
print(numbers)  # [1, 2, 3, [4, 5]]

# Практический пример: сбор данных
grades = []
grades.append(85)
grades.append(92)
grades.append(78)
print(f"Оценки: {grades}")  # Оценки: [85, 92, 78]
```

### `insert()` - Вставка элемента на определенную позицию

```python
list.insert(index: int, object: Any) -> None
```
- **`index`** - позиция для вставки
- **`object`** - элемент для вставки

```python
fruits = ["яблоко", "вишня"]
fruits.insert(1, "банан")  # Вставляем на позицию 1
print(fruits)  # ["яблоко", "банан", "вишня"]

# Вставка в начало
numbers = [2, 3, 4]
numbers.insert(0, 1)
print(numbers)  # [1, 2, 3, 4]

# Вставка в конец (аналогично append)
numbers.insert(len(numbers), 5)
print(numbers)  # [1, 2, 3, 4, 5]

# Если индекс больше длины, вставляет в конец
numbers.insert(100, 6)
print(numbers)  # [1, 2, 3, 4, 5, 6]
```

### `extend()` - Расширение списка элементами из другого итерируемого объекта

```python
list.extend(iterable: Iterable) -> None
```
- **`iterable`** - итерируемый объект (список, строка, кортеж и т.д.)

```python
fruits = ["яблоко", "банан"]
fruits.extend(["вишня", "груша"])
print(fruits)  # ["яблоко", "банан", "вишня", "груша"]

# Разница между append и extend
list1 = [1, 2, 3]
list1.append([4, 5])
print(list1)  # [1, 2, 3, [4, 5]] - список как один элемент

list2 = [1, 2, 3]
list2.extend([4, 5])
print(list2)  # [1, 2, 3, 4, 5] - элементы добавлены по отдельности

# Можно расширить строкой
letters = ['a', 'b']
letters.extend("cde")
print(letters)  # ['a', 'b', 'c', 'd', 'e']

# Практический пример: объединение списков
all_numbers = [1, 2, 3]
even_numbers = [4, 6, 8]
odd_numbers = [5, 7, 9]
all_numbers.extend(even_numbers)
all_numbers.extend(odd_numbers)
print(all_numbers)  # [1, 2, 3, 4, 6, 8, 5, 7, 9]
```

---

## 2. Методы удаления элементов

### `remove()` - Удаление первого вхождения элемента

```python
list.remove(value: Any) -> None
```
- **`value`** - значение для удаления

```python
fruits = ["яблоко", "банан", "вишня", "банан"]
fruits.remove("банан")  # Удаляет первое вхождение
print(fruits)  # ["яблоко", "вишня", "банан"]

# Ошибка, если элемента нет
try:
    fruits.remove("лимон")  # ValueError: list.remove(x): x not in list
except ValueError as e:
    print(f"Ошибка: {e}")

# Безопасное удаление
fruits = ["яблоко", "банан", "вишня"]
item_to_remove = "лимон"
if item_to_remove in fruits:
    fruits.remove(item_to_remove)
    print(f"Удален {item_to_remove}")
else:
    print(f"{item_to_remove} не найден в списке")
```

### `pop()` - Удаление и возвращение элемента по индексу

```python
list.pop(index: int = -1) -> object
```
- **`index`** - индекс элемента для удаления (по умолчанию -1, последний элемент)

```python
fruits = ["яблоко", "банан", "вишня"]

# Удаление последнего элемента
last_fruit = fruits.pop()
print(f"Удален: {last_fruit}")  # Удален: вишня
print(fruits)  # ["яблоко", "банан"]

# Удаление по индексу
fruits = ["яблоко", "банан", "вишня", "груша"]
second_fruit = fruits.pop(1)
print(f"Удален: {second_fruit}")  # Удален: банан
print(fruits)  # ["яблоко", "вишня", "груша"]

# Практический пример: стек (LIFO - Last In, First Out)
stack = []
stack.append("первый")
stack.append("второй")
stack.append("третий")
print(f"Стек: {stack}")  # Стек: ['первый', 'второй', 'третий']

last_item = stack.pop()
print(f"Извлечен: {last_item}")  # Извлечен: третий
print(f"Стек: {stack}")  # Стек: ['первый', 'второй']
```

### `clear()` - Удаление всех элементов

```python
list.clear() -> None
```

```python
fruits = ["яблоко", "банан", "вишня"]
fruits.clear()
print(fruits)  # []

# Эквивалентно
fruits = ["яблоко", "банан", "вишня"]
del fruits[:]
print(fruits)  # []

# Или
fruits = ["яблоко", "банан", "вишня"]
fruits[:] = []
print(fruits)  # []
```

### `del` - Удаление элементов по индексу или срезу

❗ **`del`** - это не метод, а оператор языка

```python
# Удаление по индексу
fruits = ["яблоко", "банан", "вишня", "груша"]
del fruits[1]  # Удаляет "банан"
print(fruits)  # ["яблоко", "вишня", "груша"]

# Удаление по срезу
numbers = [1, 2, 3, 4, 5, 6]
del numbers[1:4]  # Удаляет элементы с индексами 1, 2, 3
print(numbers)  # [1, 5, 6]

# Удаление каждого второго элемента
numbers = [1, 2, 3, 4, 5, 6]
del numbers[::2]  # Удаляет элементы с индексами 0, 2, 4
print(numbers)  # [2, 4, 6]
```

---

## 3. Методы поиска и подсчета

### `index()` - Поиск индекса первого вхождения

```python
list.index(value: Any, start: int = 0, stop: int = sys.maxsize) -> int
```
- **`value`** - значение для поиска
- **`start`** - начальная позиция поиска
- **`stop`** - конечная позиция поиска

```python
fruits = ["яблоко", "банан", "вишня", "банан"]
index = fruits.index("банан")
print(index)  # 1 (индекс первого вхождения)

# Поиск с определенной позиции
index2 = fruits.index("банан", 2)  # Ищем начиная с индекса 2
print(index2)  # 3

# Ошибка, если элемент не найден
try:
    index3 = fruits.index("лимон")  # ValueError: 'лимон' is not in list
except ValueError as e:
    print(f"Ошибка: {e}")

# Безопасный поиск
def safe_index(lst, value):
    try:
        return lst.index(value)
    except ValueError:
        return -1  # Или None, если предпочитаете

fruits = ["яблоко", "банан", "вишня"]
print(safe_index(fruits, "банан"))  # 1
print(safe_index(fruits, "лимон"))  # -1
```

### `count()` - Подсчет количества вхождений

```python
list.count(value: Any) -> int
```
- **`value`** - значение для подсчета

```python
numbers = [1, 2, 3, 2, 2, 4, 2]
count = numbers.count(2)
print(count)  # 4

# Подсчет всех уникальных элементов
from collections import Counter
numbers = [1, 2, 3, 2, 2, 4, 2]
counter = Counter(numbers)
print(counter)  # Counter({2: 4, 1: 1, 3: 1, 4: 1})

# Или вручную
def count_all(lst):
    counts = {}
    for item in lst:
        counts[item] = counts.get(item, 0) + 1
    return counts

numbers = [1, 2, 3, 2, 2, 4, 2]
print(count_all(numbers))  # {1: 1, 2: 4, 3: 1, 4: 1}
```

---

## 4. Методы сортировки и изменения порядка

### `sort()` - Сортировка списка на месте

```python
list.sort(key: Callable = None, reverse: bool = False) -> None
```
- **`key`** - функция для извлечения ключа сравнения
- **`reverse`** - сортировка в обратном порядке

```python
# Простая сортировка
numbers = [3, 1, 4, 1, 5, 9, 2, 6]
numbers.sort()
print(numbers)  # [1, 1, 2, 3, 4, 5, 6, 9]

# Сортировка в обратном порядке
numbers = [3, 1, 4, 1, 5, 9, 2, 6]
numbers.sort(reverse=True)
print(numbers)  # [9, 6, 5, 4, 3, 2, 1, 1]

# Сортировка строк
words = ["яблоко", "банан", "вишня", "абрикос"]
words.sort()
print(words)  # ['абрикос', 'банан', 'вишня', 'яблоко']

# Сортировка по длине строки
words = ["яблоко", "банан", "вишня", "абрикос"]
words.sort(key=len)
print(words)  # ['банан', 'вишня', 'яблоко', 'абрикос']

# Сортировка по нескольким критериям
students = [
    ("Анна", 85),
    ("Борис", 92),
    ("Анна", 78),
    ("Борис", 85)
]
# Сортировка по имени, затем по оценке
students.sort(key=lambda x: (x[0], x[1]))
print(students)  # [('Анна', 78), ('Анна', 85), ('Борис', 85), ('Борис', 92)]
```

### `reverse()` - Обращение порядка элементов

```python
list.reverse() -> None
```

```python
numbers = [1, 2, 3, 4, 5]
numbers.reverse()
print(numbers)  # [5, 4, 3, 2, 1]

# Палиндром
word = list("python")
word.reverse()
print(''.join(word))  # nohtyp

# Практический пример: обращение последних действий
actions = ["открыть файл", "редактировать", "сохранить"]
print(f"Прямой порядок: {actions}")

actions.reverse()
print(f"Обратный порядок: {actions}")
# Обратный порядок: ['сохранить', 'редактировать', 'открыть файл']
```

---

## 5. Методы копирования

### `copy()` - Создание поверхностной копии

```python
list.copy() -> list
```

```python
original = [1, 2, 3, 4, 5]
copy1 = original.copy()
copy2 = original[:]  # Альтернативный способ
copy3 = list(original)  # Еще один способ

# Изменение копии не влияет на оригинал
copy1.append(6)
print(f"Оригинал: {original}")  # Оригинал: [1, 2, 3, 4, 5]
print(f"Копия: {copy1}")  # Копия: [1, 2, 3, 4, 5, 6]

# Поверхностная копия для вложенных списков
original = [[1, 2], [3, 4]]
shallow_copy = original.copy()

# Изменение вложенного списка влияет на обе копии
shallow_copy[0].append(3)
print(f"Оригинал: {original}")  # Оригинал: [[1, 2, 3], [3, 4]]
print(f"Копия: {shallow_copy}")  # Копия: [[1, 2, 3], [3, 4]]

# Для глубокого копирования нужен модуль copy
import copy
deep_copy = copy.deepcopy(original)
deep_copy[0].append(4)
print(f"Оригинал: {original}")  # Оригинал: [[1, 2, 3], [3, 4]]
print(f"Глубокая копия: {deep_copy}")  # Глубокая копия: [[1, 2, 3, 4], [3, 4]]
```

---

## 6. Полезные операции со списками

### Объединение списков

```python
# Оператор +
list1 = [1, 2, 3]
list2 = [4, 5, 6]
combined = list1 + list2
print(combined)  # [1, 2, 3, 4, 5, 6]

# Не изменяет исходные списки
print(list1)  # [1, 2, 3]
print(list2)  # [4, 5, 6]

# Оператор += (изменяет исходный список)
list1 += list2
print(list1)  # [1, 2, 3, 4, 5, 6]
```

### Повторение списков

```python
# Оператор *
zeros = [0] * 5
print(zeros)  # [0, 0, 0, 0, 0]

# Внимание с вложенными списками!
matrix_wrong = [[0] * 3] * 3  # Все строки ссылаются на один список
matrix_wrong[0][0] = 1
print(matrix_wrong)  # [[1, 0, 0], [1, 0, 0], [1, 0, 0]] - неправильно!

# Правильный способ
matrix_correct = [[0] * 3 for _ in range(3)]
matrix_correct[0][0] = 1
print(matrix_correct)  # [[1, 0, 0], [0, 0, 0], [0, 0, 0]] - правильно!
```

### Проверка вхождения

```python
fruits = ["яблоко", "банан", "вишня"]

# Проверка наличия элемента
if "банан" in fruits:
    print("Банан есть в списке")

# Проверка отсутствия элемента
if "лимон" not in fruits:
    print("Лимона нет в списке")

# Для больших списков быстрее использовать set
big_list = list(range(1000000))
big_set = set(big_list)

# Медленно
# print(999999 in big_list)

# Быстро
# print(999999 in big_set)
```

---

## 7. Практические примеры

### Фильтрация списка

```python
# Удаление всех вхождений элемента
numbers = [1, 2, 3, 2, 4, 2, 5]
# Нельзя изменять список во время итерации
# for num in numbers:
#     if num == 2:
#         numbers.remove(num)  # Это может пропустить элементы!

# Правильные способы:
# 1. Создание нового списка
filtered = [x for x in numbers if x != 2]
print(filtered)  # [1, 3, 4, 5]

# 2. Обратная итерация
numbers = [1, 2, 3, 2, 4, 2, 5]
for i in range(len(numbers) - 1, -1, -1):
    if numbers[i] == 2:
        numbers.pop(i)
print(numbers)  # [1, 3, 4, 5]

# 3. Использование filter
numbers = [1, 2, 3, 2, 4, 2, 5]
filtered = list(filter(lambda x: x != 2, numbers))
print(filtered)  # [1, 3, 4, 5]
```

### Работа с очередью

```python
from collections import deque

# Неэффективно для больших списков
queue = []
queue.append("первый")    # O(1)
queue.append("второй")    # O(1)
first = queue.pop(0)      # O(n) - медленно!

# Эффективно
queue = deque()
queue.append("первый")    # O(1)
queue.append("второй")    # O(1)
first = queue.popleft()   # O(1) - быстро!
```

### Поиск максимального/минимального элемента

```python
numbers = [3, 1, 4, 1, 5, 9, 2, 6]

# Простой поиск
max_num = max(numbers)
min_num = min(numbers)
print(f"Максимум: {max_num}, Минимум: {min_num}")

# Поиск индекса максимального элемента
max_index = numbers.index(max(numbers))
print(f"Индекс максимума: {max_index}")

# Поиск по условию
students = [
    ("Анна", 85),
    ("Борис", 92),
    ("Вера", 78)
]

# Студент с лучшей оценкой
best_student = max(students, key=lambda x: x[1])
print(f"Лучший студент: {best_student}")  # Лучший студент: ('Борис', 92)
```

---

## 8. Распространенные ошибки и как их избежать

### Изменение списка во время итерации

```python
# НЕПРАВИЛЬНО
numbers = [1, 2, 3, 4, 5]
for i, num in enumerate(numbers):
    if num % 2 == 0:
        numbers.pop(i)  # Может пропустить элементы или вызвать ошибку

# ПРАВИЛЬНО
numbers = [1, 2, 3, 4, 5]
numbers = [x for x in numbers if x % 2 != 0]  # Создаем новый список
```

### Копирование списков

```python
# НЕПРАВИЛЬНО
original = [1, 2, 3]
copy = original  # Это не копия, а еще одна ссылка на тот же список
copy.append(4)
print(original)  # [1, 2, 3, 4] - изменился и оригинал!

# ПРАВИЛЬНО
original = [1, 2, 3]
copy = original.copy()  # Или original[:], или list(original)
copy.append(4)
print(original)  # [1, 2, 3] - оригинал не изменился
```

### Использование списков по умолчанию в функциях

```python
# НЕПРАВИЛЬНО
def add_item(item, target_list=[]):  # Изменяемый объект по умолчанию
    target_list.append(item)
    return target_list

print(add_item(1))  # [1]
print(add_item(2))  # [1, 2] - неожиданно!

# ПРАВИЛЬНО
def add_item(item, target_list=None):
    if target_list is None:
        target_list = []
    target_list.append(item)
    return target_list

print(add_item(1))  # [1]
print(add_item(2))  # [2] - как ожидалось
```

---

## 9. Резюме

Методы списков в Python можно разделить на несколько групп:

**Добавление элементов:**

- `append()` - добавить один элемент в конец
- `insert()` - вставить элемент на определенную позицию
- `extend()` - добавить все элементы из другого итерируемого объекта

**Удаление элементов:**

- `remove()` - удалить первое вхождение элемента
- `pop()` - удалить и вернуть элемент по индексу
- `clear()` - удалить все элементы

**Поиск и подсчет:**

- `index()` - найти индекс первого вхождения
- `count()` - подсчитать количество вхождений

**Сортировка и изменение порядка:**

- `sort()` - отсортировать список на месте
- `reverse()` - обратить порядок элементов

**Копирование:**

- `copy()` - создать поверхностную копию

Помните: большинство методов списков изменяют исходный список на месте, а не создают новый!

---

[[03_3_1_lists|← 📋 Списки]] | [[03_3_3_tuples|📦 Кортежи →]]
