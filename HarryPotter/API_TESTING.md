# 🧪 Тестирование API рекомендаций

## 📍 Базовая URL
```
http://localhost:3000
```

## 🎯 Endpoints для тестирования

### 1. Получить популярные товары
```http
GET /api/recommendations/popular?limit=6
```

**Response:**
```json
{
  "placement": "popular",
  "title": "⭐ Популярные товары",
  "description": "Самые любимые предметы в нашей лавке",
  "products": [
    {
      "id": 3,
      "name": "Метла «Сквиббы»",
      "category": "Транспорт",
      "price": 4500,
      "views": 35,
      "rating": 4.9,
      ...
    }
  ]
}
```

**Параметры:**
- `limit` (query, int) - Количество товаров (по умолчанию 6)

---

### 2. Получить похожие товары
```http
GET /api/recommendations/similar/{productId}?limit=5
```

**Пример:**
```http
GET /api/recommendations/similar/1?limit=5
```

**Response:**
```json
{
  "placement": "similar",
  "title": "📦 Похожие товары в категории «Палочки»",
  "description": "Другие магические предметы, которые вам могут понравиться",
  "baseProduct": 1,
  "products": [...]
}
```

**Параметры:**
- `productId` (path, required) - ID товара для поиска похожих
- `limit` (query, int) - Количество товаров (по умолчанию 5)

---

### 3. Товары, которые часто покупают вместе
```http
GET /api/recommendations/together/{productId}?limit=4
```

**Пример:**
```http
GET /api/recommendations/together/3?limit=4
```

**Response:**
```json
{
  "placement": "together",
  "title": "🛒 Часто покупают вместе",
  "description": "Товары, которые идеально дополнят ваш заказ",
  "baseProduct": 3,
  "products": [...],
  "reason": "На основе истории заказов"
}
```

**Параметры:**
- `productId` (path, required) - ID товара
- `limit` (query, int) - Количество товаров (по умолчанию 4)

---

### 4. Рекомендации для главной страницы
```http
GET /api/recommendations/home?limit=6&viewedProducts=[]
```

**Пример:**
```http
GET /api/recommendations/home?limit=6&viewedProducts=[1,3,5]
```

**Response:**
```json
{
  "popular": {
    "title": "⭐ Популярные товары",
    "description": "Самые любимые предметы в нашей лавке",
    "placement": "home_popular",
    "products": [...]
  },
  "forYou": {
    "title": "🎯 Рекомендации для вас",
    "description": "Подобрано специально на основе ваших интересов",
    "placement": "home_for_you",
    "items": [
      {
        "product": {...},
        "reason": "Популярно в категории «Палочки»"
      }
    ]
  },
  "newArrivals": {
    "title": "🆕 Новинки",
    "description": "Свежие поступления в Лавку",
    "placement": "home_new",
    "products": [...]
  }
}
```

**Параметры:**
- `limit` (query, int) - Количество товаров (по умолчанию 6)
- `viewedProducts` (query, string) - JSON массив ID просмотренных товаров

---

### 5. Получить статистику
```http
GET /api/recommendations/stats
```

**Response:**
```json
{
  "totalProducts": 12,
  "totalOrders": 50,
  "productCategories": [
    "Палочки",
    "Зелья",
    "Знания",
    "Магия",
    "Транспорт",
    "Средства",
    "Аксессуары",
    "Еда"
  ],
  "mostViewed": {
    "id": 11,
    "name": "Метла Чемпиона",
    "views": 40
  },
  "mostPurchased": {
    "id": 10,
    "name": "Чернила для письма",
    "purchases": 10
  },
  "averageOrderValue": "1500.00"
}
```

---

### 6. Логирование просмотра товара
```http
POST /api/recommendations/track-view/{productId}
```

**Пример:**
```http
POST /api/recommendations/track-view/5
```

**Response:**
```json
{
  "success": true,
  "message": "Просмотр записан",
  "product": {
    "id": 5,
    "views": 9
  }
}
```

**Параметры:**
- `productId` (path, required) - ID товара

---

## 🧪 Примеры использования в curl

### Тест 1: Получить популярные товары
```bash
curl http://localhost:3000/api/recommendations/popular?limit=6
```

### Тест 2: Получить похожие товары
```bash
curl http://localhost:3000/api/recommendations/similar/3?limit=5
```

### Тест 3: Товары к покупке
```bash
curl http://localhost:3000/api/recommendations/together/1?limit=4
```

### Тест 4: Главная страница
```bash
curl "http://localhost:3000/api/recommendations/home?limit=6&viewedProducts=[1,3,5]"
```

### Тест 5: Статистика
```bash
curl http://localhost:3000/api/recommendations/stats
```

### Тест 6: Логирование просмотра
```bash
curl -X POST http://localhost:3000/api/recommendations/track-view/5
```

---

## 🖼️ Тестирование через Swagger UI

1. Откройте браузер
2. Перейдите на `http://localhost:3000/api-docs`
3. Найдите папку "Рекомендации"
4. Нажмите на нужный endpoint
5. Нажмите "Try it out"
6. Введите параметры
7. Нажмите "Execute"

---

## 📊 Тестовые сценарии

### Сценарий 1: Сотрудник магазина смотрит популярные товары
```
1. Запрос: GET /api/recommendations/popular?limit=6
2. Ожидание: Получить 6 товаров, отсортированные по популярности
3. Проверка: Товары с наибольшим views идут в начале
```

### Сценарий 2: Клиент смотрит палочму #1
```
1. Запрос: POST /api/recommendations/track-view/1
2. Ожидание: views увеличится на 1
3. Проверка: Ответ содержит новое значение views
```

### Сценарий 3: Рекомендации на основе палочки #1
```
1. Запрос: GET /api/recommendations/similar/1?limit=5
2. Ожидание: Получить похожие товары из категории "Палочки"
3. Проверка: Все возвращенные товары из категории "Палочки"
4. Проверка: Цены близки к цене товара #1 (±30%)
```

### Сценарий 4: Товары к покупке вместе с палочкой #3
```
1. Запрос: GET /api/recommendations/together/3?limit=4
2. Ожидание: Получить товары, которые часто покупают с #3
3. Проверка: Товары соответствуют истории заказов
```

### Сценарий 5: Главная страница с историей
```
1. Запрос: GET /api/recommendations/home?limit=6&viewedProducts=[1,3,5]
2. Ожидание: Получить все 3 типа рекомендаций
3. Проверка: forYou содержит товары из категорий 1,3,5
```

---

## 📈 Проверка данных

### Проверить увеличение просмотров
```
1. Запрос: GET /api/products/1
   └─ Заметить текущее значение views
2. Запрос: POST /api/recommendations/track-view/1
   └─ Views должен увеличиться на 1
3. Запрос: GET /api/products/1
   └─ Проверить новое значение views
```

### Проверить влияние на популярность
```
1. Запрос: GET /api/recommendations/popular?limit=1
   └─ Заметить первый товар
2. 5 раз: POST /api/recommendations/track-view/2
   └─ Увеличить просмотры товара #2
3. Запрос: GET /api/recommendations/popular?limit=1
   └─ Первым может быть товар #2
```

---

## 🔄 JSON примеры для скопирования

### viewedProducts параметр
```json
["[1,3,5,7]"]
```

### Ответ с причинами рекомендаций
```json
{
  "product": {
    "id": 6,
    "name": "Волшебная палочка №2",
    "category": "Палочки",
    "price": 1500,
    ...
  },
  "reason": "Популярно в категории «Палочки»"
}
```

---

## 🐛 Отладка

### Проверить, что сервер работает
```bash
curl http://localhost:3000/api/health
```

Ожидается:
```json
{"status":"OK"}
```

### Посмотреть все товары
```bash
curl http://localhost:3000/api/products
```

### Посмотреть все заказы
```bash
curl http://localhost:3000/api/orders
```

### Посмотреть базу данных в консоли сервера
```bash
# Когда сервер запущен, посмотрите логи в консоли
# Там будут логи всех операций
```

---

## 📝 Результаты тестирования

| Endpoint | Status | Response | Тест |
|----------|--------|----------|------|
| GET /api/recommendations/popular | 200 | Array | ✅ |
| GET /api/recommendations/similar/1 | 200 | Object | ✅ |
| GET /api/recommendations/together/3 | 200 | Object | ✅ |
| GET /api/recommendations/home | 200 | Object | ✅ |
| GET /api/recommendations/stats | 200 | Object | ✅ |
| POST /api/recommendations/track-view/5 | 200 | Object | ✅ |

---

## 🚀 Автоматизированное тестирование

### Bash скрипт для всех тестов
```bash
#!/bin/bash

BASE_URL="http://localhost:3000/api"

echo "🧪 Тестирование API рекомендаций..."

echo "✓ Популярные товары"
curl "$BASE_URL/recommendations/popular?limit=3"

echo "✓ Похожие товары"
curl "$BASE_URL/recommendations/similar/1?limit=3"

echo "✓ Товары вместе"
curl "$BASE_URL/recommendations/together/3?limit=3"

echo "✓ Статистика"
curl "$BASE_URL/recommendations/stats"

echo "✓ Логирование просмотра"
curl -X POST "$BASE_URL/recommendations/track-view/5"

echo "✓ Готово!"
```

---

**Версия:** 1.0.0  
**Дата:** 2 апреля 2026 г.  
**Статус:** Готово к тестированию ✅
