[[07_4_raise_and_assert|← 🔺 `raise` и `assert`]] | [[07_6_context_managers|🔑 Контекстные менеджеры →]]

---
# 🏗️ Собственные исключения
```toc
minLevel: 2
```

## Зачем создавать свои исключения?

Встроенные исключения хороши для общих случаев. Но в реальных приложениях нужна точность — чтобы можно было перехватить именно ту ошибку которую ожидаешь:

```python
# ❌ Непонятно — что именно пошло не так?
raise ValueError("Пользователь не найден")

# ✅ Понятно — конкретная ошибка домена
raise UserNotFoundError("Пользователь с id=42 не найден")
```

Собственные исключения делают код:
- **Выразительным** — название исключения само объясняет что произошло
- **Гибким** — можно поймать конкретную ошибку, не трогая остальные
- **Структурированным** — иерархия исключений отражает иерархию проблем

## Простейшее собственное исключение

Достаточно унаследовать от `Exception`:

```python
class UserNotFoundError(Exception):
    pass


def get_user(user_id):
    users = {1: "Анна", 2: "Иван"}
    if user_id not in users:
        raise UserNotFoundError(f"Пользователь с id={user_id} не найден")
    return users[user_id]


try:
    get_user(99)
except UserNotFoundError as e:
    print(f"Ошибка: {e}")   # Ошибка: Пользователь с id=99 не найден
```

## Исключение с дополнительными данными

Можно передавать структурированную информацию через `__init__`:

```python
class ValidationError(Exception):
    def __init__(self, field, message):
        self.field = field
        self.message = message
        super().__init__(f"{field}: {message}")


def validate_age(age):
    if not isinstance(age, int):
        raise ValidationError("age", "должно быть целым числом")
    if age < 0 or age > 150:
        raise ValidationError("age", f"должно быть от 0 до 150, получено {age}")


try:
    validate_age(-5)
except ValidationError as e:
    print(f"Поле: {e.field}")      # Поле: age
    print(f"Проблема: {e.message}") # Проблема: должно быть от 0 до 150, получено -5
    print(e)                        # age: должно быть от 0 до 150, получено -5
```

## Иерархия собственных исключений

Для приложения полезно создать базовое исключение — чтобы можно было поймать любую ошибку приложения одним `except`:

```python
# Базовое исключение приложения
class AppError(Exception):
    """Базовый класс для всех исключений приложения"""
    pass


# Исключения по категориям
class DatabaseError(AppError):
    """Ошибки работы с базой данных"""
    pass

class NetworkError(AppError):
    """Ошибки сети"""
    pass

class ValidationError(AppError):
    """Ошибки валидации данных"""
    pass


# Конкретные исключения
class ConnectionError(DatabaseError):
    pass

class QueryError(DatabaseError):
    pass

class UserNotFoundError(DatabaseError):
    pass

class TimeoutError(NetworkError):
    pass

class FieldRequiredError(ValidationError):
    def __init__(self, field):
        self.field = field
        super().__init__(f"Поле '{field}' обязательно для заполнения")

class FieldTooLongError(ValidationError):
    def __init__(self, field, max_length):
        self.field = field
        self.max_length = max_length
        super().__init__(f"Поле '{field}' не должно превышать {max_length} символов")
```

Теперь можно ловить на любом уровне:

```python
try:
    save_user(data)
except FieldRequiredError as e:
    print(f"Заполните поле: {e.field}")   # Конкретная ошибка
except ValidationError as e:
    print(f"Неверные данные: {e}")        # Любая ошибка валидации
except DatabaseError as e:
    print(f"Ошибка БД: {e}")             # Любая ошибка БД
except AppError as e:
    print(f"Ошибка приложения: {e}")     # Любая ошибка нашего кода
except Exception as e:
    print(f"Неожиданная ошибка: {e}")    # Всё остальное
```

## Хорошие практики

### Называйте исключения с суффиксом `Error`

```python
# ✅ Хорошо
class UserNotFoundError(Exception): pass
class InvalidTokenError(Exception): pass
class PaymentFailedError(Exception): pass

# ❌ Плохо — непонятно что это исключение
class UserNotFound(Exception): pass
class InvalidToken(Exception): pass
```

### Всегда вызывайте `super().__init__()`

```python
class MyError(Exception):
    def __init__(self, message, code=None):
        self.code = code
        super().__init__(message)   # Передаём сообщение в базовый класс


e = MyError("что-то сломалось", code=404)
print(e)        # что-то сломалось  — работает благодаря super().__init__
print(e.args)   # ('что-то сломалось',)
```

### Документируйте исключения в docstring

```python
def transfer_money(from_account, to_account, amount):
    """
    Переводит деньги между счетами.

    Raises:
        ValueError: если amount <= 0
        InsufficientFundsError: если на счёте недостаточно средств
        AccountNotFoundError: если один из счетов не найден
    """
    ...
```

## 🎯 Пример с реальной задачей

Исключения для интернет-магазина:

```python
class ShopError(Exception):
    """Базовое исключение магазина"""
    pass


class ProductError(ShopError):
    pass

class OrderError(ShopError):
    pass


class ProductNotFoundError(ProductError):
    def __init__(self, product_id):
        self.product_id = product_id
        super().__init__(f"Товар #{product_id} не найден")

class OutOfStockError(ProductError):
    def __init__(self, product_id, requested, available):
        self.product_id = product_id
        self.requested = requested
        self.available = available
        super().__init__(
            f"Товар #{product_id}: запрошено {requested}, доступно {available}"
        )

class EmptyCartError(OrderError):
    def __init__(self):
        super().__init__("Нельзя оформить заказ с пустой корзиной")


def place_order(cart, inventory):
    if not cart:
        raise EmptyCartError()

    for product_id, quantity in cart.items():
        if product_id not in inventory:
            raise ProductNotFoundError(product_id)

        available = inventory[product_id]
        if quantity > available:
            raise OutOfStockError(product_id, quantity, available)

    return "Заказ оформлен"


inventory = {1: 5, 2: 0}
cart = {1: 3, 2: 1}

try:
    place_order(cart, inventory)
except OutOfStockError as e:
    print(f"Нет в наличии: {e}")
    print(f"Доступно: {e.available} шт.")
except ProductNotFoundError as e:
    print(f"Товар не найден: {e.product_id}")
except ShopError as e:
    print(f"Ошибка магазина: {e}")
# Нет в наличии: Товар #2: запрошено 1, доступно 0
# Доступно: 0 шт.
```

## 🔎 Проверка на ходу

```python
>>> class AppError(Exception):
...     pass
>>> class NotFoundError(AppError):
...     pass
>>>
>>> try:
...     raise NotFoundError("объект не найден")
... except AppError as e:
...     print(f"Поймали через AppError: {e}")
...
Поймали через AppError: объект не найден
>>> issubclass(NotFoundError, AppError)
True
>>> issubclass(NotFoundError, Exception)
True
```

## Резюме

- Собственные исключения делают код выразительным и позволяют точно перехватывать нужные ошибки
- Минимум — унаследовать от `Exception` и дать понятное имя
- Дополнительные данные — через `__init__` с атрибутами и вызовом `super().__init__(сообщение)`
- Базовое исключение приложения (`AppError`) — позволяет поймать любую ошибку домена одним `except`
- Иерархия исключений отражает иерархию проблем в приложении
- Называйте исключения с суффиксом `Error`
- Документируйте исключения в `Raises:` секции docstring

---

[[07_4_raise_and_assert|← 🔺 `raise` и `assert`]] | [[07_6_context_managers|🔑 Контекстные менеджеры →]]
