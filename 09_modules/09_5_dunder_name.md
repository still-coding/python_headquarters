[[09_4_relative_imports|← ↔️ Абсолютные и относительные импорты]] | [[09_6_venv_and_pip|📥 Виртуальные окружения, pip и uv →]]

---
# 🏁 `__name__ == "__main__"` и `python -m`
```toc
minLevel: 2
```

## Зачем вообще этот блок в конце файлов

Почти в каждом Python-проекте встречается такой хвост:

```python
def main():
    ...

if __name__ == "__main__":
    main()
```

Эта конструкция выглядит как ритуал, но на самом деле решает конкретную проблему: **один и тот же файл может быть либо запущен как программа, либо импортирован как модуль**, и вести себя в этих случаях нужно по-разному.

## Что такое `__name__`

`__name__` — специальная переменная, которую Python автоматически создаёт для каждого модуля. Её значение зависит от того, **как модуль попал в исполнение**:

- **Запущен напрямую** (`python my_file.py`) → `__name__ == "__main__"`
- **Импортирован** (`import my_file`) → `__name__ == "my_file"`

```python
# greeter.py
print(f"Модуль загружен. __name__ = {__name__}")

def greet(name):
    return f"Привет, {name}!"
```

Два разных запуска:

```bash
$ python greeter.py
Модуль загружен. __name__ = __main__
```

```python
>>> import greeter
Модуль загружен. __name__ = greeter
```

## Зачем это нужно

Представь модуль с функциями и «демонстрацией» их работы:

```python
# calculator.py

def add(a, b):
    return a + b

def multiply(a, b):
    return a * b

# Тестовый вызов для проверки
print(add(2, 3))        # 5
print(multiply(4, 5))   # 20
```

Запускаем напрямую — печатает `5` и `20`. Всё хорошо.

Теперь другой файл хочет использовать эти функции:

```python
# main.py
from calculator import add

result = add(10, 20)
print(result)
```

```bash
$ python main.py
5           ← откуда это?!
20          ← и это?!
30
```

Импорт `calculator` **выполнил его целиком**, включая тестовые `print`. Получился побочный эффект, которого никто не ждал.

### Решение — `if __name__ == "__main__":`

```python
# calculator.py

def add(a, b):
    return a + b

def multiply(a, b):
    return a * b

if __name__ == "__main__":
    # Этот блок выполнится ТОЛЬКО при прямом запуске
    print(add(2, 3))
    print(multiply(4, 5))
```

Теперь:

- `python calculator.py` → печатает 5 и 20 (работает как программа)
- `from calculator import add` → не печатает ничего (работает как модуль)

## Типичные применения

### 1. Точка входа в программу

Стандартный паттерн для любого скрипта:

```python
# app.py
import sys

def main():
    args = sys.argv[1:]
    print(f"Получены аргументы: {args}")
    # ... основная логика

if __name__ == "__main__":
    main()
```

Почему оборачивать в `main()`, а не писать логику прямо в блоке `if`:

- 🟢 Локальные переменные не утекают в глобальное пространство модуля
- 🟢 Функцию `main()` можно вызвать из теста или из другого скрипта
- 🟢 Легче читается

### 2. Быстрый self-test модуля

```python
# string_utils.py

def reverse(s):
    return s[::-1]

def is_palindrome(s):
    return s == reverse(s)

if __name__ == "__main__":
    # Простая проверка, что всё работает
    assert reverse("abc") == "cba"
    assert is_palindrome("radar")
    assert not is_palindrome("hello")
    print("Все проверки прошли")
```

Для серьёзного тестирования есть `pytest` и `unittest`, но для быстрой проверки «на коленке» такой блок удобен.

### 3. Демо использования

```python
# my_library.py

class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        return f"{self.name} издаёт звук"

if __name__ == "__main__":
    # Пример использования — запусти файл, чтобы увидеть
    dog = Animal("Рекс")
    print(dog.speak())
```

## Запуск модуля через `python -m`

У Python есть два способа запустить код:

```bash
# 1. Как скрипт по пути к файлу
python path/to/my_module.py

# 2. Как модуль по «точечному» имени
python -m path.to.my_module
```

Второй способ — ключ к запуску кода внутри пакетов.

### Почему `python file.py` часто ломается в пакетах

Структура проекта:

```
my_project/
├── myapp/
│   ├── __init__.py
│   ├── main.py
│   └── utils.py
```

```python
# myapp/main.py
from .utils import helper       # относительный импорт
```

Пробуем запустить:

```bash
$ python myapp/main.py
ImportError: attempted relative import with no known parent package
```

**Что произошло:** когда Python запускает файл по пути, он не видит его как часть пакета. `myapp/main.py` становится «корневым скриптом», и точки в `from .utils` не к чему применить.

### Решение — `python -m`

Запускаем из корня проекта (`my_project/`):

```bash
$ python -m myapp.main
```

Теперь Python знает: «запускаем `main` из пакета `myapp`». Относительные импорты работают, потому что у модуля есть родительский пакет.

### Что именно делает `python -m`

1. Добавляет **текущую папку** в `sys.path`
2. Находит указанный модуль как если бы его импортировали
3. Запускает его с `__name__ == "__main__"`

Ключевой эффект — файл одновременно «запущен как программа» (`__name__ == "__main__"`) **и** «является модулем пакета» (относительные импорты работают).

### Примеры `-m` в реальной жизни

Многие стандартные и сторонние инструменты запускаются именно так:

```bash
python -m venv .venv              # создать виртуальное окружение
python -m pip install requests    # установить пакет
python -m http.server 8000        # простой HTTP-сервер
python -m json.tool data.json     # красиво отформатировать JSON
python -m unittest                # запустить тесты
python -m pytest                  # запустить pytest (если установлен)
```

## `__main__.py` — запуск пакета целиком

Если в пакете есть файл `__main__.py`, можно запустить **весь пакет**:

```
mytool/
├── __init__.py
├── __main__.py         ← специальный файл
├── core.py
└── cli.py
```

```python
# mytool/__main__.py
from mytool.cli import run

if __name__ == "__main__":
    run()
```

Теперь:

```bash
$ python -m mytool
```

Python найдёт `mytool/__main__.py` и выполнит его. Так устроены `pip`, `venv`, `http.server` и многие CLI-утилиты.

## 💡 Главное

```python
# __name__ — автоматическая переменная, которую Python создаёт
# для каждого модуля:
# - запустили файл напрямую → __name__ == "__main__"
# - импортировали файл       → __name__ == имя модуля

# Стандартный шаблон точки входа:
def main():
    # основная логика
    pass

if __name__ == "__main__":
    main()

# Два способа запустить код:
python file.py              # как скрипт
python -m package.module    # как модуль пакета

# Используй -m, когда:
# - в модуле относительные импорты
# - модуль внутри пакета
# - запускаешь стандартные инструменты (pip, venv, http.server)

# __main__.py в пакете позволяет:
python -m mypackage         # запустить пакет целиком
```

---

[[09_4_relative_imports|← ↔️ Абсолютные и относительные импорты]] | [[09_6_venv_and_pip|📥 Виртуальные окружения, pip и uv →]]
