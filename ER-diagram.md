# ER-диаграмма VELVET & BEAN

Концептуальная модель данных сайта. На самом сайте базы данных нет: корзина и формы работают в браузере.
Диаграмма показывает, как эти данные хранились бы в реальной базе. GitHub рисует её автоматически.

```mermaid
erDiagram
  CATEGORY ||--o{ MENU_ITEM : contains
  CATEGORY ||--|{ OPTION_GROUP : defines
  OPTION_GROUP ||--|{ OPTION_CHOICE : offers
  MENU_ITEM ||--o{ ORDER_ITEM : ordered_as
  ORDERS ||--|{ ORDER_ITEM : includes
  ORDER_ITEM ||--o{ ORDER_ITEM_OPTION : has
  OPTION_CHOICE ||--o{ ORDER_ITEM_OPTION : selected_in
  ORDERS ||--|| PAYMENT : paid_by
  MEMBER |o--o{ ORDERS : places
  TIER ||--o{ MEMBER : ranks
  MEMBER |o--o{ RESERVATION : books
  LOCATION ||--o{ RESERVATION : hosts
  CATEGORY {
    string id PK
    string name
  }
  MENU_ITEM {
    string id PK
    string category_id FK
    string name
    string description
    decimal base_price
  }
  OPTION_GROUP {
    int id PK
    string category_id FK
    string label
  }
  OPTION_CHOICE {
    int id PK
    int group_id FK
    string label
    decimal price_delta
  }
  ORDERS {
    string order_no PK
    int member_id FK
    decimal subtotal
    decimal tax
    decimal total
    string currency
    timestamp created_at
  }
  ORDER_ITEM {
    int id PK
    string order_no FK
    string menu_item_id FK
    int quantity
    decimal unit_price
  }
  ORDER_ITEM_OPTION {
    int order_item_id FK
    int choice_id FK
  }
  PAYMENT {
    int id PK
    string order_no FK
    string cardholder
    string card_last4
    string status
  }
  MEMBER {
    int id PK
    int tier_id FK
    string name
    string email UK
    int points
  }
  TIER {
    int id PK
    string name
    int min_points
    string perks
  }
  LOCATION {
    int id PK
    string name
    string address
    string phone
    int open_hour
    int close_hour
  }
  RESERVATION {
    string ref PK
    int location_id FK
    int member_id FK
    string type
    string guest_name
    string email
    date visit_date
    time visit_time
    int guests
  }
  REVIEW {
    int id PK
    string quote
    string author
  }
  ROAST_STAGE {
    int id PK
    string name
    int temp_c
    string time_mark
    string description
  }
```

## Пояснения

- **Меню и опции.** У категории есть позиции и группы опций (молоко, сладость, температура, размер, помол). Доплата за вариант хранится в `price_delta`.
- **Заказ.** Строки заказа `ORDER_ITEM` связаны с выбранными опциями через `ORDER_ITEM_OPTION` (связь «многие ко многим»). У заказа одна запись об оплате `PAYMENT`, в ней только последние 4 цифры карты.
- **Валюта.** Цены хранятся в долларах, поле `currency` в `ORDERS` фиксирует валюту, в которой гость увидел итог (KZT, RUB или USD).
- **Клуб.** Участник `MEMBER` относится к одному уровню `TIER`.
- **Бронирования.** Бронь привязана к кофейне `LOCATION`. Участник клуба необязателен, как и в заказе.
- **Справочники.** `REVIEW` и `ROAST_STAGE` ни с чем не связаны. Переводы текстов на казахский, русский и английский в диаграмму не вынесены, они лежат в коде сайта.
- Таблица называется `ORDERS`, потому что `ORDER` зарезервировано в SQL.
