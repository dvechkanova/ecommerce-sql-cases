# Города, которые тратят больше среднего

## Задача
По каждому городу посчитать суммарную выручку за доставленные заказы 2026 года.  
Затем для каждого города определите ранг по выручке и вывести только те города, чья выручка выше средней выручки по всем городам.
Полная схема данных — в файле [DATA-SCHEMA.md](https://github.com/dvechkanova/sql-practice/blob/main/DATA-SCHEMA.md).

### Использовала
CTE, оконные функции RANK() и AVG() OVER (), метрика «выручка города выше средней»

```sql
-- Находим выручку по городам
WITH city_revenue AS (
    SELECT u.city,
           SUM(o_i.price * o_i.quantity) AS revenue
    FROM users AS u
    INNER JOIN orders AS o      ON o.user_id = u.user_id
    INNER JOIN order_items AS o_i ON o_i.order_id = o.order_id
    WHERE o.status = 'delivered'
      AND o.created_at >= DATE '2026-01-01'
      AND o.created_at <  DATE '2027-01-01'
    GROUP BY u.city
),
-- Присваиваем каждому городу ранг по выручке
ranked AS (
    SELECT city,
           revenue,
           RANK() OVER (ORDER BY revenue DESC) AS revenue_rank,
           AVG(revenue) OVER ()                AS avg_revenue
    FROM city_revenue
)
-- Отбираем города с выручкой выше среднего
SELECT city, revenue, revenue_rank
FROM ranked
WHERE revenue > avg_revenue
ORDER BY revenue_rank;
```
