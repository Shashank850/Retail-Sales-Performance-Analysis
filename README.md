use  Retail_Transactions;
select * from Retail_Transactions_Dataset;

select count(*)as transactions
from Retail_Transactions_Dataset;/*1000000*/

/*unique dataset*/

select count(distinct Customer_Name) as unique_customername /*329738*/
from Retail_Transactions_Dataset;

select round (SUM(total_cost),2) as total_revenue
from Retail_Transactions_Dataset;/*51928216.4*/

/*Update the Discount Status*/
SELECT
   Discount_applied,
    case
       when Discount_applied = 0 then 'False'
       when Discount_Applied = 1 then 'True'
    else 'Unknown'
end as discount_status
from Retail_Transactions_Dataset;

alter table Retail_Transactions_Dataset
add discount_status varchar(10);


update Retail_Transactions_Dataset
set discount_status = 
   case
      when discount_applied = 0 then 'False'
      when Discount_Applied = 1 then 'True'
      else 'Unknown'
    end;
select * from Retail_Transactions_Dataset;

/*Extract Annual Trend*/
with Annual_revenue as 
(
select
year  (date) as sales_year,
round(sum(total_cost),3) as Total_revenue,
round(avg(total_cost),2) as average_revenue_value,
avg(total_items)as average_item_per_transaction
from Retail_Transactions_Dataset
group by year (date)
--order by sales_year;
)
--Repeat Customer Analysis

with customer_summary as
(
select
Customer_name,
count(*) as Total_transaction_count,
round(sum(total_cost),3) as Total_revenue,
round(avg(total_cost),2) as average_revenue_value
from Retail_Transactions_Dataset
group by Customer_Name
--order by Total_revenue desc;
)
select 
      case
           when Total_transaction_count = 1 then 'One Time Customer'
           else 'Repeat Customer'
     end as customer_type,
    count(*)as customer,
    sum(Total_Revenue) as revenue,
    sum(Total_transaction_count) as transaction_per,
    avg(average_revenue_value) as average_customer_value
from customer_summary

group by

 case
  
   when Total_Transaction_count = 1 then 'One Time Customer'
else 'Repeat Customer'
end
order by revenue desc;

/*Split Function*/

SELECT
   Customer_name,

   Case
       when Customer_Name Like 'Dr.%'
            Then PARSENAME(REPLACE(Customer_name,'','.'),2)

       WHEN Customer_Name LIKE 'Mr. %'
            THEN PARSENAME(REPLACE(Customer_Name, ' ', '.'), 2)
        WHEN Customer_Name LIKE 'Mrs. %'
            THEN PARSENAME(REPLACE(Customer_Name, ' ', '.'), 2)
        ELSE LEFT(Customer_Name, CHARINDEX(' ', Customer_Name) - 1)
    END AS First_Name,

    CASE
        WHEN Customer_Name LIKE 'Dr. %'
            THEN PARSENAME(REPLACE(Customer_Name, ' ', '.'), 1)
        WHEN Customer_Name LIKE 'Mr. %'
            THEN PARSENAME(REPLACE(Customer_Name, ' ', '.'), 1)
        WHEN Customer_Name LIKE 'Mrs. %'
            THEN PARSENAME(REPLACE(Customer_Name, ' ', '.'), 1)
        ELSE SUBSTRING(
          
            Customer_Name,
            CHARINDEX(' ', Customer_Name) + 1,
            LEN(Customer_Name)
        )
    END AS Last_Name

FROM Retail_Transactions_Dataset;

/*Calculate with City's percentage contribution to total revenue*/
 
 select
 city,
 sum(Total_cost) as revenue,
 ROUND(
 100*sum(total_cost)/sum(sum(total_cost)) over(), 2
 )as revenue_share_percentage
 
 from  Retail_Transactions_Dataset
 group by City
 order by revenue desc;

 /* Revenue Performance by City */

 select
 city,
 count(*) as transaction_,
 sum(Total_cost) as revenue,
 avg(total_cost) as average_revenue_value,
 avg(Total_items) as average_Items_per_transaction,
 count(*) as Discount_rate
      from Retail_Transactions_Dataset
      where discount_status = 'True'
      group by city
      order by revenue desc;

/* Performance by store type */

select 
store_type,
count(*) as transaction_,
 sum(Total_cost) as revenue,
 avg(total_cost) as average_revenue_value,
 avg(Total_items) as average_Items_per_transaction
 from Retail_Transactions_Dataset
 group by Store_Type
 order by revenue desc;

 /* Seasonal Performance*/

 select 
 Season,
count(*) as transaction_,
 sum(Total_cost) as revenue,
 avg(total_cost) as average_revenue_value,
 avg(Total_items) as average_Items_per_transaction
 from Retail_Transactions_Dataset
 group by Season
 order by revenue desc;

 /* Revenue and basket value by promotion*/--handling Null and Missing values
  select
  coalesce(promotion,'No promotion') as promotion_type,
  count(*) as transaction_,
 sum(Total_cost) as revenue,
 avg(total_cost) as average_revenue_value,
 avg(Total_items) as average_Items_per_transaction
 from Retail_Transactions_Dataset
 group by coalesce( Promotion,'No promotion')
 order by average_revenue_value  desc;

 /* provides city and store performance together*/

 select 
 city,
 store_type,
 count(*) as transactions,
 sum(Total_cost) as revenue,
 avg(Total_cost) as average_revenue_value

 from Retail_Transactions_Dataset
 group by City,Store_Type
 order by revenue desc;
 
 /*Best Showing days*/
  
  select
  datename(WEEKDAY,[Date]) as day_of_week,
  count(*) as transactions,
 sum(Total_cost) as revenue,
 avg(Total_cost) as average_revenue_value
 from Retail_Transactions_Dataset
 group by DATENAME(WEEKDAY,[Date])
 order by revenue desc;

 /*Best Showing Date*/

  select
  datename(MONTH,[Date]) as day_of_week,
  count(*) as transactions,
 sum(Total_cost) as revenue,
 avg(Total_cost) as average_revenue_value
 from Retail_Transactions_Dataset
 group by DATENAME(MONTH,[Date])
 order by revenue desc;

 /*Best Showing Month*/

  select
  datename(YEAR,[Date]) as day_of_week,
  count(*) as transactions,
 sum(Total_cost) as revenue,
 avg(Total_cost) as average_revenue_value
 from Retail_Transactions_Dataset
 group by DATENAME(YEAR,[Date])
 order by revenue desc;

 
 




       





  













