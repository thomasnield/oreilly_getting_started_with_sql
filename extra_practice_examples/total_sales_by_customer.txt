SELECT 
Customer.CustomerId,
FirstName,
LastName,

SUM(Total) as TotalSales

FROM Invoice INNER JOIN Customer 
ON Invoice.CustomerId = Customer.CustomerId

GROUP BY 1,2,3
