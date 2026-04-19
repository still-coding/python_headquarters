[[09_5_dunder_name|← 🏁 `__name__ == "__main__"` и `python -m`]] | [[00_table_of_contents|☰ Оглавление →]]

---
# 📥 Виртуальные окружения, pip и uv
```toc
minLevel: 2
```

## Проблема: один Python на всю систему

Как только начинаешь устанавливать сторонние пакеты, возникает неизбежная проблема. Представь:

- В **проекте A** нужен `Django 4.2`
- В **проекте B** нужен `Django 5.1`
- На компьютере один Python, и он может держать только одну версию `Django`

Если ставить пакеты «в систему», рано или поздно проекты начнут ломать друг друга. Решение — **виртуальные окружения**: отдельная изолированная копия Python со своим набором пакетов для каждого проекта.

```
~/projects/
├── project_a/
│   └── .venv/              ← свой Python + Django 4.2
└── project_b/
    └── .venv/              ← свой Python + Django 5.1
```

## `venv` — стандартный инструмент

Модуль `venv` входит в Python — ничего ставить не нужно.

### Создание окружения

```bash
# В папке проекта
python -m venv .venv
```

Появилась папка `.venv/` с копией Python. `.venv` — это просто соглашение, можно назвать как угодно, но стандартное имя помогает инструментам (Git, IDE) его распознавать.

### Активация

Активация «включает» окружение в текущей сессии терминала: все команды `python` и `pip` начинают работать с окружением, а не с системным Python.

```bash
# Linux / macOS
source .venv/bin/activate

# Windows (PowerShell)
.venv\Scripts\Activate.ps1
```

После активации в начале командной строки появляется `(.venv)`:

```bash
(.venv) $ python --version
Python 3.13.1

(.venv) $ which python
/home/user/my_project/.venv/bin/python
```

### Деактивация

```bash
(.venv) $ deactivate
$
```

### Что добавить в `.gitignore`

Папку `.venv/` **никогда не коммитят**. Она большая, зависит от ОС и легко пересоздаётся.

```
# .gitignore
.venv/
__pycache__/
*.pyc
```

## `pip` — установка пакетов

`pip` — стандартный установщик пакетов Python. Внутри активированного окружения все команды `pip` работают с ним.

### Основные команды

```bash
# Установить пакет
pip install requests

# Установить конкретную версию
pip install requests==2.31.0

# Установить несколько пакетов
pip install requests rich click

# Обновить пакет
pip install --upgrade requests

# Удалить пакет
pip uninstall requests

# Список установленных пакетов
pip list

# Подробная информация о пакете
pip show requests
```

### `requirements.txt` — фиксация зависимостей

Чтобы другие люди (или ты сам на другой машине) могли воспроизвести окружение, список зависимостей сохраняют в текстовый файл.

```bash
# Сохранить текущие зависимости
pip freeze > requirements.txt
```

Содержимое `requirements.txt`:

```
requests==2.31.0
rich==13.7.0
click==8.1.7
```

Установка из файла:

```bash
pip install -r requirements.txt
```

> ⚠️ `pip freeze` сохраняет **все** установленные пакеты, включая транзитивные зависимости. Для ручного ведения лучше поддерживать отдельный файл только с прямыми зависимостями.

## Рабочий цикл с `venv` + `pip`

```bash
# 1. Создать окружение
python -m venv .venv

# 2. Активировать
source .venv/bin/activate

# 3. Установить зависимости
pip install requests rich

# 4. Зафиксировать
pip freeze > requirements.txt

# 5. Работать
python main.py

# 6. Закончил — деактивировать
deactivate
```

## `uv` — современная альтернатива

[uv](https://docs.astral.sh/uv/) — быстрый менеджер пакетов и проектов, написанный на Rust. Он заменяет связку `python -m venv` + `pip` + `pip-tools` + отчасти `pyenv` одной утилитой, которая работает **в десятки раз быстрее** обычного pip.

На момент начала 2026 года `uv` стал фактическим стандартом в новых проектах: быстрее, проще, совместим с существующими `requirements.txt` и `pyproject.toml`.

### Установка

```bash
# Linux / macOS
curl -LsSf https://astral.sh/uv/install.sh | sh

# Windows (PowerShell)
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

После установки доступны команды `uv` и `uvx`.

### Два способа использовать uv

**Вариант 1 — как быстрая замена pip.** Работает с обычным `venv` и `requirements.txt`, просто быстрее:

```bash
# Создать окружение
uv venv                          # аналог: python -m venv .venv

# Активировать (как обычно)
source .venv/bin/activate

# Установить пакеты
uv pip install requests          # аналог: pip install requests

# Установить из requirements.txt
uv pip install -r requirements.txt
```

**Вариант 2 — управление проектом целиком** (рекомендуется для новых проектов):

```bash
# Создать новый проект с pyproject.toml и виртуальным окружением
uv init my_project
cd my_project

# Добавить зависимость (установит + обновит pyproject.toml + lock-файл)
uv add requests rich

# Удалить зависимость
uv remove rich

# Запустить скрипт в окружении проекта (без ручной активации)
uv run python main.py
uv run pytest

# Синхронизировать окружение с lock-файлом (на другой машине)
uv sync
```

При таком подходе уже не нужно вручную активировать `.venv` — `uv run` делает это сам.

### Запуск утилит: `uvx`

Команда `uvx` запускает CLI-утилиты во временном изолированном окружении, без установки в проект:

```bash
uvx ruff check .                 # линтер Ruff
uvx black my_file.py             # форматтер Black
uvx pytest                       # тесты
```

Это аналог `pipx run`. Удобно, когда инструмент нужен разово или не должен засорять зависимости проекта.

Если инструмент нужен постоянно — установить глобально:

```bash
uv tool install ruff
ruff check .                     # теперь доступен напрямую
```

### Управление версиями Python

`uv` умеет сам скачивать нужную версию Python — отдельный `pyenv` не нужен:

```bash
# Установить конкретную версию
uv python install 3.12

# Использовать её в проекте
uv venv --python 3.12
```

## Что выбрать

| Ситуация | Что использовать |
|---|---|
| Учебный скрипт, первые шаги | `python -m venv` + `pip` (стандарт, ничего ставить не надо) |
| Новый проект с нуля | `uv init` + `uv add` |
| Существующий проект с `requirements.txt` | `uv pip install -r requirements.txt` (быстрее, совместимо) |
| Разовый запуск утилиты | `uvx tool_name` |

Оба подхода сосуществуют, и знать базовый `venv` + `pip` полезно в любом случае: они есть всегда, их понимают все инструкции и все коллеги.

## Типичные проблемы

### «Команда `python` не найдена» после `deactivate`

После деактивации команды `python`/`pip` возвращаются к системным. Если системный Python не установлен или называется `python3`, используй `python3`.

### Установка идёт в систему вместо `.venv`

Значит окружение не активировано. Проверь:

```bash
which python        # должно указывать на .venv/bin/python
which pip           # должно указывать на .venv/bin/pip
```

Если указывает на `/usr/bin/python` — активируй окружение снова.

### `Permission denied` при `pip install`

Не используй `sudo pip install`. Ошибка прав означает, что pip пытается писать в системную папку — скорее всего, окружение не активировано.

### Коммитнули `.venv/` в Git

Удалить из репозитория и добавить в `.gitignore`:

```bash
git rm -r --cached .venv/
echo ".venv/" >> .gitignore
git commit -am "Remove .venv from repo"
```

## 💡 Главное

```bash
# Классический путь — работает везде
python -m venv .venv
source .venv/bin/activate        # Linux/macOS
pip install requests
pip freeze > requirements.txt
deactivate

# Современный путь — uv
uv init my_project               # новый проект
uv add requests                  # добавить зависимость
uv run python main.py            # запустить без активации
uv sync                          # восстановить окружение на другой машине

# Разовый запуск утилиты
uvx ruff check .

# В .gitignore всегда:
# .venv/
# __pycache__/
```

---

[[09_5_dunder_name|← 🏁 `__name__ == "__main__"` и `python -m`]] | [[00_table_of_contents|☰ Оглавление →]]
