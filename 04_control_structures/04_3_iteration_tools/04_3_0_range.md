[[04_2_2_loop_control|← ⚙️ Управление циклами]] | [[04_3_1_enumerate| 🔢 enumerate() — нумерация элементов →]]

---
# 🔢 range() — генерация числовых последовательностей
```toc
minLevel: 2
```
## Что это?

`range()` создаёт объект-последовательность чисел, который генерирует числа по требованию (ленивое вычисление). Это самая часто используемая функция для создания числовых диапазонов в циклах.

---

## Синтаксис

```python
range(stop)                    # от 0 до stop-1
range(start, stop)             # от start до stop-1  
range(start, stop, step)       # от start до stop-1 с шагом step
```

**Важно:** Конечное значение `stop` **не включается** в диапазон!

---

## Базовое использование

### range(stop)

```python
# Числа от 0 до 4
for i in range(5):
    print(i, end=" ")
# Вывод: 0 1 2 3 4

# Преобразование в список
print(list(range(5)))  # [0, 1, 2, 3, 4]
```

### range(start, stop)

```python
# Числа от 2 до 7
for i in range(2, 8):
    print(i, end=" ")
# Вывод: 2 3 4 5 6 7

print(list(range(2, 8)))  # [2, 3, 4, 5, 6, 7]
```

### range(start, stop, step)

```python
# Чётные числа от 0 до 8
for i in range(0, 10, 2):
    print(i, end=" ")
# Вывод: 0 2 4 6 8

print(list(range(0, 10, 2)))  # [0, 2, 4, 6, 8]

# Нечётные числа
print(list(range(1, 10, 2)))  # [1, 3, 5, 7, 9]
```

---

## Обратный отсчёт

Используйте отрицательный шаг для убывающей последовательности:

```python
# От 10 до 1
for i in range(10, 0, -1):
    print(i, end=" ")
# Вывод: 10 9 8 7 6 5 4 3 2 1

print(list(range(10, 0, -1)))  # [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]

# От 5 до 0 (включая 0)
print(list(range(5, -1, -1)))  # [5, 4, 3, 2, 1, 0]

# Обратный отсчёт с шагом 2
print(list(range(10, 0, -2)))  # [10, 8, 6, 4, 2]
```

---

## Свойства range

### range — это объект, а не список!

```python
r = range(5)

print(type(r))         # <class 'range'>
print(r)               # range(0, 5)

# Атрибуты range
print(r.start)         # 0
print(r.stop)          # 5
print(r.step)          # 1
```

### Эффективность памяти

```python
# ❌ Неэффективно для больших диапазонов
big_list = list(range(1000000))  # Создаёт список из 1 млн элементов в памяти

# ✅ Эффективно — генерирует по требованию
big_range = range(1000000)       # Объект range занимает постоянное количество памяти

# Использование в цикле
for i in range(1000000):         # Память не расходуется на весь список
    if i == 10:
        break
```

### Проверка принадлежности (очень быстро!)

```python
# Проверка работает за O(1) — мгновенно!
print(1000000 in range(0, 2000000, 2))  # True
print(999999 in range(0, 2000000, 2))   # False

# Даже для огромных диапазонов
print(5000000000 in range(0, 10000000000, 5))  # True (мгновенно!)
```

---

## Практические примеры

### Обратный отсчёт

```python
print("Запуск ракеты!")
for i in range(10, 0, -1):
    print(f"До старта: {i}")
print("Пуск!")
```

### Индексы списка

```python
fruits = ["яблоко", "банан", "вишня"]

# Перебор по индексам
for i in range(len(fruits)):
    print(f"{i}: {fruits[i]}")

# Вывод:
# 0: яблоко
# 1: банан
# 2: вишня
```

**Примечание:** Обычно лучше использовать `enumerate()`:
```python
for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")
```

### Таблица умножения

```python
# Таблица умножения от 1 до 10
for i in range(1, 11):
    for j in range(1, 11):
        print(f"{i * j:4d}", end="")
    print()
```

### Срезы списка с range

```python
numbers = list(range(20))  # [0, 1, 2, ..., 19]

# Каждый третий элемент
every_third = numbers[::3]
print(every_third)  # [0, 3, 6, 9, 12, 15, 18]

# Эквивалентно
every_third = [numbers[i] for i in range(0, len(numbers), 3)]
```

---

## Пустые диапазоны

```python
# Если start >= stop при положительном step
print(list(range(5, 2)))      # []
print(list(range(5, 5)))      # []

# Если start <= stop при отрицательном step
print(list(range(2, 5, -1)))  # []

# Корректное использование
print(list(range(5, 2, -1)))  # [5, 4, 3]
```

---

## Операции с range

### Длина

```python
r = range(5, 15, 2)
print(len(r))  # 5
# Элементы: 5, 7, 9, 11, 13
```

### Индексирование

```python
r = range(10, 20)

print(r[0])    # 10 (первый элемент)
print(r[5])    # 15
print(r[-1])   # 19 (последний элемент)

# Но нельзя изменять!
# r[0] = 100  # ОШИБКА! TypeError
```

### Срезы range

```python
r = range(10)

print(r[2:5])      # range(2, 5) — подмножество
print(r[::2])      # range(0, 10, 2) — каждый второй
print(r[::-1])     # range(9, -1, -1) — в обратном порядке

# Преобразование в список
print(list(r[2:5]))    # [2, 3, 4]
print(list(r[::2]))    # [0, 2, 4, 6, 8]
```

---

## Продвинутые примеры

### Генерация последовательностей

```python
# Степени двойки
powers_of_two = [2 ** i for i in range(10)]
print(powers_of_two)  # [1, 2, 4, 8, 16, 32, 64, 128, 256, 512]

# Квадраты чисел
squares = [i ** 2 for i in range(1, 11)]
print(squares)  # [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

# Числа Фибоначчи (первые 10)
fib = [0, 1]
for i in range(2, 10):
    fib.append(fib[i-1] + fib[i-2])
print(fib)  # [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

### Разбиение на блоки

```python
def chunks(data, chunk_size):
    """Разбивает список на блоки заданного размера"""
    for i in range(0, len(data), chunk_size):
        yield data[i:i + chunk_size]

data = list(range(15))
for chunk in chunks(data, 4):
    print(chunk)

# Вывод:
# [0, 1, 2, 3]
# [4, 5, 6, 7]
# [8, 9, 10, 11]
# [12, 13, 14]
```

### Пропуск элементов

```python
data = list(range(20))

# Пропускаем каждый третий элемент
result = [data[i] for i in range(len(data)) if i % 3 != 0]
print(result)  # [1, 2, 4, 5, 7, 8, 10, 11, 13, 14, 16, 17, 19]
```

---

## range vs другие подходы

### Создание списка чисел

```python
# ❌ Плохо
numbers = []
for i in range(10):
    numbers.append(i)

# ✅ Хорошо
numbers = list(range(10))

# ✅ Ещё лучше (если нужен просто цикл)
for i in range(10):
    print(i)  # не создаём список вообще
```

### Итерация с индексами

```python
data = ["a", "b", "c", "d"]

# ❌ Менее питоничо
for i in range(len(data)):
    print(f"{i}: {data[i]}")

# ✅ Более питонично
for i, item in enumerate(data):
    print(f"{i}: {item}")
```

Подробнее: [[04_3_1_enumerate|enumerate()]]

---

## Частые ошибки

### ❌ Забыть, что stop не включается

```python
# Хочу числа от 1 до 10 включительно
print(list(range(1, 10)))  # [1, 2, 3, 4, 5, 6, 7, 8, 9] — ОШИБКА!
```

✅ Правильно:
```python
print(list(range(1, 11)))  # [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

### ❌ Неправильный шаг для обратного отсчёта

```python
# Пытаемся получить обратный отсчёт
print(list(range(10, 0)))  # [] — пустой!
```

✅ Правильно:
```python
print(list(range(10, 0, -1)))  # [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
```

### ❌ Попытка изменить range

```python
r = range(5)
r[0] = 10  # ОШИБКА! TypeError: 'range' object does not support item assignment
```

range — неизменяемый объект!

---

## Когда использовать range?

### ✅ Используйте range для:
- Повторения действия N раз
- Генерации последовательности чисел
- Итерации по индексам
- Обратного отсчёта

### ❌ Не используйте range, когда:
- Можно использовать `enumerate()` для получения индексов
- Можно напрямую итерировать по коллекции
- Нужен список конкретных чисел (используйте явный список)

---

## Связанные темы

- [[04_2_0_for_loop|Цикл for]] — основное применение range
- [[04_3_1_enumerate|enumerate()]] — когда нужны и индексы, и значения
- [[04_4_comprehensions|Списковые включения]] — часто используется с range

---

[[04_2_2_loop_control|← ⚙️ Управление циклами]] | [[04_3_1_enumerate| 🔢 enumerate() — нумерация элементов →]]
