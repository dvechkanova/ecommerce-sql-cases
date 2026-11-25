# Карточка: воронка событий (e-commerce)
  
## Задача
Оценить конверсию на трёх этапах взаимодействия с сайтом за декабрь 2025: просмотр страницы → добавление в корзину → покупка.
Полная схема данных — в файле [DATA-SCHEMA.md](DATA-SCHEMA.md).
  
### Использовала
  CTE, LEFT JOIN, подсчёт конверсии.
  
```sql
-- Пользователи, просмотревшие страницу
WITH viewers AS (
    SELECT DISTINCT user_id
    FROM events
    WHERE event_name = 'page_view'
      AND DATE_TRUNC('month', event_ts) = DATE '2025-12-01'
), 
-- Пользователи, добавившие товар в корзину
adders AS (
    SELECT DISTINCT user_id
    FROM events
    WHERE event_name = 'add_to_cart'
      AND DATE_TRUNC('month', event_ts) = DATE '2025-12-01'
),
-- Пользователи, совершившие покупку
purchasers AS (
    SELECT DISTINCT user_id
    FROM events
    WHERE event_name = 'purchase'
      AND DATE_TRUNC('month', event_ts) = DATE '2025-12-01'
)
-- Соединяем шаги воронки: считаем, сколько просмотревших пользователей добавили товар в корзину и сколько из них купили
SELECT
    COUNT(v.user_id) AS viewers,
    COUNT(a.user_id) AS adders,
    COUNT(p.user_id) AS purchasers,
    ROUND(COUNT(a.user_id) * 100.0 / COUNT(v.user_id), 1) AS step1_pct,
    ROUND(COUNT(p.user_id) * 100.0 / COUNT(a.user_id), 1) AS step2_pct
FROM viewers AS v  
LEFT JOIN adders AS a     ON a.user_id = v.user_id
LEFT JOIN purchasers AS p ON p.user_id = a.user_id;
```
