# Java- Databases: Group By & Having

1. Select all orders that have total price greater than 300.
```
select od.order_id, sum(od.unit_price) from order_details od 
group by od.order_id 
having sum(od.unit_price) > 300
```
2. Select only employees that have more than 100 orders.
```
select o.employee_id, count(o.order_id) from orders o  
group by o.employee_id 
having count(o.order_id)>100
```
3. Select the latest order (order_id, customer_id, order_date) for each customer. Include only orders that were made after 1998-05-05.
```
select o.order_id, o.customer_id, o.order_date from orders o 
where o.order_date > '1998-05-05'
and o.order_id in (
select max(o2.order_id) 
from orders o2 
group by customer_id)
```
4. Select shipper company name and calculate how many orders have each.
```
select o.ship_via, s.company_name, count(o.order_id) from orders o 
left join shippers s 
on o.ship_via = s.shipper_id 
group by o.ship_via, s.company_name 
```
5. Select the most expensive order.
```
select max(order_value) order_value from 
(select sum(od.unit_price) as order_value from order_details od 
group by od.order_id ) as subquery
```
6. Select an order that have the most items in it.
```
select max(order_items_quantity) order_items_quantity from 
(select count(od.quantity) as order_items_quantity from order_details od 
group by od.order_id ) as subquery
```
7. Select orders that was shipped by company with id = 1 and contains more than 4 items.
```
select o.order_id, count(*) 
from orders o 
left join order_details od 
on o.order_id = od.order_id 
where o.ship_via = 1
group by o.order_id 
having count(*) > 4
order by o.order_id 
```
8. Select category id, category name and sumarize quantity of products for each category. Take into consideratation only categories with id: 1,2,3 and list only those where sum of products is bigger than 6000.
```
select p.category_id, c.category_name, sum(od.quantity) from order_details od 
join products p 
on od.product_id = p.product_id 
join categories c 
on p.category_id = c.category_id 
where p.category_id in (1,2,3)
group by p.category_id, c.category_name 
having sum(od.quantity) > 6000
order by p.category_id 
```
9. Make a report on the 10 best selling products with categories in 1997.
```

select distinct c.category_name, 
    p.product_name,
    sum(round(CAST(FLOAT8(od.unit_price * od.quantity * (1 - od.discount)) AS NUMERIC),2)) as total_value
from categories c
left join products p on c.category_id = p.category_id 
left join order_details od on od.product_id = p.product_id 
left join orders o on o.order_id = od.order_id 
where o.shipped_date between date('1997-01-01') and date('1997-12-31')
group by c.category_name, 
    p.product_name
order by total_value desc
limit 10;
```
10. Select the least popular categories of products for orders made in 1997.
```
select distinct c.category_name, 
    sum(round(CAST(FLOAT8(od.unit_price * od.quantity * (1 - od.discount)) AS NUMERIC),2)) as total_category_value
from categories c
left join products p on c.category_id = p.category_id 
left join order_details od on od.product_id = p.product_id 
left join orders o on o.order_id = od.order_id 
where o.shipped_date between date('1997-01-01') and date('1997-12-31')
group by c.category_name
order by total_category_value asc
limit 10;
```
