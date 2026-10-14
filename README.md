# Яндекс Самокат - финальный проект (QA Engineer Plus)

Проект содержит решения двух частей финального задания:
1. **Работа с базой данных** — SQL-запросы к БД `scooter_rent`.
2. **Автоматизация теста к API** — проверка сценария «создание заказа → получение заказа по треку».

---

## 📁 Структура проекта

```
.
├── configuration.py         # Базовый URL сервиса и пути эндпоинтов
├── data.py                  # Тестовые данные и заголовки запросов
├── order_requests.py        # Функции отправки запросов к API
├── track_order_test.py      # Автотест сценария создания/получения заказа
├── request_order.sql        # SQL-запрос: логины курьеров и число заказов «В доставке»
├── request_track.sql        # SQL-запрос: трекеры заказов и их статусы
├── .gitignore               # Исключения для Git
└── README.md                # Документация проекта
```

---

## 🗄️ Часть 1. Работа с базой данных

### Подключение к БД

```bash
psql -U morty -d scooter_rent
```

### Задание 1 — `request_order.sql`

Выводит список логинов курьеров и количество их заказов в статусе **«В доставке»** (`inDelivery = true`).

```sql
SELECT
    c.login,
    COUNT(o.courierId) AS orders_in_delivery
FROM
    Couriers c
LEFT JOIN
    Orders o ON c.id = o.courierId
WHERE
    o.inDelivery = true
GROUP BY
    c.id, c.login
ORDER BY
    orders_in_delivery DESC;
```

### Задание 2 — `request_track.sql`

Выводит все трекеры заказов и их статусы по правилу:

| Условие              | Статус |
|----------------------|--------|
| `finished = true`    | 2      |
| `cancelled = true`   | -1     |
| `inDelivery = true`  | 1      |
| иначе                | 0      |

```sql
SELECT
    track,
    CASE
        WHEN finished = true THEN 2
        WHEN cancelled = true THEN -1
        WHEN inDelivery = true THEN 1
        ELSE 0
    END AS status
FROM
    Orders
ORDER BY
    track;
```

---

## 🤖 Часть 2. Автоматизация теста к API

### Сценарий автотеста

1. Клиент создаёт заказ (`POST /api/v1/orders/`).
2. Сохраняется номер трека заказа.
3. Выполняется запрос на получение заказа по треку (`GET /api/v1/orders?track=...`).
4. Проверяется, что код ответа равен **200**.

### Файлы

- **`configuration.py`** — базовый адрес сервиса и пути эндпоинтов.
- **`data.py`** — заголовки и тело запроса на создание заказа.
- **`order_requests.py`** — обёртки над `requests` для создания и получения заказа.
- **`track_order_test.py`** — сам тест (pytest).

### Установка и запуск

1. Установите зависимости:

   ```bash
   pip install requests pytest
   ```

2. Запустите тест:

   ```bash
   pytest track_order_test.py -v
   ```

Ожидаемый результат:

```
track_order_test.py::test_create_and_get_order PASSED
```

---

## 📝 Логи

Логи бэкенда находятся по пути:

```
/var/www/backend/logs/error.log
```

---

## 👤 Автор

**Серёгина Анастасия**, 43 когорта, Инженер по тестированию плюс
