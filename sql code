-- Retrieve the total number of orders placed.
select count(distinct order_id) as 'Total Orders' from orders;

-- Calculate the total revenue generated from pizza sales.

-- to see the details
select order_details.pizza_id, order_details.quantity, pizzas.price
from order_details 
join pizzas on pizzas.pizza_id = order_details.pizza_id;

-- to get the answer
select cast(sum(order_details.quantity * pizzas.price) as decimal(10,2)) as 'Total Revenue'
from order_details 
join pizzas on pizzas.pizza_id = order_details.pizza_id;

-- Identify the highest-priced pizza.
-- using LIMIT function for databases like MySQL
select pizza_types.name as 'Pizza Name', cast(pizzas.price as decimal(10,2)) as 'Price'
from pizzas 
join pizza_types on pizza_types.pizza_type_id = pizzas.pizza_type_id
order by price desc
limit 1;

-- Alternative (using window function) - for databases supporting window functions
with cte as (
    select pizza_types.name as 'Pizza Name', cast(pizzas.price as decimal(10,2)) as 'Price',
           rank() over (order by price desc) as rnk
    from pizzas
    join pizza_types on pizza_types.pizza_type_id = pizzas.pizza_type_id
)
select 'Pizza Name', 'Price' 
from cte 
where rnk = 1;

-- Identify the most common pizza size ordered.

select pizzas.size, count(distinct order_id) as 'No of Orders', sum(quantity) as 'Total Quantity Ordered' 
from order_details
join pizzas on pizzas.pizza_id = order_details.pizza_id
group by pizzas.size
order by count(distinct order_id) desc;

-- List the top 5 most ordered pizza types along with their quantities.

select pizza_types.name as 'Pizza', sum(quantity) as 'Total Ordered'
from order_details
join pizzas on pizzas.pizza_id = order_details.pizza_id
join pizza_types on pizza_types.pizza_type_id = pizzas.pizza_type_id
group by pizza_types.name 
order by sum(quantity) desc
limit 5;

-- Join the necessary tables to find the total quantity of each pizza category ordered.

select pizza_types.category, sum(quantity) as 'Total Quantity Ordered'
from order_details
join pizzas on pizzas.pizza_id = order_details.pizza_id
join pizza_types on pizza_types.pizza_type_id = pizzas.pizza_type_id
group by pizza_types.category 
order by sum(quantity) desc
limit 5;

-- Determine the distribution of orders by hour of the day.

select extract(hour from time) as 'Hour of the day', count(distinct order_id) as 'No of Orders'
from orders
group by extract(hour from time) 
order by 'No of Orders' desc;

-- Find the category-wise distribution of pizzas

select category, count(distinct pizza_type_id) as 'No of pizzas'
from pizza_types
group by category
order by 'No of pizzas';

-- Calculate the average number of pizzas ordered per day.

with cte as (
    select orders.date as 'Date', sum(order_details.quantity) as 'Total Pizza Ordered that day'
    from order_details
    join orders on order_details.order_id = orders.order_id
    group by orders.date
)
select avg('Total Pizza Ordered that day') as 'Avg Number of pizzas ordered per day' from cte;

-- Alternative using subquery
select avg('Total Pizza Ordered that day') as 'Avg Number of pizzas ordered per day' 
from (
    select orders.date as 'Date', sum(order_details.quantity) as 'Total Pizza Ordered that day'
    from order_details
    join orders on order_details.order_id = orders.order_id
    group by orders.date
) as pizzas_ordered;

-- Determine the top 3 most ordered pizza types based on revenue.

select pizza_types.name, sum(order_details.quantity * pizzas.price) as 'Revenue from pizza'
from order_details 
join pizzas on pizzas.pizza_id = order_details.pizza_id
join pizza_types on pizza_types.pizza_type_id = pizzas.pizza_type_id
group by pizza_types.name
order by 'Revenue from pizza' desc
limit 3;

-- Advanced: Calculate the percentage contribution of each pizza type to total revenue.

select pizza_types.name, 
concat(cast((sum(order_details.quantity * pizzas.price) /
    (select sum(order_details.quantity * pizzas.price) 
     from order_details 
     join pizzas on pizzas.pizza_id = order_details.pizza_id)) * 100 as decimal(10,2)), '%') as 'Revenue Contribution'
from order_details 
join pizzas on pizzas.pizza_id = order_details.pizza_id
join pizza_types on pizza_types.pizza_type_id = pizzas.pizza_type_id
group by pizza_types.name
order by 'Revenue Contribution' desc; 

-- Analyze the cumulative revenue generated over time.

with cte as (
    select orders.date as 'Date', cast(sum(order_details.quantity * pizzas.price) as decimal(10,2)) as Revenue
    from order_details 
    join orders on order_details.order_id = orders.order_id
    join pizzas on pizzas.pizza_id = order_details.pizza_id
    group by orders.date
)
select Date, Revenue, sum(Revenue) over (order by Date) as 'Cumulative Revenue'
from cte
order by Date;

-- Determine the top 3 most ordered pizza types based on revenue for each pizza category.

with cte as (
    select pizza_types.category, pizza_types.name, cast(sum(order_details.quantity * pizzas.price) as decimal(10,2)) as Revenue
    from order_details 
    join pizzas on pizzas.pizza_id = order_details.pizza_id
    join pizza_types on pizza_types.pizza_type_id = pizzas.pizza_type_id
    group by pizza_types.category, pizza_types.name
)
, ranked_cte as (
    select category, name, Revenue, rank() over (partition by category order by Revenue desc) as rnk
    from cte
)
select category, name, Revenue
from ranked_cte
where rnk <= 3
order by category, Revenue desc
;

