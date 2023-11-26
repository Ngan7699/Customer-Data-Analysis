
````sql
select (total_churn*100)/total_customer as ChurnRate
from
(select SUM(CASE 
				WHEN status='unactive' then 1
				else 0
			end) as total_churn,
		COUNT(distinct CustomerKey) as total_customer
from
(select CustomerKey, status, SUM(SalesAmount) as Total_Sales, count (CustomerKey) as Total_Transaction
from 
(select d.CustomerKey,
	   d.SalesAmount,
	   case 
			when d.date_diff <= 180 then 'active'
			else 'unactive'
		end as status
from

(select c.CustomerKey,c.SalesAmount, DATEDIFF(day, max_orderdate, max_date) date_diff 
from
		(Select a.CustomerKey, a.SalesAmount, a.max_date, b.max_orderdate
		from
		(select CustomerKey, SalesAmount, OrderDate,(select MAX(OrderDate) from FactInternetSales) as max_date
		from FactInternetSales) a         ---print max date of dataset
		left join
		(select CustomerKey, max (OrderDate) as max_orderdate 
		from FactInternetSales
		group by CustomerKey) b          ---print the latest purchase date by the customer
		on a.CustomerKey = b.CustomerKey) c  
) d) e
group by CustomerKey, status) f) g ````
