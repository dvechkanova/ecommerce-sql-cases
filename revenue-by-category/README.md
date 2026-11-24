# Динамика выручки по категориям

## Задача. 
Посмотреть динамику выручки по категориям: текущий месяц и сравнение с предыдущим (только для доставленных заказов).

### Использовала
оконная функция, метрика «прирост к предыдущему периоду»

```SELECT tab.category,
       tab.month,
       tab.revenue,
       tab.prev_revenue,
       ROUND((tab.revenue - tab.prev_revenue) / tab.prev_revenue * 100.0, 1) AS change_pct
FROM (
    SELECT o_i.category,
           DATE_TRUNC('month', o.created_at) AS month,
           SUM(o_i.price * o_i.quantity) AS revenue,
           LAG(SUM(o_i.price * o_i.quantity)) OVER (
               PARTITION BY o_i.category
               ORDER BY DATE_TRUNC('month', o.created_at)
           ) AS prev_revenue
    FROM order_items AS o_i
    INNER JOIN orders AS o ON o.order_id = o_i.order_id
    WHERE o.status = 'delivered'
    GROUP BY o_i.category, month
) AS tab
ORDER BY tab.category, tab.month;
