[[05_1_read_write|← 📖 Чтение и запись]]

---
# 📂 Работа с путями
```toc
minLevel: 2
```

## `pathlib` — современный способ работы с путями

Модуль `pathlib` (стандартная библиотека) предоставляет удобный объектно-ориентированный способ работать с путями к файлам и директориям.

```python
from pathlib import Path

# Создание пути
p = Path("data/users.csv")

p.name        # "users.csv" — имя файла
p.stem        # "users" — имя без расширения
p.suffix      # ".csv" — расширение
p.parent      # Path("data") — родительская директория
```

### Построение путей

```python
from pathlib import Path

# Оператор / для объединения путей
base = Path("project")
data_dir = base / "data"
file_path = data_dir / "results.txt"
print(file_path)  # project/data/results.txt

# Текущая директория
cwd = Path.cwd()
print(cwd)  # /home/user/my_project

# Домашняя директория
home = Path.home()
print(home)  # /home/user
```

### Проверки

```python
from pathlib import Path

p = Path("data.txt")

p.exists()       # True/False — существует ли
p.is_file()      # True/False — это файл?
p.is_dir()       # True/False — это директория?
```

### Обход файлов в директории

```python
from pathlib import Path

data_dir = Path("data")

# Все файлы в директории
for file in data_dir.iterdir():
    print(file)

# Только .txt файлы
for txt_file in data_dir.glob("*.txt"):
    print(txt_file)

# Рекурсивный поиск (включая поддиректории)
for py_file in data_dir.rglob("*.py"):
    print(py_file)
```

### Создание и удаление

```python
from pathlib import Path

# Создать директорию
Path("output/reports").mkdir(parents=True, exist_ok=True)
# parents=True — создаёт промежуточные директории
# exist_ok=True — не ошибается, если уже существует

# Чтение и запись через Path
content = Path("data.txt").read_text(encoding="utf-8")
Path("output.txt").write_text("Привет!", encoding="utf-8")
```

## Практический пример

```python
from pathlib import Path

def process_all_txt_files(directory):
    """Обрабатывает все .txt файлы в директории"""
    input_dir = Path(directory)
    output_dir = input_dir / "processed"
    output_dir.mkdir(exist_ok=True)
    
    for txt_file in input_dir.glob("*.txt"):
        content = txt_file.read_text(encoding="utf-8")
        word_count = len(content.split())
        
        result = f"Файл: {txt_file.name}\nСлов: {word_count}\n"
        output_file = output_dir / f"{txt_file.stem}_stats.txt"
        output_file.write_text(result, encoding="utf-8")
        
        print(f"Обработан: {txt_file.name} → {output_file.name}")
```

## 💡 Главное

```python
from pathlib import Path

# Создание пути и навигация
p = Path("data") / "file.txt"
p.name, p.stem, p.suffix, p.parent

# Проверки
p.exists(), p.is_file(), p.is_dir()

# Обход
Path("dir").glob("*.txt")      # Файлы по шаблону
Path("dir").rglob("*.py")      # Рекурсивный поиск

# Чтение и запись (короткий синтаксис)
text = Path("file.txt").read_text(encoding="utf-8")
Path("file.txt").write_text("данные", encoding="utf-8")
```

---

[[05_1_read_write|← 📖 Чтение и запись]]
