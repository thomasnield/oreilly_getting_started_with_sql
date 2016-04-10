SELECT BillingCountry as COUNTRY,
SUM(Total) as TOTAL_SALES

FROM Invoice
GROUP BY BillingCountry
