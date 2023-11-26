
````sql
SELECT (total_churn*100)/total_customer as ChurnRate
FROM
	(SELECT SUM(CASE WHEN status='unactive' then 1 ELSE 0 END) as total_churn,
		COUNT(distinct CustomerKey) as total_customer
	FROM
		(SELECT CustomerKey, status, SUM(SalesAmount) as Total_Sales, COUNT (CustomerKey) as Total_Transaction
		FROM 
			(SELECT d.CustomerKey, d.SalesAmount, CASE
								WHEN d.date_diff <= 180 THEN 'active'
								ELSE 'unactive'
								END as status
			FROM
				(SELECT c.CustomerKey,c.SalesAmount, DATEDIFF(day, max_orderdate, max_date) date_diff 
				FROM
					(SELECT a.CustomerKey, a.SalesAmount, a.max_date, b.max_orderdate
					FROM
						(SELECT	CustomerKey,
							SalesAmount,
							OrderDate,
							(select MAX(OrderDate) from FactInternetSales) as max_date
						 FROM FactInternetSales) a         ---print max date of dataset
					LEFT JOIN
					(SELECT CustomerKey, MAX(OrderDate) as max_orderdate 
					 FROM FactInternetSales
					GROUP BY CustomerKey) b          ---print the latest purchase date by the customer
					ON a.CustomerKey = b.CustomerKey) c  
				   ) d
				)  e
		group by CustomerKey, status
		) f
	) g
````
