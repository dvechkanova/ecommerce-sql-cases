# Схема данных (интернет-магазин)

### orders — заказы.

order_id	PK  
user_id	FK → users  
created_at	TIMESTAMP  
status	delivered, cancelled и др.  

### order_items — позиции заказов.

order_item_id	PK  
order_id	FK → orders  
category	категория товара  
price	цена за единицу  
quantity	количество  

### users — пользователи.

user_id	PK  
city	город  

### events — события на сайте.

event_id	PK (опционально)  
user_id	FK → users  
event_ts	TIMESTAMP  
event_name	page_view, add_to_cart, purchase…  

### Связи
users (1) ←→ (N) orders  
orders (1) ←→ (N) order_items  
users (1) ←→ (N) events  
