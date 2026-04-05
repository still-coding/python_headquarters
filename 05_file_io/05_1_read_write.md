[[05_0_intro|← 📁 Работа с файлами]] | [[05_2_paths|📂 Работа с путями →]]

---
# 📖 Чтение и запись файлов
```toc
minLevel: 2
```

## Чтение файлов

### Прочитать весь файл целиком

```python
with open("data.txt", "r") as f:
    content = f.read()      # Весь файл как одна строка
    print(content)
```

### Прочитать по строкам

```python
# Способ 1: readlines() — список строк
with open("data.txt", "r") as f:
    lines = f.readlines()   # ["строка1\n", "строка2\n", ...]
    for line in lines:
        print(line.strip())  # strip() убирает \n на концах

# Способ 2: итерация по файлу (рекомендуемый)
with open("data.txt", "r") as f:
    for line in f:           # Файл — итерируемый объект
        print(line.strip())

# Способ 3: readline() — одна строка за раз
with open("data.txt", "r") as f:
    first_line = f.readline()
    second_line = f.readline()
```

> 💡 **Способ 2 (итерация)** — лучший для больших файлов: он не загружает весь файл в память, а читает строку за строкой.

### Кодировка

По умолчанию Python использует системную кодировку. Лучше указывать явно:

```python
# Всегда указывай encoding для текстовых файлов
with open("data.txt", "r", encoding="utf-8") as f:
    content = f.read()
```

## Запись в файлы

### Перезапись (`"w"`)

```python
# ⚠️ Полностью перезаписывает файл! Старое содержимое исчезнет
with open("output.txt", "w", encoding="utf-8") as f:
    f.write("Первая строка\n")
    f.write("Вторая строка\n")
```

### Дополнение (`"a"`)

```python
# Добавляет в конец файла
with open("log.txt", "a", encoding="utf-8") as f:
    f.write("Новая запись в лог\n")
```

### `print()` в файл

```python
with open("output.txt", "w", encoding="utf-8") as f:
    print("Привет!", file=f)              # Автоматически добавляет \n
    print("Имя:", "Иван", file=f)
    print(1, 2, 3, sep=", ", file=f)
```

### Запись нескольких строк

```python
lines = ["Строка 1\n", "Строка 2\n", "Строка 3\n"]

with open("output.txt", "w", encoding="utf-8") as f:
    f.writelines(lines)   # Записывает список строк (без добавления \n!)
```

## Практические примеры

### Подсчёт строк, слов и символов

```python
def file_stats(filepath):
    """Статистика по файлу — как wc в Unix"""
    with open(filepath, "r", encoding="utf-8") as f:
        lines = f.readlines()
    
    num_lines = len(lines)
    num_words = sum(len(line.split()) for line in lines)
    num_chars = sum(len(line) for line in lines)
    
    return num_lines, num_words, num_chars

lines, words, chars = file_stats("example.txt")
print(f"Строк: {lines}, Слов: {words}, Символов: {chars}")
```

### Чтение CSV вручную

```python
def read_csv(filepath):
    """Простое чтение CSV файла"""
    records = []
    with open(filepath, "r", encoding="utf-8") as f:
        header = f.readline().strip().split(",")
        for line in f:
            values = line.strip().split(",")
            record = dict(zip(header, values))
            records.append(record)
    return records

# users.csv:
# name,age,city
# Иван,25,Москва
# Анна,30,Питер

users = read_csv("users.csv")
# [{'name': 'Иван', 'age': '25', 'city': 'Москва'}, ...]
```

### Копирование файла

```python
def copy_file(source, destination):
    """Копирует текстовый файл"""
    with open(source, "r", encoding="utf-8") as src:
        content = src.read()
    with open(destination, "w", encoding="utf-8") as dst:
        dst.write(content)
```

## ⚠️ Частые ошибки

```python
# ❌ Файл не найден
# open("несуществующий.txt", "r")  # FileNotFoundError

# ✅ Проверка или обработка ошибки
import os
if os.path.exists("data.txt"):
    with open("data.txt", "r") as f:
        content = f.read()

# Или через try/except
try:
    with open("data.txt", "r") as f:
        content = f.read()
except FileNotFoundError:
    print("Файл не найден!")

# ❌ Забыли encoding — может сломаться на не-ASCII символах
# ✅ Всегда указывай encoding="utf-8"

# ❌ Использовали "w" вместо "a" и потеряли данные
# "w" — перезаписывает, "a" — дополняет
```

## 💡 Главное

```python
# Всегда используй with для работы с файлами
with open("file.txt", "r", encoding="utf-8") as f:
    content = f.read()

# Режимы: "r" — чтение, "w" — перезапись, "a" — дополнение
# Для больших файлов — итерация по строкам: for line in f
# Всегда указывай encoding="utf-8"
# f.write() не добавляет \n, print(file=f) — добавляет
```

---

[[05_0_intro|← 📁 Работа с файлами]] | [[05_2_paths|📂 Работа с путями →]]
