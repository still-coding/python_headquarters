[[04_2_1_if_else |← 🤔 Условный оператор if-else]] | [[04_3_1_for_loop| 🔁 Цикл for →]]

---
# 🔀 Тернарное Тернарное выражение
```toc
minLevel: 2
```
## Что это?

Тернарное выражение — это компактная форма записи `if-else`, которая позволяет записать простое условие в одну строку.

## Синтаксис

```python
значение_если_истина if условие else значение_если_ложь
```

Это выражение возвращает одно из двух значений в зависимости от условия.

---

## Базовые примеры

### Простое присваивание

Обычный способ:
```python
age = 20

if age >= 18:
    status = "Взрослый"
else:
    status = "Ребенок"
```

С тернарным выражением:
```python
age = 20
status = "Взрослый" if age >= 18 else "Ребенок"
```

### Вывод текста

```python
temperature = 25
print("Жарко" if temperature > 30 else "Нормально")
```

### Математические операции

```python
x = 10
y = 20

# Выбираем максимальное значение
max_value = x if x > y else y
print(max_value)  # 20

# Или можно сразу в вычислении
result = (x if x > y else y) * 2
```

---

## Когда использовать?

### ✅ Хорошо подходит для

**Простых присваиваний:**
```python
# Определение чётности
parity = "чётное" if number % 2 == 0 else "нечётное"

# Выбор значения по умолчанию
username = user_input if user_input else "Гость"

# Форматирование вывода
print(f"Найден {count} {'элемент' if count == 1 else 'элемента'}")
```

**Возврата значений из функций:**
```python
def get_discount(is_student):
    return 0.2 if is_student else 0.0

def absolute_value(x):
    return x if x >= 0 else -x
```

### ❌ Не стоит использовать для

**Сложных условий:**
```python
# Плохо — слишком сложно читать
result = "A" if x > 10 and y < 5 else "B" if x > 5 else "C"

# Лучше использовать обычный if-elif-else
if x > 10 and y < 5:
    result = "A"
elif x > 5:
    result = "B"
else:
    result = "C"
```

**Выполнения действий (не присваивания):**
```python
# Плохо — неочевидный код
print("Да") if condition else print("Нет")

# Лучше
if condition:
    print("Да")
else:
    print("Нет")
```

---

## Вложенные тернарные выражения

Технически можно вкладывать тернарные выражения, но это усложняет чтение:

```python
# Можно, но не рекомендуется
score = 85
grade = "A" if score >= 90 else "B" if score >= 80 else "C" if score >= 70 else "F"

# Лучше использовать обычный if-elif-else
if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
else:
    grade = "F"
```

---

## Практические примеры

### Валидация данных

```python
# Проверка диапазона
age = int(input("Введите возраст: "))
valid_age = age if 0 < age < 150 else None

if valid_age:
    print(f"Ваш возраст: {valid_age}")
else:
    print("Некорректный возраст")
```

### Форматирование вывода

```python
# Множественное число
items_count = 5
print(f"Куплено {items_count} {'товар' if items_count == 1 else 'товаров'}")

# Цвет в зависимости от статуса
status = "online"
color = "green" if status == "online" else "red"
```

### Значения по умолчанию

```python
# Если значение не задано, используем дефолтное
config = {}
timeout = config.get("timeout") if "timeout" in config else 30

# Или ещё короче с помощью метода get():
timeout = config.get("timeout", 30)
```

### В списковых включениях

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Чётные делаем отрицательными, нечётные оставляем
result = [n if n % 2 != 0 else -n for n in numbers]
print(result)  # [1, -2, 3, -4, 5, -6, 7, -8, 9, -10]
```

---

## Сравнение с другими подходами

### Тернарное выражение vs обычный if-else

```python
# Ситуация 1: Простое присваивание
# Тернарное (лучше)
message = "Доступ разрешён" if password == "123" else "Доступ запрещён"

# Обычный (многословно)
if password == "123":
    message = "Доступ разрешён"
else:
    message = "Доступ запрещён"

# Ситуация 2: Сложная логика
# Тернарное (плохо)
result = "A" if x > 10 and y > 10 else "B" if x > 5 or y > 5 else "C"

# Обычный (лучше)
if x > 10 and y > 10:
    result = "A"
elif x > 5 or y > 5:
    result = "B"
else:
    result = "C"
```

---

## Советы по использованию

1. **Используйте для простых случаев** — если условие и значения короткие и понятные
2. **Одна строка** — если не помещается в одну читаемую строку, используйте обычный `if-else`
3. **Читаемость важнее краткости** — если код сложно понять с первого взгляда, лучше развернуть в полный `if-else`

---

## Связанные темы

- [[04_2_1_if_else|Условный оператор if-else]]
- [[04_5_comprehensions|Включения (comprehensions)]] — часто используются вместе с тернарным оператором

---

[[04_2_1_if_else |← 🤔 Условный оператор if-else]] | [[04_3_1_for_loop| 🔁 Цикл for →]]
