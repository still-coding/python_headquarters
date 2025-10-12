[[04_3_2_while_loop|← 🔄 Цикл while]] | [[04_4_1_range| 🔢 range() — генерация числовых последовательностей →]]

---
# ⚙️ Управление циклами: break, continue, else
```toc
minLevel: 2
```
## Что это?

Python предоставляет специальные операторы для управления выполнением циклов:
- `break` — досрочный выход из цикла
- `continue` — переход к следующей итерации
- `else` — выполняется после нормального завершения цикла

---

## break — выход из цикла

Оператор `break` немедленно прерывает выполнение цикла и передаёт управление следующему после цикла коду.

### Синтаксис

```python
for элемент in последовательность:
    if условие:
        break  # выход из цикла
    # код
```

### Примеры с for

```python
# Поиск элемента
numbers = [1, 3, 5, 7, 9, 11, 13]

for num in numbers:
    if num == 7:
        print(f"Найдено число: {num}")
        break  # прекращаем поиск
    print(f"Проверяем {num}")

# Вывод:
# Проверяем 1
# Проверяем 3
# Проверяем 5
# Найдено число: 7
```

```python
# Досрочное завершение при ошибке
data = [10, 20, 0, 40, 50]

for num in data:
    if num == 0:
        print("Ошибка: деление на ноль!")
        break
    result = 100 / num
    print(f"100 / {num} = {result}")

# Вывод:
# 100 / 10 = 10.0
# 100 / 20 = 5.0
# Ошибка: деление на ноль!
```

### Примеры с while

```python
# Ввод пароля с ограниченным числом попыток
attempts = 3
correct_password = "secret123"

while attempts > 0:
    password = input("Введите пароль: ")
    
    if password == correct_password:
        print("Доступ разрешён!")
        break
    
    attempts -= 1
    print(f"Неверный пароль. Осталось попыток: {attempts}")

if attempts == 0:
    print("Доступ заблокирован")
```

---

## continue — переход к следующей итерации

Оператор `continue` пропускает оставшийся код в текущей итерации и переходит к следующей.

### Синтаксис

```python
for элемент in последовательность:
    if условие:
        continue  # пропустить остаток итерации
    # этот код не выполнится, если условие истинно
```

### Примеры с for

```python
# Пропуск определённых значений
for i in range(10):
    if i % 2 == 0:
        continue  # пропускаем чётные числа
    print(i)

# Вывод: 1, 3, 5, 7, 9
```

```python
# Фильтрация данных
words = ["apple", "banana", "avocado", "cherry", "apricot"]

print("Слова, начинающиеся с 'a':")
for word in words:
    if not word.startswith('a'):
        continue  # пропускаем слова, не начинающиеся с 'a'
    print(word)

# Вывод:
# apple
# avocado
# apricot
```

```python
# Обработка с пропуском ошибок
numbers = ["1", "2", "abc", "4", "xyz", "6"]

for item in numbers:
    try:
        num = int(item)
    except ValueError:
        print(f"Пропускаем некорректное значение: {item}")
        continue
    
    print(f"Число: {num}, квадрат: {num ** 2}")

# Вывод:
# Число: 1, квадрат: 1
# Число: 2, квадрат: 4
# Пропускаем некорректное значение: abc
# Число: 4, квадрат: 16
# Пропускаем некорректное значение: xyz
# Число: 6, квадрат: 36
```

### Примеры с while

```python
# Пропуск итераций в while
count = 0

while count < 10:
    count += 1
    
    if count % 3 == 0:
        continue  # пропускаем числа, кратные 3
    
    print(count)

# Вывод: 1, 2, 4, 5, 7, 8, 10
```

---

## else в циклах

Блок `else` после цикла выполняется, если цикл завершился нормально (без `break`).

### Синтаксис

```python
for элемент in последовательность:
    if условие:
        break
else:
    # выполнится, если break НЕ был вызван
```

### Примеры с for

```python
# Проверка наличия элемента
numbers = [1, 3, 5, 7, 9]
search = 4

for num in numbers:
    if num == search:
        print(f"Число {search} найдено!")
        break
else:
    print(f"Число {search} не найдено")

# Вывод: Число 4 не найдено
```

```python
# Проверка условия для всех элементов
numbers = [2, 4, 6, 8, 10]

for num in numbers:
    if num % 2 != 0:
        print("Найдено нечётное число!")
        break
else:
    print("Все числа чётные")

# Вывод: Все числа чётные
```

### Примеры с while

```python
# Успешное выполнение задачи
attempts = 0
max_attempts = 5
success = False

while attempts < max_attempts:
    attempts += 1
    print(f"Попытка {attempts}")
    
    # Имитация успешного выполнения
    if attempts == 3:
        success = True
        break
else:
    print("Превышено максимальное количество попыток")

if success:
    print("Задача выполнена успешно!")
```

---

## Комбинирование операторов

### break + continue

```python
# Обработка данных с условиями
data = [1, -2, 3, 0, 5, -6, 7, 8, 0, 10]

print("Обрабатываем положительные числа до первого нуля:")
for num in data:
    if num == 0:
        print("Найден ноль, останавливаемся")
        break
    
    if num < 0:
        print(f"Пропускаем отрицательное: {num}")
        continue
    
    print(f"Обрабатываем: {num}")

# Вывод:
# Обрабатываем: 1
# Пропускаем отрицательное: -2
# Обрабатываем: 3
# Найден ноль, останавливаемся
```

### break + continue + else

```python
# Сложная логика обработки
def process_items(items, max_errors=3):
    errors = 0
    
    for item in items:
        # Пропускаем пустые элементы
        if not item:
            continue
        
        # Останавливаемся при превышении ошибок
        if errors >= max_errors:
            print("Слишком много ошибок!")
            break
        
        # Обработка с возможной ошибкой
        try:
            result = process(item)
            print(f"Обработан: {item} -> {result}")
        except Exception as e:
            errors += 1
            print(f"Ошибка при обработке {item}: {e}")
            continue
    else:
        print("Все элементы обработаны успешно!")

items = ["a", "b", "", "c", "d"]
process_items(items)
```

---

## Вложенные циклы

### break во вложенных циклах

`break` прерывает только самый внутренний цикл:

```python
for i in range(3):
    for j in range(3):
        if j == 1:
            break  # прерывает только внутренний цикл
        print(f"({i}, {j})", end=" ")
    print()

# Вывод:
# (0, 0) 
# (1, 0) 
# (2, 0)
```

### Выход из всех вложенных циклов

Способ 1: Использовать флаг
```python
found = False

for i in range(5):
    for j in range(5):
        if i * j == 6:
            print(f"Найдено: {i} × {j} = 6")
            found = True
            break
    if found:
        break
```

Способ 2: Вынести в функцию и использовать return
```python
def find_pair():
    for i in range(5):
        for j in range(5):
            if i * j == 6:
                print(f"Найдено: {i} × {j} = 6")
                return  # выход из всех циклов

find_pair()
```

---

## Практические примеры

### Валидация с повторными попытками

```python
def get_valid_age():
    max_attempts = 3
    attempts = 0
    
    while attempts < max_attempts:
        age_input = input("Введите ваш возраст: ")
        
        if not age_input.isdigit():
            print("Пожалуйста, введите число")
            attempts += 1
            continue
        
        age = int(age_input)
        
        if age < 0 or age > 150:
            print("Возраст должен быть от 0 до 150")
            attempts += 1
            continue
        
        return age  # успешный ввод
    else:
        print("Превышено количество попыток")
        return None

age = get_valid_age()
if age:
    print(f"Ваш возраст: {age}")
```

### Обработка файла с пропуском ошибок

```python
lines = [
    "10",
    "20",
    "invalid",
    "30",
    "",
    "40"
]

total = 0
processed = 0

for line in lines:
    # Пропускаем пустые строки
    if not line.strip():
        continue
    
    try:
        number = int(line)
        total += number
        processed += 1
    except ValueError:
        print(f"Пропускаем некорректную строку: {line}")
        continue

print(f"Обработано чисел: {processed}")
print(f"Сумма: {total}")
```

### Поиск первого совпадения

```python
def find_student(students, name):
    for student in students:
        if student["name"] == name:
            return student
    else:
        return None

students = [
    {"name": "Анна", "age": 20},
    {"name": "Борис", "age": 22},
    {"name": "Вера", "age": 21}
]

student = find_student(students, "Борис")
if student:
    print(f"Найден: {student}")
else:
    print("Студент не найден")
```

---

## Частые ошибки

### ❌ continue в неправильном месте

```python
# Код после continue не выполнится
for i in range(5):
    if i % 2 == 0:
        continue
    print(f"Нечётное: {i}")  # выполнится
    print("Эта строка тоже")  # выполнится
```

### ❌ Бесполезный else

```python
# else не нужен, если break никогда не вызывается
for i in range(5):
    print(i)
else:
    print("Цикл завершён")  # всегда выполнится
```

### ❌ Путаница с вложенными циклами

```python
# break прерывает только внутренний цикл
for i in range(3):
    for j in range(3):
        if j == 1:
            break  # прерывает только цикл по j
    # внешний цикл продолжается
```

---

## Когда использовать?

### break
- ✅ Досрочное завершение при нахождении результата
- ✅ Выход по критической ошибке
- ✅ Ограничение числа итераций

### continue
- ✅ Пропуск некорректных данных
- ✅ Фильтрация в процессе обработки
- ✅ Упрощение условной логики

### else
- ✅ Действие после полной обработки
- ✅ Сообщение о том, что элемент не найден
- ✅ Финальная проверка успешности

---

## Связанные темы

- [[04_3_1_for_loop|Цикл for]]
- [[04_3_2_while_loop|Цикл while]]

---

[[04_3_2_while_loop|← 🔄 Цикл while]] | [[04_4_1_range| 🔢 range() — генерация числовых последовательностей →]]
