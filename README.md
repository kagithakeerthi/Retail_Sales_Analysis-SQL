use project1;
select * from sales;
-- change data types of columns
SET sql_safe_updates=0;

UPDATE sales
SET purchase_date = STR_TO_DATE(purchase_date, '%d-%m-%Y');
alter table sales
modify purchase_date date;

UPDATE sales
SET time_of_purchase = str_to_date(time_of_purchase,  '%H:%i:%s');
alter table sales
modify time_of_purchase time;

-- Rename column name
ALTER TABLE sales
RENAME COLUMN quantiy TO quantity;

alter table sales 
modify transaction_id Varchar(20);
alter table sales
modify customer_id varchar(20);
alter table sales
modify customer_name varchar(50);
alter table sales
modify gender varchar(10);
alter table sales
modify product_id varchar(20);
alter table sales
modify product_name varchar(20);
alter table sales
modify product_category varchar(20);
alter table sales
modify payment_mode varchar(20);
alter table sales
modify status varchar(10);


-- what are the top 5 most selling products by quantity?
 select 
      product_name,
      sum(quantity) as total_quantity
	from sales
    where status = 'delivered'
	group by product_name
    order by  total_quantity desc
    limit 5;

-- count of products cancelled?

select 
      product_name,
      count(status) as count_cancelled
	from sales
    where status ='cancelled'
    group by product_name
    order by count_cancelled desc;
    
-- what times of the day has the highest number of purchases?
        
        -- METHOD 1
        
select  time_of_purchase,
   count(status) as count_purchases
from sales
where status = 'delivered'
group by time_of_purchase
order by count_purchases desc;
    
     -- METHOD 2
     
select 
case when hour(time_of_purchase) between 6 and 11 then 'Morning'
     when hour(time_of_purchase) between 12 and 17 then 'afternoon'
     when hour(time_of_purchase) between 18 and 23 then 'evening'
     else 'night'
     end as time_of_day,
	count(*) as count_day_hours
    from sales
    group by time_of_day
    order by count_day_hours desc;
       
-- who are the top 5 highest spending customers?

select customer_name,customer_id,
   sum(quantity*price) as cost_spend
   from sales
    where status='delivered'
   group by customer_name,customer_id
   order by cost_spend desc
   limit 5;
  
  
-- which product categories generates the highest revenue?

select product_category,
sum(quantity*price) as total_cost
from sales
where status = 'delivered'
group by product_category
order by total_cost desc;

-- what is the return/cancellation rate per product category?
      -- Method 1
select product_category,
status,
avg(price) as rate
from sales
where status='returned' or 'cancelled'
group by product_category,status
order by rate desc;
    -- Method 2
select product_category,
count(*) as total_orders,
sum(status='returned') as returned_order,
sum(status='cancelled') as cancelled_order,
sum(status='returned')/count(*) as returned_rate,
sum(status='cancelled')/count(*) as cancelled_rate
from sales
group by product_category
order by total_orders desc;


-- what is the preferred payment mode?

select payment_mode,
count(payment_mode) as count_payment
from sales
group by payment_mode
order by count_payment desc;

-- how does age group affect purchasing behaviour?

select 
    case 
        when customer_age between 18 and 25 then '18-25'
        when customer_age between 26 and 35 then '26-35'
        when customer_age between 36 and 50 then '36-50'
        else '51+'
        end as age_group,
        sum(quantity*price) as cost_spend
	from sales
    group by age_group
    order by cost_spend desc;
        
-- what is the monthly sales trend?

select
  date_format(purchase_date,'%y-%m') as monthly_purchases,
  sum(quantity*price) as cost_purchased
  from sales
  group by monthly_purchases
  order by cost_purchased desc;
  
-- are certain genders buying more specific product categories?


select gender,
product_category,
count(product_category) as total_purchases
from sales
group by gender,product_category
order by total_purchases desc;
