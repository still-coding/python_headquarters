[[09_3_packages|← 🗂️ Пакеты и `__init__.py`]] | [[09_5_dunder_name|🏁 `__name__ == "__main__"` и `python -m` →]]

---
# ↔️ Абсолютные и относительные импорты
```toc
minLevel: 2
```

## Два способа сослаться на модуль

Внутри пакета один и тот же модуль можно импортировать двумя способами:

```python
# Абсолютный импорт — полный путь от корня
from myapp.utils import format_date

# Относительный импорт — путь относительно текущего модуля
from .utils import format_date
```

Оба варианта работают. Вопрос — когда какой использовать.

## Абсолютные импорты

**Абсолютный импорт** указывает полный путь к модулю от корня — от того места, откуда запускается программа.

```
myapp/
├── __init__.py
├── main.py
├── utils.py
└── models/
    ├── __init__.py
    └── user.py
```

```python
# myapp/models/user.py
from myapp.utils import format_date       # абсолютный
from myapp.models.product import Product  # абсолютный
```

Преимущества:

- 🟢 Сразу видно, откуда берётся имя — можно прочитать «в отрыве» от файла
- 🟢 При перемещении файла импорты не ломаются
- 🟢 PEP 8 рекомендует абсолютные импорты как основной способ

## Относительные импорты

**Относительный импорт** использует точки, чтобы указать путь от текущего модуля:

- `.` — текущий пакет (та же папка)
- `..` — родительский пакет (на уровень выше)
- `...` — ещё выше

```
myapp/
├── __init__.py
├── utils.py
└── models/
    ├── __init__.py
    ├── user.py
    └── base.py
```

```python
# myapp/models/user.py

from .base import BaseModel        # из того же пакета (models)
from ..utils import format_date    # из родительского пакета (myapp)
```

Читаем по точкам: `.` — «отсюда», `..` — «на этаж выше».

### Более сложный пример

```
myapp/
├── __init__.py
├── config.py
├── db/
│   ├── __init__.py
│   ├── connection.py
│   └── models/
│       ├── __init__.py
│       └── user.py
└── api/
    ├── __init__.py
    └── handlers.py
```

```python
# myapp/db/models/user.py

from .base import BaseModel          # myapp.db.models.base
from ..connection import get_conn    # myapp.db.connection
from ...config import DB_URL         # myapp.config
```

## Синтаксис: только `from ... import`

Относительные импорты работают **только** в форме `from ... import`:

```python
# ✅ Работает
from . import utils
from .utils import format_date
from ..models import User

# ❌ Так нельзя — синтаксическая ошибка
import .utils
import ..models
```

## Когда какой использовать

На практике в зрелых проектах встречаются оба стиля. Общее правило:

| Ситуация | Рекомендация |
|---|---|
| Импорт внутри пакета (сосед/родитель) | Относительный — `from .sibling import X` |
| Импорт из другого пакета проекта | Абсолютный — `from myapp.other import Y` |
| Импорт стандартной/сторонней библиотеки | Только абсолютный |

### Почему относительные удобны внутри пакета

Пакет легко переименовать. Если пакет `myapp` переименовать в `shop`:

```python
# Абсолютные импорты придётся править:
from myapp.utils import format_date    # → from shop.utils import format_date

# Относительные — нет:
from .utils import format_date         # работает как было
```

Это особенно ценно для библиотек, которые публикуются под разными именами.

### Почему абсолютные безопаснее в целом

Относительные импорты ломаются, если **файл запускается напрямую** как скрипт:

```python
# myapp/models/user.py
from ..utils import format_date
```

```bash
$ python myapp/models/user.py
ImportError: attempted relative import with no known parent package
```

В момент прямого запуска Python не понимает, что `user.py` — часть пакета, и точки не к чему применить. Подробнее — в [[09_5_dunder_name|`python -m`]].

## Главное ограничение: только внутри пакета

Относительные импорты работают **только** между модулями одного пакета. Нельзя через относительный импорт сослаться на файл, который лежит вне пакета.

```
project/
├── script.py
└── myapp/
    ├── __init__.py
    └── utils.py
```

```python
# myapp/utils.py
from ..script import something    # ❌ script.py не в пакете
```

Это не работает и работать не будет: `script.py` не часть никакого пакета, относительный импорт его не видит.

## Частые ошибки

### `attempted relative import with no known parent package`

```python
# myapp/models/user.py
from ..utils import format_date
```

Запустили напрямую:

```bash
$ python myapp/models/user.py
ImportError: attempted relative import with no known parent package
```

**Причина:** файл запущен как скрипт, а не как модуль пакета.
**Решение:** запускать через `-m` из корня проекта:

```bash
$ python -m myapp.models.user
```

### `attempted relative import beyond top-level package`

```python
# myapp/utils.py
from ..other import X        # ❌ myapp — верхний пакет, выше некуда
```

**Причина:** пытаешься подняться выше корня пакета.
**Решение:** если `other` — соседний пакет того же проекта, импортируй абсолютно: `from other import X`.

### Слишком много точек в больших проектах

```python
from ....common.utils.helpers import foo   # 😵
```

Если точек больше трёх — это запах того, что структура проекта слишком глубокая или модуль положен не туда. Обычно лучше переписать через абсолютный импорт:

```python
from myapp.common.utils.helpers import foo
```

## 💡 Главное

```python
# Абсолютный импорт — полный путь от корня проекта
from myapp.utils import format_date

# Относительный импорт — путь с точками от текущего модуля
from .utils import format_date       # сосед
from ..models import User            # родительский пакет
from ...config import DB_URL         # на два уровня выше

# Только в форме from ... import (не просто import)

# Используй:
# - относительные — для соседей внутри одного пакета
# - абсолютные — для всего остального

# Относительные импорты работают ТОЛЬКО внутри пакета
# И ломаются при прямом запуске файла как скрипта

# Если больше трёх точек — пересмотри структуру
```

---

[[09_3_packages|← 🗂️ Пакеты и `__init__.py`]] | [[09_5_dunder_name|🏁 `__name__ == "__main__"` и `python -m` →]]
