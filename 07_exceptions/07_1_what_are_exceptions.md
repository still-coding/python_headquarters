[[07_0_intro|← 🚨 Исключения]] | [[07_2_try_except|🛡️ `try/except` →]]

---
# 💥 Что такое исключения
```toc
minLevel: 2
```

## Синтаксические ошибки и исключения

В Python есть два вида проблем при работе с кодом.

**Синтаксические ошибки** — код написан неправильно, Python не может его разобрать и не запустит вообще:

```python
if x = 5:      # SyntaxError: нужно ==, а не =
    print(x)

def foo(       # SyntaxError: незакрытая скобка
```

**Исключения** — код синтаксически верен, но при выполнении возникла нештатная ситуация:

```python
int("abc")        # ValueError
1 / 0             # ZeroDivisionError
[1, 2, 3][99]     # IndexError
```

Синтаксические ошибки нужно просто исправить в коде. Исключения — это то, с чем можно и нужно работать во время выполнения программы.

## Что такое исключение

**Исключение** — это объект, который Python создаёт когда нормальный поток выполнения невозможно продолжить. Он прерывает выполнение текущего кода и «поднимается» вверх по стеку вызовов в поисках обработчика.

Важно: исключение — это **механизм сигнализации**, а не обязательно признак ошибки. Нештатная ситуация бывает двух видов:

- **Ошибка** — что-то пошло не так: деление на ноль, неверный тип, файл не найден
- **Штатный сигнал** — исключение используется как часть протокола: конец итерации, прерывание пользователем

```python
# Ошибка — программист передал неверные данные
int("abc")   # ValueError

# Штатный сигнал — итерация закончилась, это нормально
my_iter = iter([1, 2])
next(my_iter)  # 1
next(my_iter)  # 2
next(my_iter)  # StopIteration — не ошибка, просто конец
```

Цикл `for` перехватывает `StopIteration` автоматически — поэтому он просто останавливается, а не падает с ошибкой.

## Исключения — это классы

Исключения — это обычные классы Python. `ValueError`, `TypeError`, `IndexError` — классы, унаследованные от `Exception`. Когда исключение возникает, Python создаёт экземпляр этого класса:

```python
e = ValueError("неверное значение")
print(type(e))    # <class 'ValueError'>
print(e)          # неверное значение
print(e.args)     # ('неверное значение',)
```

Подробнее об иерархии классов исключений — в файле [[07_3_exception_hierarchy|Иерархия исключений]].

## Как читать Traceback

Когда исключение не перехвачено — Python печатает **Traceback** (трассировку стека). Умение читать его — важный навык.

```python
def divide(a, b):
    return a / b

def calculate(x):
    return divide(x, 0)

calculate(10)
```

```
Traceback (most recent call last):
  File "main.py", line 7, in <module>
    calculate(10)
  File "main.py", line 5, in calculate
    return divide(x, 0)
  File "main.py", line 2, in divide
    return a / b
ZeroDivisionError: division by zero
```

Читаем Traceback **снизу вверх**:

```
ZeroDivisionError: division by zero    ← 1. Тип и сообщение — что случилось
  File "main.py", line 2, in divide    ← 2. Где именно — файл, строка, функция
    return a / b                       ← 3. Конкретная строка кода
  File "main.py", line 5, in calculate ← 4. Кто вызвал эту функцию
  File "main.py", line 7, in <module>  ← 5. С чего всё началось
```

Самая важная строка — последняя: тип исключения и сообщение. Всё остальное — путь к месту где оно возникло.

## Исключение прерывает выполнение

Когда возникает исключение — Python немедленно прекращает выполнение текущего кода:

```python
def step_three():
    raise ValueError("Исключение здесь")
    print("Это не выполнится")       # ← никогда

def step_two():
    step_three()
    print("Это тоже не выполнится")  # ← никогда

def step_one():
    step_two()
    print("И это тоже")              # ← никогда

step_one()
# ValueError: Исключение здесь
```

Если обработчик не найден нигде в стеке вызовов — программа завершается и Python печатает Traceback.

## Распространённые исключения

```python
# ValueError — значение правильного типа, но неподходящее
int("abc")             # ValueError: invalid literal for int()

# TypeError — операция неприменима к данному типу
"hello" + 5            # TypeError: can only concatenate str (not "int") to str

# IndexError — индекс за пределами последовательности
[1, 2, 3][10]          # IndexError: list index out of range

# KeyError — ключ отсутствует в словаре
{"a": 1}["b"]          # KeyError: 'b'

# AttributeError — атрибут не существует у объекта
"hello".push("!")      # AttributeError: 'str' object has no attribute 'push'

# NameError — имя не определено в текущей области видимости
print(undefined_var)   # NameError: name 'undefined_var' is not defined

# ZeroDivisionError — деление на ноль
10 / 0                 # ZeroDivisionError: division by zero

# FileNotFoundError — файл не найден
open("no_such_file.txt")   # FileNotFoundError: No such file or directory

# ImportError — модуль не найден
import no_such_module      # ModuleNotFoundError: No module named '...'
```

## 🔎 Проверка на ходу

```python
>>> try:
...     int("abc")
... except ValueError as e:
...     print(type(e))
...     print(e)
...     print(e.args)
...
<class 'ValueError'>
invalid literal for int() with base 10: 'abc'
("invalid literal for int() with base 10: 'abc'",)
```

## Резюме

- **Синтаксические ошибки** — код неверно написан, Python не запустит его вообще
- **Исключение** — механизм сигнализации о нештатной ситуации во время выполнения
- Исключение — не обязательно ошибка: бывают штатные исключения-сигналы (`StopIteration`)
- Исключения — это обычные классы Python, унаследованные от `Exception`
- **Traceback** читается снизу вверх: тип и сообщение → место → цепочка вызовов
- Исключение прерывает выполнение и поднимается вверх по стеку до обработчика
- Если обработчик не найден — программа завершается с Traceback

---

[[07_0_intro|← 🚨 Исключения]] | [[07_2_try_except|🛡️ `try/except` →]]
