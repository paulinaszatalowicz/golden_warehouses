#### Warehouses whose data I have analyzed aren't made of gold. Furthermore, trading industry is extremely competetive and unwatched may not give much income. Thus, it is crucial to keep an eye on every piece of data that depicts the business. I have imported a .csv files given on one of my Google courses to check how profitable are the warehouses. ####

#### First, I have created aliases, temporary names, to simplify work and make it easier to write this complicated query. I have aggregated data from two tables and created new columns that combine data from many columns to create unique indexes to compare sales in specific warehouses. Then, I have written a rule to output when the fulfillment of orders exceeds the level of 20% and 60%. To make it easier to read, I have named this comparison by a single query. Additionaly, I have excluded from the analysis those warehouses under construction, having no fulfilled orders.

```SQL
SELECT 
    Warehouse.warehouse_id,
    CONCAT(Warehouse.state, ': ', Warehouse.warehouse_alias) AS warehouse_name,
    COUNT(ORDERS.order_id) AS number_of_orders,
    (
        SELECT 
            COUNT(*)
        FROM 
            warehouse_orders.Orders Orders
    )
AS total_orders,
CASE 
    WHEN COUNT(Orders.order_id)/(SELECT COUNT(*) FROM warehouse_orders.Orders Orders) <= 0.20
    THEN "fulfilled 0-20% of orders"
    WHEN COUNT(Orders.order_id)/(SELECT COUNT(*) FROM warehouse_orders.Orders Orders) > 0.20
    AND COUNT(Orders.order_id)/(SELECT COUNT(*) FROM warehouse_orders.Orders Orders) <= 0.60
    THEN "fulfilled 21-60% of orders"
ELSE 
    "fulfilled more than 60% of orders"
END AS fulfillment_summary
FROM 
    `llaallala.warehouse_orders.Orders` Warehouse
LEFT JOIN warehouse_orders.orders Orders
    ON Orders.warehouse_id = Warehouse.warehouse_id
GROUP BY 
    Warehouse.warehouse_id,
    Warehouse.name
HAVING 
    COUNT(Orders.order_id > 0)
```
