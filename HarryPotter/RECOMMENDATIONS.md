# 🪄 Система интеллектуальных рекомендаций "Лавка у Оливандера"

## Обзор

Модуль персональных рекомендаций для интернет-магазина "Лавка у Оливандера" использует алгоритмы анализа поведения пользователя и сходства товаров для предоставления умных рекомендаций.

## 📊 Компоненты системы

### 1. **Backend логика** (`recommendations.js`)
Основной сервис с алгоритмами рекомендаций:
- **Популярные товары** - ранжирование по просмотрам и покупкам
- **Похожие товары** - анализ категорий и ценовых диапазонов
- **Персональные рекомендации** - на основе истории просмотров
- **Товары, купленные вместе** - анализ корзин пользователей

### 2. **API маршруты** (`recommendationsRouter.js`)
REST API endpoints для получения рекомендаций:
```
GET  /api/recommendations/popular          - Популярные товары
GET  /api/recommendations/similar/{id}     - Похожие товары
GET  /api/recommendations/together/{id}    - Часто покупают вместе
GET  /api/recommendations/home              - Главная страница
GET  /api/recommendations/stats             - Статистика
POST /api/recommendations/track-view/{id}  - Логирование просмотра
```

### 3. **Frontend виджет** (`assets/js/recommendations.js`)
JS класс для отображения рекомендаций на странице:
- Управление историей просмотров (localStorage)
- Загрузка и рендеринг рекомендаций
- Отслеживание взаимодействий

### 4. **Стили** (`assets/css/recommendations.css`)
Красивое оформление с темой "Лавки у Оливандера":
- Адаптивная сетка товаров
- Анимации и переходы
- Мобильная оптимизация

## 🚀 Как использовать

### На главной странице (`index.html`)

```html
<!-- Подключить скрипты -->
<link rel="stylesheet" href="/assets/css/recommendations.css">
<script src="/assets/js/recommendations.js"></script>

<!-- Контейнер для рекомендаций -->
<div id="recommendations-container"></div>

<!-- Инициализация -->
<script>
  document.addEventListener('DOMContentLoaded', () => {
    // Загрузить рекомендации для главной страницы
    recommendationWidget.renderHome('recommendations-container', 6);
  });
</script>
```

### На странице товара (`product.html`)

```html
<!-- Похожие товары -->
<div id="similar-products"></div>
<script>
  const productId = new URLSearchParams(window.location.search).get('id');
  recommendationWidget.renderSimilar('similar-products', productId, 5);
  recommendationWidget.renderTogether('together-products', productId, 4);
</script>

<!-- Часто покупают вместе -->
<div id="together-products"></div>
```

### На странице корзины

```html
<div id="cart-recommendations"></div>
<script>
  // Получить рекомендации для товаров в корзине
  recommendationWidget.renderPopular('cart-recommendations', 4);
</script>
```

## 📈 Алгоритмы рекомендаций

### 1. Популярные товары
```
Популярность = (Просмотры × 0.3) + (Покупки × 0.7)
```
Товары сортируются по этому показателю в убывающем порядке.

### 2. Похожие товары
Критерии:
- Одна категория ✓
- Цена в диапазоне ±30% от исходного товара ✓
- Приоритет товарам в наличии ✓

### 3. Персональные рекомендации
1. Анализируем категории, которые смотрел пользователь
2. Ранжируем категории по количеству просмотров
3. Из топ категорий берём непросмотренные товары
4. Сортируем по популярности

### 4. Часто покупают вместе
1. Находим заказы с целевым товаром
2. Собираем emails таких покупателей
3. Оставляем товары, которые эти люди покупали также
4. Ранжируем по количеству совпадений

## 🔄 Отслеживание взаимодействий

### Автоматические отслеживания:
- Просмотр товара → `views++`
- Открытие страницы товара → логируется в localStorage
- Получение товарной карточки → может отслеживаться

### История пользователя:
```javascript
// Доступ к истории просмотров
console.log(recommendationWidget.userHistory);

// Добавить товар вручную
recommendationWidget.trackView(productId);
```

## 📊 Статистика и аналитика

```javascript
// Получить статистику рекомендаций
const stats = await recommendationWidget.getStats();
console.log(stats);
// {
//   totalProducts: 12,
//   totalOrders: 50,
//   productCategories: [...],
//   mostViewed: {...},
//   mostPurchased: {...},
//   averageOrderValue: 1500
// }
```

## 🎨 Настройка

### Параметры контроллера рекомендаций:

```javascript
const recommender = new RecommendationWidget({
  apiBase: '/api/recommendations'  // URL к API
});
```

### Параметры запросов API:

```javascript
// Получить 8 популярных товаров
fetch('/api/recommendations/popular?limit=8')

// Получить 10 похожих товаров
fetch('/api/recommendations/similar/5?limit=10')
```

## 📱 Категории товаров "Лавки"

Система работает со следующими категориями:
- **Палочки** - 🪄️ волшебные палочки
- **Зелья** - 🧪 магические зелья
- **Книги заклинаний** - 📖 знания
- **Артефакты** - 💎 магические предметы
- **Транспорт** - 🧹 метлы и средства передвижения
- **Средства** - 🧴 вспомогательные материалы
- **Аксессуары** - 👒 дополнения и украшения
- **Еда** - 🍬 волшебные сладости

## 🔧 Интеграция в существующий код

### Обновлен `server.js`:
```javascript
// Добавлены импорты
const RecommendationService = require('./recommendations');
const recommendationsRouter = require('./recommendationsRouter');

// Подключен router
app.use('/api/recommendations', recommendationsRouter);

// Отслеживание просмотров в маршруте товара
app.get('/api/products/:id', (req, res) => {
  const product = products.find(p => p.id === parseInt(req.params.id));
  if (!product) return res.status(404).json({ error: '...' });
  
  product.views = (product.views || 0) + 1; // Увеличиваем счетчик
  res.json(product);
});
```

### Обновлена база данных:
Все товары теперь имеют поля:
- `views` - количество просмотров
- `cartAdds` - добавления в корзину
- `rating` - рейтинг (0-5)
- `ratingCount` - количество оценок

## 📚 Примеры использования

### Пример 1: Главная страница
```html
<div class="recommendations-section">
  <div id="home-recommendations"></div>
</div>

<script>
  recommendationWidget.renderHome('home-recommendations', 6);
</script>
```

### Пример 2: Модальное окно товара
```html
<div class="product-modal">
  <div id="product-detail">...</div>
  <div class="recommendations">
    <div id="similar"></div>
    <div id="together"></div>
  </div>
</div>

<script>
  const productId = parseInt(document.currentScript.dataset.productId);
  recommendationWidget.renderSimilar('similar', productId, 4);
  recommendationWidget.renderTogether('together', productId, 3);
</script>
```

### Пример 3: Рекомендации в корзине
```html
<div class="cart-page">
  <div class="cart-items">...</div>
  <div class="recommendations">
    <h2>✨ Рекомендуем добавить в корзину:</h2>
    <div id="cart-recs"></div>
  </div>
</div>

<script>
  // Берем ID первого товара из карзины для рекомендаций
  const firstCartItem = cartItems[0];
  if (firstCartItem) {
    recommendationWidget.renderTogether('cart-recs', firstCartItem.id, 4);
  }
</script>
```

## 🌟 Особенности системы

✨ **Адаптивная загрузка** - работает на всех устройствах  
🎯 **Персоналізированные рекомендации** - на основе истории  
📊 **Умное ранжирование** - учитывает популярность и тип товара  
🔄 **Кэширование** - история просмотров сохраняется локально  
⚡ **Быстрая загрузка** - оптимизированные запросы  
🎨 **Красивый дизайн** - стилизовано под тему магазина  

## 🐛 Отладка

```javascript
// Включить логирование
window.DEBUG_RECOMMENDATIONS = true;

// Посмотреть историю просмотров
console.log('История:', recommendationWidget.userHistory);

// Очистить историю
localStorage.removeItem('wizardShopHistory');

// Проверить API
fetch('/api/recommendations/stats').then(r => r.json()).then(console.log);
```

## 📝 Лицензия

Этот модуль является частью учебного проекта "Лавка у Оливандера".

---

**Дата создания:** 2 апреля 2026 г.  
**Версия:** 1.0.0  
**Автор:** Система интеллектуальных рекомендаций 🪄
