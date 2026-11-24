# Динамика выручки по категориям

## Задача
Посмотреть динамику выручки по категориям: текущий месяц и сравнение с предыдущим (только для доставленных заказов).

### Использовала
Агрегация (`SUM`), оконная функция (`LAG`), метрика «прирост месяц к месяцу».

```sql

-- Прирост выручки месяц к месяцу по категориям
SELECT tab.category,
       tab.month,
       tab.revenue,
       tab.prev_revenue,
       ROUND((tab.revenue - tab.prev_revenue) / tab.prev_revenue * 100.0, 1) AS change_pct
FROM (
-- Выручка по категориям предыдущего месяца
    SELECT o_i.category,
           DATE_TRUNC('month', o.created_at) AS month,
           SUM(o_i.price * o_i.quantity) AS revenue,
           LAG(SUM(o_i.price * o_i.quantity)) OVER (
               PARTITION BY o_i.category
               ORDER BY DATE_TRUNC('month', o.created_at)
           ) AS prev_revenue
-- Соединяем позиции заказов с заказами и берём только доставленные
    FROM order_items AS o_i
    INNER JOIN orders AS o ON o.order_id = o_i.order_id
    WHERE o.status = 'delivered'
    GROUP BY o_i.category, month
) AS tab
ORDER BY tab.category, tab.month;
```
