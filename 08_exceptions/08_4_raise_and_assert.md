[[08_3_exception_hierarchy|← 🌳 Иерархия исключений]] | [[08_5_custom_exceptions|🏗️ Собственные исключения →]]

---
# 🔺 `raise` и `assert`
```toc
minLevel: 2
```

## `raise` — вызов исключения вручную

`raise` позволяет вызвать исключение явно — когда вы сами обнаружили что что-то пошло не так:

```python
def set_age(age):
    if age < 0:
        raise ValueError(f"Возраст не может быть отрицательным: {age}")
    return age


set_age(25)   # ок
set_age(-5)   # ValueError: Возраст не может быть отрицательным: -5
```

## Что можно передать в `raise`

```python
# Экземпляр исключения — самый распространённый способ
raise ValueError("сообщение")

# Класс исключения — Python создаст экземпляр автоматически
raise ValueError   # эквивалентно raise ValueError()

# Без аргументов — повторно поднимает текущее исключение (об этом ниже)
raise
```

## Повторный подъём исключения (`re-raise`)

Иногда нужно перехватить исключение, что-то сделать (залогировать, очистить ресурсы) и передать его дальше:

```python
import logging


def process_data(data):
    try:
        result = complex_operation(data)
    except ValueError as e:
        logging.error(f"Ошибка обработки данных: {e}")
        raise   # Повторно поднимаем то же исключение


# Вызывающий код получит оригинальное исключение
try:
    process_data(bad_data)
except ValueError:
    print("Обработка не удалась")
```

`raise` без аргументов сохраняет оригинальный тип, сообщение и Traceback.

## Цепочка исключений (`raise from`)

Когда одно исключение вызывает другое — используйте `raise ... from ...` чтобы явно показать связь:

```python
def load_config(path):
    try:
        with open(path) as f:
            return f.read()
    except FileNotFoundError as e:
        raise RuntimeError(f"Не удалось загрузить конфиг: {path}") from e


load_config("missing.json")
# RuntimeError: Не удалось загрузить конфиг: missing.json
#
# The above exception was the direct cause of the following exception:
# FileNotFoundError: [Errno 2] No such file or directory: 'missing.json'
```

Python покажет оба исключения в Traceback и явно укажет причинно-следственную связь.

Если хотите скрыть оригинальное исключение — используйте `raise ... from None`:

```python
def get_user(user_id):
    try:
        return database[user_id]
    except KeyError:
        raise ValueError(f"Пользователь {user_id} не найден") from None
        # Внутренняя деталь (KeyError из БД) скрыта от вызывающего кода
```

## Когда вызывать исключения

**Вызывайте исключения когда:**
- Функция получила недопустимые аргументы
- Операция невозможна в текущем состоянии
- Нарушены предусловия для выполнения кода

```python
def divide(a, b):
    if b == 0:
        raise ZeroDivisionError("Делитель не может быть нулём")
    return a / b


def withdraw(balance, amount):
    if amount <= 0:
        raise ValueError("Сумма снятия должна быть положительной")
    if amount > balance:
        raise ValueError(f"Недостаточно средств: баланс {balance}, запрос {amount}")
    return balance - amount
```

**Выбирайте подходящий тип исключения:**

| Ситуация | Исключение |
|---|---|
| Неверное значение аргумента | `ValueError` |
| Неверный тип аргумента | `TypeError` |
| Объект не найден | `KeyError`, `IndexError` или кастомное |
| Операция недоступна | `RuntimeError` или кастомное |
| Нет прав / доступа | `PermissionError` или кастомное |

## `assert` — проверки при отладке

`assert` проверяет условие и вызывает `AssertionError` если оно ложно:

```python
assert условие
assert условие, "сообщение если условие False"
```

```python
def calculate_discount(price, percent):
    assert 0 <= percent <= 100, f"Скидка должна быть от 0 до 100, получено: {percent}"
    assert price >= 0, f"Цена не может быть отрицательной: {price}"
    return price * (1 - percent / 100)


calculate_discount(1000, 10)   # 900.0
calculate_discount(1000, 150)  # AssertionError: Скидка должна быть от 0 до 100
```

## `assert` vs `raise`

Это принципиально разные инструменты:

```python
# assert — для отладки и проверки инвариантов
# Можно отключить запуском python с флагом -O (optimize)
assert x > 0, "x должен быть положительным"

# raise — для обработки ошибок в продакшне
# Никогда не отключается
if x <= 0:
    raise ValueError("x должен быть положительным")
```

**`assert` используйте для:**
- Проверки внутренних инвариантов кода
- Документирования предположений
- Отладки в процессе разработки

**`raise` используйте для:**
- Валидации входных данных от пользователя или внешних источников
- Обработки ошибок в продакшн-коде
- Любых проверок, которые должны работать всегда

```python
# ❌ Плохо — assert для валидации пользовательских данных
def create_user(age):
    assert age >= 0, "Возраст не может быть отрицательным"  # Отключается с -O!

# ✅ Правильно — raise для валидации входных данных
def create_user(age):
    if age < 0:
        raise ValueError("Возраст не может быть отрицательным")
```

## 🎯 Пример с реальной задачей

```python
class Stack:
    def __init__(self, max_size=None):
        self._items = []
        self._max_size = max_size

    def push(self, item):
        if self._max_size and len(self._items) >= self._max_size:
            raise OverflowError(f"Стек переполнен (максимум {self._max_size})")
        self._items.append(item)

    def pop(self):
        if not self._items:
            raise IndexError("Нельзя извлечь элемент из пустого стека")
        return self._items.pop()

    def peek(self):
        if not self._items:
            raise IndexError("Стек пуст")
        return self._items[-1]

    def __len__(self):
        # assert — внутренний инвариант: размер не может быть отрицательным
        assert len(self._items) >= 0, "Внутренняя ошибка: отрицательный размер стека"
        return len(self._items)


stack = Stack(max_size=3)
stack.push(1)
stack.push(2)
stack.push(3)
stack.push(4)   # OverflowError: Стек переполнен (максимум 3)

empty = Stack()
empty.pop()     # IndexError: Нельзя извлечь элемент из пустого стека
```

## 🔎 Проверка на ходу

```python
>>> def check_positive(n):
...     if n <= 0:
...         raise ValueError(f"Ожидалось положительное число, получено: {n}")
...     return n
...
>>> check_positive(5)
5
>>> check_positive(-3)
ValueError: Ожидалось положительное число, получено: -3
>>>
>>> assert 2 + 2 == 4, "Математика сломалась"   # ок
>>> assert 2 + 2 == 5, "Математика сломалась"
AssertionError: Математика сломалась
```

## Резюме

- `raise ТипИсключения("сообщение")` — вызывает исключение вручную
- `raise` без аргументов — повторно поднимает текущее исключение (re-raise)
- `raise NewError(...) from original` — цепочка исключений с явной причиной
- `raise NewError(...) from None` — скрывает оригинальное исключение
- Выбирайте подходящий тип исключения — `ValueError`, `TypeError`, `RuntimeError` и др.
- `assert условие, "сообщение"` — проверка инвариантов, отключается с флагом `-O`
- `assert` — для отладки и документирования предположений; `raise` — для продакшн-валидации

---

[[08_3_exception_hierarchy|← 🌳 Иерархия исключений]] | [[08_5_custom_exceptions|🏗️ Собственные исключения →]]
