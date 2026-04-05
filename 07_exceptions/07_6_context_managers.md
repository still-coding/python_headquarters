[[07_5_custom_exceptions|← 🏗️ Собственные исключения]]

---
# 🔑 Контекстные менеджеры
```toc
minLevel: 2
```

## Проблема: гарантированное освобождение ресурсов

Работая с файлами, соединениями и другими ресурсами нужно гарантировать их освобождение — даже если возникло исключение:

```python
# ❌ Опасно — если возникнет исключение, файл не закроется
f = open("data.txt")
data = f.read()   # Что если здесь исключение?
f.close()         # Этот код может не выполниться!

# ✅ Безопасно — файл закроется в любом случае
with open("data.txt") as f:
    data = f.read()
# Здесь файл уже закрыт — автоматически
```

`with` — это синтаксис для работы с контекстными менеджерами.

## Как работает `with`

```python
with выражение as переменная:
    # тело блока
```

При входе в блок `with` вызывается метод `__enter__` — он возвращает объект который попадает в `as`. При выходе из блока — в любом случае, с исключением или без — вызывается `__exit__`.

```python
with open("file.txt") as f:   # __enter__ открывает файл и возвращает его
    data = f.read()
# __exit__ закрывает файл — даже если было исключение
```

`as` необязателен — если возвращаемый объект не нужен:

```python
with lock:      # Захватить блокировку
    do_work()   # __exit__ освободит блокировку
```

## `__enter__` и `__exit__`

Любой класс с этими методами может быть контекстным менеджером:

```python
class ManagedFile:
    def __init__(self, path, mode="r"):
        self.path = path
        self.mode = mode
        self.file = None

    def __enter__(self):
        print(f"Открываем {self.path}")
        self.file = open(self.path, self.mode)
        return self.file        # Это попадёт в переменную после as

    def __exit__(self, exc_type, exc_val, exc_tb):
        print(f"Закрываем {self.path}")
        if self.file:
            self.file.close()
        return False            # False — не подавлять исключение


with ManagedFile("data.txt") as f:
    data = f.read()
# Открываем data.txt
# Закрываем data.txt  ← выполнится в любом случае
```

### Параметры `__exit__`

`__exit__` получает информацию об исключении если оно возникло:

```python
def __exit__(self, exc_type, exc_val, exc_tb):
    # exc_type  — тип исключения (None если исключения не было)
    # exc_val   — объект исключения
    # exc_tb    — traceback объект

    if exc_type is not None:
        print(f"Исключение: {exc_type.__name__}: {exc_val}")

    # return True  — подавить исключение (не распространять дальше)
    # return False — распространить исключение дальше (обычное поведение)
    return False
```

Подавление исключения — редкий случай. Обычно возвращайте `False`.

## `contextmanager` — проще через генератор

Модуль `contextlib` предоставляет декоратор `@contextmanager` — позволяет создать контекстный менеджер через генераторную функцию без написания класса:

```python
from contextlib import contextmanager


@contextmanager
def managed_file(path, mode="r"):
    print(f"Открываем {path}")
    f = open(path, mode)
    try:
        yield f             # Всё до yield — это __enter__
    finally:
        f.close()           # Всё после yield — это __exit__
        print(f"Закрываем {path}")


with managed_file("data.txt") as f:
    data = f.read()
```

Логика простая: код до `yield` выполняется при входе в `with`, `yield` возвращает значение для `as`, код после `yield` выполняется при выходе. `try/finally` гарантирует выполнение кода очистки даже при исключении.

## Практические примеры

### Таймер

```python
from contextlib import contextmanager
import time


@contextmanager
def timer(label=""):
    start = time.time()
    try:
        yield
    finally:
        elapsed = time.time() - start
        print(f"{label}: {elapsed:.3f} сек")


with timer("Загрузка данных"):
    time.sleep(0.5)   # имитация работы
# Загрузка данных: 0.501 сек
```

### Временная директория

```python
from contextlib import contextmanager
import os
import tempfile
import shutil


@contextmanager
def temp_directory():
    """Создаёт временную директорию и удаляет её после использования"""
    path = tempfile.mkdtemp()
    try:
        yield path
    finally:
        shutil.rmtree(path)


with temp_directory() as tmpdir:
    # Работаем с временными файлами
    with open(os.path.join(tmpdir, "temp.txt"), "w") as f:
        f.write("временные данные")
# Директория удалена автоматически
```

### Транзакция базы данных

```python
from contextlib import contextmanager


@contextmanager
def transaction(connection):
    """Управляет транзакцией БД — commit при успехе, rollback при ошибке"""
    try:
        yield connection
        connection.commit()
        print("Транзакция зафиксирована")
    except Exception:
        connection.rollback()
        print("Транзакция отменена")
        raise   # Передаём исключение дальше


with transaction(db_connection) as conn:
    conn.execute("INSERT INTO users VALUES (...)")
    conn.execute("UPDATE balance SET amount = ...")
# Если всё хорошо — commit, если ошибка — rollback
```

## Несколько контекстных менеджеров

Можно объединить несколько в одном `with`:

```python
# Два менеджера в одной строке
with open("input.txt") as fin, open("output.txt", "w") as fout:
    fout.write(fin.read())

# Эквивалентно вложенным with
with open("input.txt") as fin:
    with open("output.txt", "w") as fout:
        fout.write(fin.read())
```

## `contextlib.suppress` — подавить конкретное исключение

```python
from contextlib import suppress


# ❌ Многословно
try:
    os.remove("temp.txt")
except FileNotFoundError:
    pass

# ✅ Лаконично
with suppress(FileNotFoundError):
    os.remove("temp.txt")
```

## 🎯 Пример с реальной задачей

```python
from contextlib import contextmanager


@contextmanager
def db_connection(host, port):
    """Управляет подключением к базе данных"""
    print(f"Подключение к {host}:{port}")
    conn = {"host": host, "port": port, "connected": True}   # имитация
    try:
        yield conn
    except Exception as e:
        print(f"Ошибка при работе с БД: {e}")
        raise
    finally:
        conn["connected"] = False
        print(f"Отключение от {host}:{port}")


with db_connection("localhost", 5432) as conn:
    print(f"Выполняем запрос через {conn['host']}")
# Подключение к localhost:5432
# Выполняем запрос через localhost
# Отключение от localhost:5432
```

## 🔎 Проверка на ходу

```python
>>> from contextlib import contextmanager
>>>
>>> @contextmanager
... def greeting(name):
...     print(f"Привет, {name}!")
...     yield name.upper()
...     print(f"Пока, {name}!")
...
>>> with greeting("Анна") as name:
...     print(f"Внутри блока: {name}")
...
Привет, Анна!
Внутри блока: АННА
Пока, Анна!
```

## Резюме

- `with` гарантирует выполнение кода очистки — даже при исключении
- `__enter__` — выполняется при входе в `with`, возвращает объект для `as`
- `__exit__` — выполняется при выходе, получает информацию об исключении
- `__exit__` возвращает `True` чтобы подавить исключение, `False` — передать дальше
- `@contextmanager` — создать менеджер через генератор: код до `yield` — вход, после — выход
- Несколько менеджеров в одном `with` — через запятую
- `contextlib.suppress(Error)` — лаконичная замена `try/except: pass`

---

[[07_5_custom_exceptions|← 🏗️ Собственные исключения]]
