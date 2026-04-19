[[08_1_what_are_exceptions|← 💥 Что такое исключения]] | [[08_3_exception_hierarchy|🌳 Иерархия исключений →]]

---
# 🛡️ `try/except`
```toc
minLevel: 2
```

## Базовый синтаксис

```python
try:
    # Код, который может вызвать исключение
    result = 10 / 0
except ZeroDivisionError:
    # Что делать если возникло это исключение
    print("Деление на ноль!")
```

Python выполняет блок `try`. Если исключение возникло — выполнение `try` прерывается и управление переходит в соответствующий `except`. Если исключения не было — `except` пропускается.

## Перехват конкретного исключения

Всегда указывайте конкретный тип исключения — не ловите всё подряд:

```python
def get_number(text):
    try:
        return int(text)
    except ValueError:
        print(f"Не удалось преобразовать '{text}' в число")
        return None


print(get_number("42"))    # 42
print(get_number("abc"))   # Не удалось преобразовать 'abc' в число → None
```

## Доступ к объекту исключения

Через `as` получаем сам объект исключения — с сообщением и деталями:

```python
try:
    result = int("abc")
except ValueError as e:
    print(f"Тип: {type(e).__name__}")   # Тип: ValueError
    print(f"Сообщение: {e}")            # Сообщение: invalid literal...
    print(f"Аргументы: {e.args}")       # Аргументы: ('invalid literal...',)
```

## Несколько блоков `except`

Можно обрабатывать разные исключения по-разному:

```python
def read_element(lst, index):
    try:
        return lst[index]
    except IndexError:
        print(f"Индекс {index} за пределами списка")
        return None
    except TypeError:
        print(f"Индекс должен быть числом, получен: {type(index).__name__}")
        return None


read_element([1, 2, 3], 10)    # Индекс 10 за пределами списка
read_element([1, 2, 3], "a")   # Индекс должен быть числом, получен: str
```

### Несколько исключений в одном блоке

Если реакция одинакова — перечислите через запятую в кортеже:

```python
try:
    value = int(input("Число: "))
    result = 100 / value
except (ValueError, ZeroDivisionError) as e:
    print(f"Неверный ввод: {e}")
```

## `else` — если исключения не было

Блок `else` выполняется только если в `try` **не возникло** исключений:

```python
def divide(a, b):
    try:
        result = a / b
    except ZeroDivisionError:
        print("Деление на ноль!")
    else:
        print(f"Результат: {result}")   # Только если всё прошло успешно
        return result


divide(10, 2)   # Результат: 5.0
divide(10, 0)   # Деление на ноль!
```

`else` полезен чтобы чётко отделить «защищённый» код от кода который выполняется только при успехе.

## `finally` — выполняется всегда

Блок `finally` выполняется **всегда** — и при исключении, и без него. Используется для освобождения ресурсов:

```python
def read_file(path):
    f = None
    try:
        f = open(path)
        return f.read()
    except FileNotFoundError:
        print(f"Файл не найден: {path}")
        return None
    finally:
        if f:
            f.close()   # Выполнится в любом случае
            print("Файл закрыт")
```

`finally` выполняется даже если в `except` есть `return`:

```python
def risky():
    try:
        return "из try"
    finally:
        print("finally выполнился")   # Выполнится перед return!

print(risky())
# finally выполнился
# из try
```

## Полная структура

```python
try:
    # Код который может вызвать исключение
    pass
except SomeError:
    # Реакция на конкретное исключение
    pass
except (AnotherError, YetAnotherError) as e:
    # Несколько исключений + доступ к объекту
    pass
else:
    # Выполняется если исключения НЕ было
    pass
finally:
    # Выполняется всегда
    pass
```

Обязательны только `try` и хотя бы один `except` (или `finally`).

## Порядок блоков `except` важен

Python проверяет блоки `except` по очереди и останавливается на первом совпадении. Более конкретные исключения ставьте **выше** более общих:

```python
# ❌ Плохо — общий Exception перехватит всё, до ValueError никогда не дойдёт
try:
    int("abc")
except Exception:
    print("Какая-то ошибка")
except ValueError:
    print("Это никогда не выполнится")


# ✅ Правильно — от конкретного к общему
try:
    int("abc")
except ValueError:
    print("Неверное значение")
except Exception as e:
    print(f"Неожиданная ошибка: {e}")
```

## Не злоупотребляйте `except Exception`

Ловить все исключения подряд — плохая практика: можно случайно скрыть реальные ошибки.

```python
# ❌ Плохо — маскируем любые проблемы
try:
    result = complex_operation()
except Exception:
    pass   # Тихо проглатываем ошибку — очень опасно!

# ✅ Лучше — ловим только то, что ожидаем
try:
    result = complex_operation()
except ValueError as e:
    logger.warning(f"Неверные данные: {e}")
    result = default_value
```

## 🎯 Пример с реальной задачей

Надёжная функция чтения числа от пользователя:

```python
def input_positive_number(prompt):
    """Запрашивает положительное число пока пользователь не введёт корректное"""
    while True:
        try:
            value = float(input(prompt))
        except ValueError:
            print("Введите числовое значение")
            continue

        if value <= 0:
            print("Число должно быть положительным")
            continue

        return value


radius = input_positive_number("Введите радиус: ")
print(f"Площадь: {3.14159 * radius ** 2:.2f}")
```

## 🔎 Проверка на ходу

```python
>>> def safe_divide(a, b):
...     try:
...         result = a / b
...     except ZeroDivisionError:
...         return None
...     else:
...         return result
...     finally:
...         print("Операция завершена")
...
>>> safe_divide(10, 2)
Операция завершена
5.0
>>> safe_divide(10, 0)
Операция завершена
>>>
```

## Резюме

- `try` — код который может вызвать исключение
- `except ТипИсключения` — реакция на конкретное исключение
- `as e` — доступ к объекту исключения
- Несколько `except` — для разных исключений; порядок от конкретного к общему
- `else` — выполняется только если исключения **не было**
- `finally` — выполняется **всегда**, используется для освобождения ресурсов
- Не ловите `Exception` без необходимости — это маскирует ошибки

---

[[08_1_what_are_exceptions|← 💥 Что такое исключения]] | [[08_3_exception_hierarchy|🌳 Иерархия исключений →]]
