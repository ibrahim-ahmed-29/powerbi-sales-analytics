# Data Dictionary

## dim_customer
| Column | Description |
|---|---|
| customer_id | Unique customer identifier |
| customer_name | Customer's name |
| segment | Customer segment/tier |
| account_manager | Assigned account manager |
| payment_terms | Agreed payment terms |
| contact_name | Primary contact name |
| contact_email | Primary contact email |
| credit_limit | Approved credit limit |
| phone | Contact phone number |
| street | Street address |
| city | City |
| region | Region (used for dynamic RLS) |

## dim_product
| Column | Description |
|---|---|
| product_key | Unique product identifier |
| product_code | Product SKU/code |
| product_name | Product name |
| brand | Brand name |
| subcategory | Product subcategory |
| category | Product category |
| price | List price |
| supplier | Supplier name |

## dim_geo
| Column | Description |
|---|---|
| geo_key | Unique location identifier |
| city | City name |
| region | Region name |

Used as a role-playing dimension in `fact_sales` via `bill_to_city_key` and `ship_to_city_key`.

## dim_order_flag
| Column | Description |
|---|---|
| flag_key | Unique flag identifier |
| channel_code | Channel code |
| channel | Sales channel (e.g. online, retail) |
| status | Order status flag |
| priority | Order priority flag |

## dim_campaign
| Column | Description |
|---|---|
| campaign_key | Unique campaign identifier |
| campaign_name | Campaign name |
| channel | Marketing channel |
| start_date | Campaign start date |
| end_date | Campaign end date |
| budget | Allocated campaign budget |

## dim_date
| Column | Description |
|---|---|
| date | Calendar date |
| Year | Year (calculated) |
| Month | Month name (calculated) |
| MonthNo | Month number (calculated) |
| Quarter | Quarter label (calculated) |
| QuarterNo | Quarter number (calculated) |
| Day | Day of month (calculated) |

## security (RLS table)
| Column | Description |
|---|---|
| user_email | Signed-in user's email, used to filter `dim_customer` by region |
| region | Region the user is permitted to see |

## fact_sales
| Column | Description |
|---|---|
| line_id | Unique order line identifier |
| order_id | Order identifier |
| product_key | FK to dim_product |
| customer_id | FK to dim_customer |
| flag_key | FK to dim_order_flag |
| quantity | Units sold on this line |
| unit_price | Price per unit |
| unit_cost | Cost per unit |
| discount_pct | Discount percentage applied |
| line_total | Total revenue for the line |
| order_date | Date the order was placed |
| bill_to_city_key | FK to dim_geo (billing location) |
| ship_to_city_key | FK to dim_geo (shipping location) |

## fact_inventory
| Column | Description |
|---|---|
| product_key | FK to dim_product |
| Month | Month of the inventory snapshot |
| units | Units on hand |

## fact_promotion_coverage
| Column | Description |
|---|---|
| campaign_key | FK to dim_campaign |
| product_key | FK to dim_product |

## fact_campaign_spend
| Column | Description |
|---|---|
| campaign_key | FK to dim_campaign |
| date | Date of the spend record |
| impressions | Number of impressions |
| clicks | Number of clicks |
| spend | Amount spent |

## fact_order_process
*(Accumulating snapshot — one row per order, updated as it moves through milestones)*
| Column | Description |
|---|---|
| order_id | Order identifier |
| customer_id | FK to dim_customer |
| order_date | Date order was placed |
| ship_date | Date order was shipped |
| delivery_date | Date order was delivered |
| invoice_date | Date invoice was issued |
| pay_date | Date payment was received |
| order_to_pay | Calculated column: `DATEDIFF(order_date, pay_date, DAY)` — days from order to payment |

## fact_sales_target
| Column | Description |
|---|---|
| date | Target period date |
| target_revenue | Revenue target for the period |

## _measures
Disconnected table holding the model's DAX measures (see main README for definitions):
`total_sales`, `total_orders`, `total_customers`, `total_active_customers`, `average_order_to_pay`
