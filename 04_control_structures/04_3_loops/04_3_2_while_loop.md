[[04_3_1_for_loop|← 🔁 Цикл for]] | [[04_3_3_loop_control| ⚙️ Управление циклами →]]

---
# 🔄 Цикл while

## Что это?
```toc
minLevel: 2
```

Цикл `while` выполняет блок кода **пока условие истинно**. В отличие от `for`, цикл `while` используется, когда заранее **неизвестно количество итераций**.

## Синтаксис

```python
while условие:
    # код выполняется, пока условие истинно
```

---

## Базовые примеры

### Простой счётчик

```python
count = 0

while count < 5:
    print(count)
    count += 1

# Вывод: 0, 1, 2, 3, 4
```

### Обратный отсчёт

```python
countdown = 5

while countdown > 0:
    print(countdown)
    countdown -= 1

print("Пуск!")

# Вывод: 5, 4, 3, 2, 1, Пуск!
```

### Ввод данных пользователем

```python
password = ""

while password != "secret":
    password = input("Введите пароль: ")

print("Доступ разрешён!")
```

---

## while vs for

### Когда использовать while?

✅ **Используйте while когда:**
- Количество итераций заранее неизвестно
- Цикл зависит от пользовательского ввода
- Условие выхода сложное или динамическое
- Нужен бесконечный цикл с условным выходом

❌ **Не используйте while когда:**
- Известно точное количество итераций
- Нужно перебрать последовательность
- Можно использовать `for` (он более читаемый)

### Примеры сравнения

```python
# for — когда известно количество итераций
for i in range(5):
    print(i)

# while — когда зависит от условия
user_input = ""
while user_input != "quit":
    user_input = input("Команда: ")
```

---

## Основные паттерны

### Счётчик с условием

```python
count = 0

while count < 10:
    print(f"Итерация {count}")
    count += 1
```

### Поиск элемента

```python
numbers = [1, 3, 5, 7, 9, 11]
target = 7
index = 0

while index < len(numbers):
    if numbers[index] == target:
        print(f"Найдено на позиции {index}")
        break
    index += 1
```

### Накопление значений

```python
total = 0
number = 1

while number <= 10:
    total += number
    number += 1

print(f"Сумма чисел от 1 до 10: {total}")  # 55
```

---

## Бесконечные циклы

### while True

Бесконечный цикл с явным условием выхода через `break`:

```python
while True:
    command = input("Введите команду (exit для выхода): ")
    
    if command == "exit":
        print("Выход из программы")
        break
    
    print(f"Вы ввели: {command}")
```

### Бесконечный цикл с множественными условиями выхода

```python
while True:
    user_input = input("Введите число (0 для выхода): ")
    
    if user_input == "0":
        break
    
    try:
        number = int(user_input)
        print(f"Квадрат числа: {number ** 2}")
    except ValueError:
        print("Это не число!")
```

---

## Управление циклом while

### break — выход из цикла

```python
count = 0

while count < 10:
    if count == 5:
        break  # выходим из цикла при count = 5
    print(count)
    count += 1

# Вывод: 0, 1, 2, 3, 4
```

### continue — переход к следующей итерации

```python
count = 0

while count < 5:
    count += 1
    if count == 3:
        continue  # пропускаем count = 3
    print(count)

# Вывод: 1, 2, 4, 5
```

### else — выполняется после нормального завершения

```python
count = 0

while count < 3:
    print(count)
    count += 1
else:
    print("Цикл завершён нормально")

# Вывод:
# 0
# 1
# 2
# Цикл завершён нормально
```

```python
# else НЕ выполнится при break
count = 0

while count < 10:
    if count == 3:
        break
    print(count)
    count += 1
else:
    print("Этот блок не выполнится")  # не выведется из-за break
```

Подробнее: [[04_3_3_loop_control|Управление циклами]]

---

## Практические примеры

### Валидация ввода

```python
while True:
    age = input("Введите ваш возраст: ")
    
    if age.isdigit():
        age = int(age)
        if 0 < age < 150:
            break
        else:
            print("Возраст должен быть от 1 до 149")
    else:
        print("Введите число!")

print(f"Ваш возраст: {age}")
```

### Меню программы

```python
while True:
    print("\n=== Меню ===")
    print("1. Новая игра")
    print("2. Загрузить игру")
    print("3. Настройки")
    print("4. Выход")
    
    choice = input("Выберите пункт (1-4): ")
    
    if choice == "1":
        print("Начинаем новую игру...")
    elif choice == "2":
        print("Загрузка игры...")
    elif choice == "3":
        print("Настройки...")
    elif choice == "4":
        print("До свидания!")
        break
    else:
        print("Неверный выбор!")
```

### Накопление суммы

```python
total = 0
count = 0

print("Введите числа (пустая строка для завершения):")

while True:
    user_input = input("Число: ")
    
    if user_input == "":
        break
    
    try:
        number = float(user_input)
        total += number
        count += 1
    except ValueError:
        print("Это не число, попробуйте снова")

if count > 0:
    average = total / count
    print(f"Сумма: {total}")
    print(f"Среднее: {average:.2f}")
else:
    print("Числа не введены")
```

### Поиск в последовательности

```python
numbers = [3, 7, 2, 9, 1, 5, 8]
target = 9
index = 0
found = False

while index < len(numbers):
    if numbers[index] == target:
        found = True
        break
    index += 1

if found:
    print(f"Число {target} найдено на позиции {index}")
else:
    print(f"Число {target} не найдено")
```

---

## Вложенные циклы while

```python
i = 1

while i <= 3:
    j = 1
    while j <= 3:
        print(f"({i}, {j})", end=" ")
        j += 1
    print()  # новая строка
    i += 1

# Вывод:
# (1, 1) (1, 2) (1, 3) 
# (2, 1) (2, 2) (2, 3) 
# (3, 1) (3, 2) (3, 3)
```

---

## Частые ошибки

### ❌ Бесконечный цикл (случайный)

```python
count = 0

while count < 5:
    print(count)
    # ОШИБКА! Забыли увеличить count
    # Цикл никогда не закончится
```

✅ Правильно:
```python
count = 0

while count < 5:
    print(count)
    count += 1  # не забываем изменять переменную условия
```

### ❌ Условие никогда не выполняется

```python
count = 10

while count < 5:  # условие сразу ложно
    print(count)
    count += 1

# Цикл не выполнится ни разу
```

### ❌ Изменение условия внутри цикла некорректно

```python
x = 10

while x > 0:
    print(x)
    x += 1  # ОШИБКА! x растёт, условие всегда истинно
```

✅ Правильно:
```python
x = 10

while x > 0:
    print(x)
    x -= 1  # x уменьшается, цикл завершится
```

---

## Оптимизация и лучшие практики

### Избегайте сложных условий

❌ Плохо:
```python
while x > 0 and x < 100 and y > 10 and not flag and len(items) > 0:
    # сложное условие
```

✅ Лучше:
```python
def should_continue():
    return (x > 0 and x < 100 and 
            y > 10 and not flag and 
            len(items) > 0)

while should_continue():
    # код
```

### Используйте флаги для ясности

```python
# Вместо сложного условия
running = True
valid_input = True
connection_active = True

while running and valid_input and connection_active:
    # внутри цикла можно изменять флаги
    if some_error:
        running = False
```

### Ограничивайте бесконечные циклы

```python
# Добавляем защиту от зависания
max_attempts = 100
attempts = 0

while True:
    attempts += 1
    if attempts > max_attempts:
        print("Превышено максимальное количество попыток")
        break
    
    # основной код
    if success:
        break
```

---

## Связанные темы

- [[04_3_1_for_loop|Цикл for]] — когда известно количество итераций
- [[04_3_3_loop_control|Управление циклами]] — break, continue, else
- [[04_2_1_if_else|Условный оператор if-else]] — для условий в while

---

[[04_3_1_for_loop|← 🔁 Цикл for]] | [[04_3_3_loop_control| ⚙️ Управление циклами →]]
