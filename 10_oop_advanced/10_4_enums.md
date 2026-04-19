[[10_3_metaclasses|← 🧬 Метаклассы]] | [[10_5_design_patterns|🎨 Паттерны проектирования →]]

---
# 🏷️ Перечисления (`Enum`)
```toc
minLevel: 2
```

## Проблема: «магические» константы

Часто в коде встречаются группы связанных значений, которые должны быть ограниченным набором:

```python
# Статус заказа
status = "pending"          # или "paid", или "shipped", или "cancelled"

# Приоритет задачи
priority = 1                # 1 — высокий, 2 — средний, 3 — низкий

# Цвета UI
color = "red"               # или "green", или "blue"
```

Такой подход работает, но создаёт проблемы:

- ❌ Опечатка `"pendign"` → молчаливая ошибка, никто не заметит
- ❌ `priority = 42` — синтаксически валидно, смыслово бессмысленно
- ❌ IDE не подсказывает доступные значения
- ❌ При переименовании нужно искать строку по всему проекту
- ❌ Неясно, откуда взялся список «допустимых» значений

**`Enum`** решает все эти проблемы: набор допустимых значений становится частью объявления типа.

## Базовый `Enum`

```python
from enum import Enum


class Status(Enum):
    PENDING = "pending"
    PAID = "paid"
    SHIPPED = "shipped"
    CANCELLED = "cancelled"


# Использование
order_status = Status.PAID

print(order_status)              # Status.PAID
print(order_status.name)         # 'PAID'    — имя члена
print(order_status.value)        # 'paid'    — значение

# Сравнение
if order_status == Status.PAID:
    print("Заказ оплачен")

# Опечатка сразу ломает программу
Status.PENDIGN      # AttributeError — IDE подсветит и Python не даст запуститься
```

Каждый член `Enum` — это **единственный** объект своего типа. Два обращения к `Status.PAID` возвращают один и тот же объект, поэтому корректно сравнивать через `is` (хотя `==` тоже работает).

## `auto()` — не хочу придумывать значения

Часто значения членов не важны сами по себе — важен только факт «эти четыре статуса существуют и они разные». Для таких случаев есть `auto()`:

```python
from enum import Enum, auto


class Color(Enum):
    RED = auto()
    GREEN = auto()
    BLUE = auto()


print(Color.RED.value)       # 1
print(Color.GREEN.value)     # 2
print(Color.BLUE.value)      # 3
```

`auto()` подставит следующее целое число. Конкретные числа — не твоя забота, важна лишь разница между членами.

## Итерация и поиск

```python
class Status(Enum):
    PENDING = "pending"
    PAID = "paid"
    SHIPPED = "shipped"


# Перебрать все члены
for status in Status:
    print(status.name, "→", status.value)


# Поиск по значению
Status("paid")               # Status.PAID

# Поиск по имени
Status["PAID"]               # Status.PAID

# Неизвестное значение — ValueError
Status("unknown")            # ValueError

# Количество членов
len(Status)                  # 3

# Проверка вхождения (Python 3.12+)
Status.PAID in Status        # True
```

Особенно полезно `Status(value)` — когда тебе приходит строка из БД или API, и нужно получить объект `Enum`:

```python
def handle_order(data: dict):
    status = Status(data["status"])      # автоматическая валидация
    if status == Status.PAID:
        ...
```

Если в `data["status"]` окажется не-статус — `ValueError` вместо тихого бага.

## Методы внутри `Enum`

Члены `Enum` — полноценные объекты, и в самом классе можно определять методы:

```python
class Priority(Enum):
    LOW = 1
    MEDIUM = 2
    HIGH = 3

    def is_urgent(self) -> bool:
        return self == Priority.HIGH

    def label(self) -> str:
        return {
            Priority.LOW: "🟢 Низкий",
            Priority.MEDIUM: "🟡 Средний",
            Priority.HIGH: "🔴 Высокий",
        }[self]


task_priority = Priority.HIGH
print(task_priority.label())         # 🔴 Высокий
print(task_priority.is_urgent())     # True
```

Это превращает `Enum` из «набора констант» в полноценный тип с поведением.

## Специализированные варианты

### `IntEnum` — перечисление на базе `int`

Члены `IntEnum` одновременно являются числами — их можно сравнивать с обычными `int` и использовать в арифметике:

```python
from enum import IntEnum


class HTTPStatus(IntEnum):
    OK = 200
    NOT_FOUND = 404
    INTERNAL_ERROR = 500


HTTPStatus.OK == 200         # True — сравнение с int работает
HTTPStatus.OK < HTTPStatus.NOT_FOUND    # True

# Можно использовать как число
code = HTTPStatus.NOT_FOUND + 1       # 405
```

Удобно для интероперабельности со старым кодом, который ожидает числа.

### `StrEnum` — перечисление на базе `str` (Python 3.11+)

Аналогично, но для строк:

```python
from enum import StrEnum


class Env(StrEnum):
    DEV = "dev"
    STAGING = "staging"
    PROD = "prod"


Env.PROD == "prod"           # True
f"Running in {Env.DEV}"      # 'Running in dev' — работает как обычная строка
```

До появления `StrEnum` приходилось наследоваться вручную: `class Env(str, Enum): ...`. С 3.11 это стало встроенным.

### `Flag` — комбинации через побитовые операции

Когда значения можно комбинировать (права доступа, настройки, состояния):

```python
from enum import Flag, auto


class Permission(Flag):
    READ = auto()
    WRITE = auto()
    EXECUTE = auto()


user_perms = Permission.READ | Permission.WRITE

print(user_perms)                           # Permission.READ|WRITE
print(Permission.WRITE in user_perms)       # True
print(Permission.EXECUTE in user_perms)     # False

# Добавить право
user_perms |= Permission.EXECUTE

# Убрать право
user_perms &= ~Permission.WRITE
```

`auto()` в `Flag` подставляет степени двойки (`1, 2, 4, 8, ...`), чтобы биты не пересекались.

## Уникальность значений: `@unique`

По умолчанию `Enum` разрешает алиасы — разные имена с одинаковыми значениями:

```python
class Status(Enum):
    PAID = "paid"
    COMPLETED = "paid"       # алиас к PAID


Status.COMPLETED is Status.PAID    # True — это один и тот же член
```

Если такое поведение нежелательно — декоратор `@unique` заставит бросить ошибку при дубликатах:

```python
from enum import Enum, unique


@unique
class Status(Enum):
    PAID = "paid"
    COMPLETED = "paid"       # ValueError при импорте модуля
```

## Когда использовать `Enum`

**Используй `Enum`, если:**

- 🟢 Есть фиксированный замкнутый набор значений
- 🟢 Значения связаны по смыслу (статусы, типы, категории, роли)
- 🟢 Нужна защита от опечаток и «мусорных» значений
- 🟢 Хочется добавить методы, связанные со значением

**Не используй `Enum`, если:**

- 🔴 Значения постоянно приходят извне и заранее неизвестны (тогда просто строки)
- 🔴 Набор активно меняется в рантайме (БД, конфиг)
- 🔴 Достаточно одной-двух констант — `MAX_RETRIES = 3` не нуждается в `Enum`

## Как это связано с метаклассами

`Enum` внутри устроен через метакласс `EnumMeta` (или `EnumType` в новых версиях). Именно он:

- перехватывает объявление тела класса
- превращает атрибуты в члены-синглтоны
- запрещает создание новых членов после объявления
- обеспечивает итерацию, поиск по значению, `len()`

Это один из немногих случаев, когда метакласс действительно оправдан — и ты используешь его, даже не зная об этом. Из [[10_3_metaclasses|предыдущего файла]] должно стать ясно, как такая магия в принципе возможна.

## 💡 Главное

```python
from enum import Enum, auto, IntEnum, StrEnum, Flag, unique


# Базовый Enum — именованные константы
class Status(Enum):
    PENDING = "pending"
    PAID = "paid"

Status.PAID.name       # 'PAID'
Status.PAID.value      # 'paid'
Status("paid")         # Status.PAID — поиск по значению


# auto() — не думать о значениях
class Color(Enum):
    RED = auto()
    GREEN = auto()


# Методы внутри Enum — полноценный тип с поведением
class Priority(Enum):
    HIGH = 3
    def is_urgent(self): return self == Priority.HIGH


# Специальные базовые классы:
# IntEnum — совместимость с int
# StrEnum — совместимость со str (Python 3.11+)
# Flag    — побитовые комбинации через | & ~


# @unique — запрет алиасов


# Используй Enum для замкнутых наборов смысловых значений
# (статусы, типы, роли) — защита от опечаток и "мусорных" данных.
```

---

[[10_3_metaclasses|← 🧬 Метаклассы]] | [[10_5_design_patterns|🎨 Паттерны проектирования →]]
