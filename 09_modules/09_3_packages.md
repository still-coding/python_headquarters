[[09_2_how_import_works|← 🔍 Как работает импорт]] | [[09_4_relative_imports|↔️ Абсолютные и относительные импорты →]]

---
# 🗂️ Пакеты и `__init__.py`
```toc
minLevel: 2
```

## Что такое пакет?

**Пакет** — это папка, содержащая модули (`.py` файлы) и, как правило, специальный файл `__init__.py`. Пакеты позволяют группировать связанные модули в одну логическую единицу и создавать иерархию.

```
myapp/                    ← пакет
├── __init__.py           ← маркер пакета
├── database.py           ← модуль myapp.database
├── utils.py              ← модуль myapp.utils
└── models/               ← вложенный пакет myapp.models
    ├── __init__.py
    ├── user.py           ← модуль myapp.models.user
    └── product.py        ← модуль myapp.models.product
```

Обращение к модулям идёт через точки — они отражают структуру папок:

```python
import myapp.database
from myapp.models.user import User
from myapp.utils import format_date
```

## Простой пример: разбиваем один файл на пакет

Было — всё в одном файле:

```python
# shop.py (200 строк)
class User: ...
class Product: ...
class Order: ...
def connect_db(): ...
def send_email(): ...
def format_price(): ...
```

Стало — пакет:

```
shop/
├── __init__.py
├── models.py        # User, Product, Order
├── database.py      # connect_db
├── notifications.py # send_email
└── utils.py         # format_price
```

```python
# main.py
from shop.models import User, Product
from shop.database import connect_db

user = User("Иван")
conn = connect_db()
```

## Что такое `__init__.py`

`__init__.py` — это **файл, который выполняется при импорте пакета**. Он играет две роли:

1. **Исторически** — маркер того, что папка является пакетом
2. **Всегда** — код инициализации пакета

```python
# myapp/__init__.py
print("Пакет myapp загружается")

VERSION = "1.0"
```

```python
# main.py
import myapp
# Печатает: Пакет myapp загружается

print(myapp.VERSION)   # 1.0
```

> 💡 С Python 3.3 папка без `__init__.py` тоже может работать как пакет (это называется *namespace package*). Но для обычных случаев всегда создавай `__init__.py` — явно лучше, чем неявно.

### `__init__.py` может быть пустым

Самый частый вариант — пустой файл:

```python
# myapp/__init__.py
# (пусто)
```

Это нормально и этого достаточно, чтобы папка считалась пакетом.

## Зачем что-то писать в `__init__.py`

### Вариант 1: Упростить API пакета

```python
# shop/models.py
class User: ...
class Product: ...
```

```python
# shop/__init__.py
from shop.models import User, Product
```

Теперь пользователи пакета могут писать короче:

```python
# ❌ Без __init__.py
from shop.models import User

# ✅ С пробросом в __init__.py
from shop import User
```

Это удобно для небольших и средних пакетов — пользователю не надо знать внутреннюю структуру.

### Вариант 2: Константы и метаданные пакета

```python
# myapp/__init__.py
__version__ = "2.1.0"
__author__ = "Иван Петров"

DEFAULT_TIMEOUT = 30
```

```python
import myapp
print(myapp.__version__)   # 2.1.0
```

### Вариант 3: Инициализация ресурсов

```python
# myapp/__init__.py
import logging

# Настроим логгер при импорте пакета
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)
logger.info("myapp инициализирован")
```

> ⚠️ С инициализацией в `__init__.py` надо быть осторожным. Всё, что там написано, выполнится при **любом** импорте из пакета — даже если нужна всего одна функция. Не клади туда тяжёлые вычисления.

## Вложенные пакеты

Пакеты могут содержать другие пакеты — на любую глубину.

```
myapp/
├── __init__.py
├── api/
│   ├── __init__.py
│   ├── users.py
│   └── products.py
└── db/
    ├── __init__.py
    ├── models/
    │   ├── __init__.py
    │   └── user.py
    └── queries.py
```

Импорты отражают структуру:

```python
from myapp.api.users import get_user
from myapp.db.models.user import User
from myapp.db.queries import find_all
```

### Что выполняется при импорте вложенного модуля

При `from myapp.db.models.user import User` выполнятся **все** `__init__.py` по пути:

1. `myapp/__init__.py`
2. `myapp/db/__init__.py`
3. `myapp/db/models/__init__.py`
4. `myapp/db/models/user.py`

В таком порядке. Это важно помнить, если в `__init__.py` есть побочные эффекты (печать, настройка логгера, подключение к БД).

## Что такое `__all__`

Переменная `__all__` в `__init__.py` (или в любом модуле) определяет, что именно попадёт в звёздочный импорт `from package import *`.

```python
# shop/__init__.py
from shop.models import User, Product, Order
from shop.database import connect_db, disconnect_db

__all__ = ["User", "Product", "connect_db"]
```

```python
from shop import *
# Импортировались только User, Product, connect_db
# Order и disconnect_db — не импортировались
```

> 💡 `__all__` влияет **только** на `from ... import *`. При обычном `from shop import Order` всё работает как раньше — это просто соглашение «что входит в публичный API».

## Как Python находит пакеты

Ровно так же, как и обычные модули — по `sys.path` (см. [[09_2_how_import_works|Как работает импорт]]). Разница одна: когда Python находит в папке из `sys.path` папку с нужным именем, он проверяет, есть ли внутри `__init__.py`, и считает её пакетом.

```
/home/user/my_project/      ← в sys.path
├── main.py
└── shop/                   ← Python видит: это пакет
    ├── __init__.py
    └── models.py
```

## Типичные ошибки

### Забыл `__init__.py`

```
myapp/
├── database.py
└── utils.py                ← нет __init__.py
```

```python
import myapp              # Может не импортироваться как ожидается
                          # (будет работать как namespace package
                          #  с ограничениями)
```

**Решение:** всегда создавай `__init__.py`, даже пустой.

### Циклические импорты в `__init__.py`

```python
# shop/__init__.py
from shop.models import User   # models импортирует что-то из __init__.py
                                # → ImportError
```

**Решение:** держи `__init__.py` максимально тонким или импортируй только из модулей, которые сами не импортируют ничего из пакета.

### Конфликт имён пакета и стандартной библиотеки

```
my_project/
└── json/              ← ❌ Назвали как встроенный модуль
    └── __init__.py
```

`import json` теперь может работать непредсказуемо. **Не называй свои пакеты именами из стандартной библиотеки**: `json`, `os`, `sys`, `email`, `test`, `string` и т.п.

## 💡 Главное

```python
# Пакет = папка с модулями (обычно с __init__.py)
myapp/
├── __init__.py          # маркер пакета + код инициализации
├── module1.py           # myapp.module1
└── subpackage/
    ├── __init__.py
    └── module2.py       # myapp.subpackage.module2

# Импорты отражают структуру папок
from myapp.module1 import func
from myapp.subpackage.module2 import Cls

# __init__.py может быть пустым — этого достаточно

# Пробрось публичные имена в __init__.py для удобного API:
# shop/__init__.py:  from shop.models import User
# Теперь: from shop import User

# При импорте myapp.a.b.c выполняются ВСЕ __init__.py по пути

# __all__ = [...] — что экспортируется при from pkg import *

# Не называй свои пакеты как стандартные модули!
```

---

[[09_2_how_import_works|← 🔍 Как работает импорт]] | [[09_4_relative_imports|↔️ Абсолютные и относительные импорты →]]
