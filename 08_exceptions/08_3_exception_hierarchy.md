[[08_2_try_except|← 🛡️ `try/except`]] | [[08_4_raise_and_assert|🔺 `raise` и `assert` →]]

---
# 🌳 Иерархия исключений
```toc
minLevel: 2
```

## Все исключения — это классы

Исключения в Python организованы в иерархию классов. Каждое исключение наследует от более общего — точно так же как обычные классы.

```python
# ValueError — это подкласс Exception
print(issubclass(ValueError, Exception))     # True
print(issubclass(ValueError, BaseException)) # True

# Экземпляр ValueError — это тоже Exception
try:
    int("abc")
except Exception as e:
    print(isinstance(e, ValueError))   # True
    print(isinstance(e, Exception))    # True
```

Это значит что `except Exception` поймает и `ValueError`, и `TypeError`, и любое другое исключение унаследованное от `Exception`.

## Дерево встроенных исключений

```
BaseException
├── SystemExit              — sys.exit()
├── KeyboardInterrupt       — Ctrl+C
├── GeneratorExit           — закрытие генератора
└── Exception               — все «обычные» исключения
    ├── StopIteration       — конец итерации
    ├── ArithmeticError
    │   ├── ZeroDivisionError
    │   ├── OverflowError
    │   └── FloatingPointError
    ├── LookupError
    │   ├── IndexError
    │   └── KeyError
    ├── TypeError
    ├── ValueError
    │   └── UnicodeError
    ├── AttributeError
    ├── NameError
    ├── OSError
    │   ├── FileNotFoundError
    │   ├── PermissionError
    │   ├── TimeoutError
    │   └── ...
    ├── RuntimeError
    │   └── RecursionError
    └── ...
```

## `BaseException` vs `Exception`

`BaseException` — корень всей иерархии. Прямые наследники `BaseException` (но не `Exception`) — особые:

```python
# KeyboardInterrupt — Ctrl+C, не наследует Exception
# ❌ Плохо — перехватываем Ctrl+C, пользователь не может остановить программу
try:
    while True:
        pass
except Exception:
    pass   # Ctrl+C не поймается — KeyboardInterrupt не Exception

# ❌ Очень плохо — ловим даже SystemExit и KeyboardInterrupt
try:
    while True:
        pass
except BaseException:
    pass   # Программу теперь не остановить!
```

**Правило:** всегда ловите `Exception` или конкретные подклассы — никогда `BaseException`.

## Группы исключений

### `LookupError` — ошибки поиска

Общий родитель для `IndexError` и `KeyError`. Удобно когда не важно, список это или словарь:

```python
def get_value(container, key):
    try:
        return container[key]
    except LookupError:
        return None   # Работает и для списков, и для словарей


print(get_value([1, 2, 3], 10))   # None
print(get_value({"a": 1}, "b"))   # None
```

### `ArithmeticError` — арифметические ошибки

Общий родитель для `ZeroDivisionError`, `OverflowError`, `FloatingPointError`:

```python
try:
    result = 10 / 0
except ArithmeticError as e:
    print(f"Арифметическая ошибка: {e}")
```

### `OSError` — ошибки операционной системы

Большая группа для работы с файлами, сетью, процессами:

```python
try:
    with open("no_file.txt") as f:
        data = f.read()
except FileNotFoundError:
    print("Файл не найден")
except PermissionError:
    print("Нет прав на чтение")
except OSError as e:
    print(f"Ошибка ОС: {e}")
```

## Как иерархия влияет на `except`

Более общий `except` поймает все подклассы. Это открывает два паттерна:

### Поймать группу

```python
try:
    data = my_dict[key][index]
except LookupError:
    print("Ключ или индекс не найден")
```

### От конкретного к общему

```python
try:
    process(data)
except ValueError:
    print("Неверное значение — знаем как обработать")
except TypeError:
    print("Неверный тип — знаем как обработать")
except Exception as e:
    print(f"Неожиданная ошибка: {e}")
    raise   # Повторно поднимаем — не знаем что делать
```

## 🎯 Пример с реальной задачей

Надёжная загрузка данных — обрабатываем разные уровни иерархии:

```python
import json


def load_config(path):
    """Загружает конфиг из JSON-файла"""
    try:
        with open(path) as f:
            data = json.load(f)
    except FileNotFoundError:
        print(f"Файл конфига не найден: {path}")
        return {}
    except PermissionError:
        print(f"Нет прав на чтение: {path}")
        return {}
    except json.JSONDecodeError as e:
        print(f"Неверный формат JSON: {e}")
        return {}
    except OSError as e:
        print(f"Ошибка при чтении файла: {e}")
        return {}
    else:
        print(f"Конфиг загружен: {len(data)} параметров")
        return data


config = load_config("settings.json")
```

## 🔎 Проверка на ходу

```python
>>> IndexError.__mro__
(<class 'IndexError'>, <class 'LookupError'>, <class 'Exception'>,
 <class 'BaseException'>, <class 'object'>)

>>> KeyError.__mro__
(<class 'KeyError'>, <class 'LookupError'>, <class 'Exception'>,
 <class 'BaseException'>, <class 'object'>)

>>> issubclass(FileNotFoundError, OSError)
True
>>> issubclass(ZeroDivisionError, ArithmeticError)
True
```

## Резюме

- Исключения организованы в иерархию классов — `except` ловит класс и все его подклассы
- `BaseException` — корень всей иерархии; `Exception` — родитель всех «обычных» исключений
- `KeyboardInterrupt` и `SystemExit` наследуют `BaseException` напрямую — не ловите их через `except Exception`
- Группы `LookupError`, `ArithmeticError`, `OSError` позволяют перехватывать целые семейства
- Порядок блоков `except` — от конкретного к общему
- `.__mro__` позволяет посмотреть цепочку наследования любого исключения

---

[[08_2_try_except|← 🛡️ `try/except`]] | [[08_4_raise_and_assert|🔺 `raise` и `assert` →]]
