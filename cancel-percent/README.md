# Доля отмен

## Задача
Посчитать количестов заказов, чило отмен и долю отмен по каждому городу.
Полная схема данных — в файле [DATA-SCHEMA.md](https://github.com/dvechkanova/sql-practice/blob/main/DATA-SCHEMA.md).

### Использовала
CTE, LEFT JOIN, метрика доля отмен.

```sql
-- Находим количество заказов по городам
WITH ord_tab AS (
    SELECT u.city, COUNT(*) AS count_ord
    FROM orders AS o
    INNER JOIN users AS u ON o.user_id = u.user_id
    GROUP BY u.city
),
-- Находим количество отмененных заказов по городам
cancel_ord AS (
    SELECT u.city, COUNT(*) AS count_come
    FROM orders AS o
    INNER JOIN users AS u ON o.user_id = u.user_id
    WHERE o.status = 'cancelled'
    GROUP BY u.city
)
-- Находим долю отмененых заказов по городам
SELECT t.city,
       t.count_ord,
       count_come,
       ROUND(c.count_come * 100.0 / t.count_ord, 1) AS cancel_pct
FROM ord_tab AS t
LEFT JOIN cancel_ord AS c ON c.city = t.city
ORDER BY cancel_pct DESC;
```
