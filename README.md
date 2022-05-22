# Queries for all the examples chapter-wise

# Chapter 4 **SELECT** <a name='1'>

### Retrieving Data
```
select * FROM PRODUCT;
```

### Expressions in SELECT statements
```
select *,PRICE*1.07 FROM PRODUCT;
select *,PRICE*1.07 AS TAXED_PRICE FROM PRODUCT;
select *,round(PRICE*1.07,0) AS TAXED_PRICE FROM PRODUCT;
```

### Describe Table
```
pragma table_info('customer');
```

### Text Concatenation
```
select name, city||' '||state as location from customer;
select name, region|| ' ' || street_address || ',' || city || ',' || state || ',' || zip as shipping_address from customer;
```

# Chapter 5 **WHERE** <a name =2>
### Using Where on Numbers
```
select * from station_data where year>2005 and year<2010 ;
select * from station_data where year between 2005 and 2010;
```
### AND, OR and IN statements
```
select * from station_data where month=3 or month=6 or month=9 or month=12;
select * from station_data where month in (3,6,9,12);
select * from station_data where month not in (3,6,9,12);
select * from station_data where month%3=0;
```

### Using WHERE on Text
```
select * from station_data where length(report_code)=6;
select * from station_data where report_code like 'b%';
select * from station_data where report_code like 'b_c%';
```

### Using WHERE on Booleans
```
select * from station_data where tornado=1 and hail=1;
select * from station_data where tornado and hail;
select * from station_data where tornado=0 and hail;
select * from station_data where not tornado and hail;
```
### Handling NULL
```
select * from station_data where snow_depth is NULL;

//coalesce() function
select report_code, coalesce(precipitation,0) as rainfall from station_data where precipitation <=0.5;
select * from station_data where (rain=1 and temperature<=32) or snow_depth>0;
```
# Chapter 6 **GROUP BY and ORDER BY**
### Grouping Records
```
select count(*) from station_data where tornado;
select year, month, count(*) as count from station_data group by year;

// Grouping using ordinal positions
select count(*) from station_data where tornado;
select year, month, count(*) as count from station_data group by 1;
```
### Ordering Records
```
select * from station_data where tornado=1 group by 1,2 order by 1,4 desc;
```
### Aggregate Functions
```
//sum() function
select year, sum(snow_depth) from station_data where year>=2000 group by year;

//max() function
select year, sum(snow_depth) as total_snow , sum(precipitation) as total_precipitation, max(precipitation) from station_data where year>=2000 and tornado group by year;
```
### HAVING Statement
```
select year, sum(precipitation) from station_data  group by year having precipitation>0.5;
```
### Getting DISTINCT Records
```
select DISTINCT STATION_NUMBER FROM STATION_DATA ORDER BY STATION_NUMBER ASC;
```

# Chapter 7 **CASE Statements**

### The CASE Statement
```
select report_code,year,month,wind_speed, 
    CASE 
        when wind_speed>=4 then 'high' 
        when wind_speed between 3 and 4 then 'moderate' 
        else 'low' end as wind_severity 
from station_data;

//More efficient method
select report_code,year,month,wind_speed, 
CASE 
    when wind_speed>=4 then 'high' 
    when wind_speed >=3 then 'moderate' 
    else 'low' 
END AS wind_severity 
from station_data;
```

### Grouping CASE Statements
```
select year, 
CASE 
    when wind_speed>=4 then 'high' 
    when wind_speed >=3 then 'moderate' 
    else 'low' 
END AS wind_severity ,
count(*) as count
from station_data 
GROUP BY 1,2;
```

### The "Zero/Null" CASE Trick
```
select year, month,
sum(case 
    when tornado=0 then precipitation
    else 0 END)
AS non_tornado_precipitation,
sum(case 
    when tornado=1 then precipitation
    else 0 END)
as tornado_precipitation
from station_data
group by year,month;
```
```
select year,month,
max(case when tornado=1 then precipitation else Null end) as max_precipitation_tornado,
max (case when tornado=0 then precipitation else NULL end) as max_precipitation_non_tornado
from station_data
group by year;
```
```
select month,
avg(case when rain or hail then temperature else NULL end) as avg_preicpitation_temp,
avg(case when not (rain or hail) then temperature else NULL end) as avg_non_precipitation_temp
from station_data
where year>2000
group by month
```

# Chapter 8 **JOIN**
## INNER JOIN
```
select order_id, customer.customer_id, order_date, ship_date, name, street_address, street_Address, city, state, zip,product_id, order_qty
from customer inner join customer_order 
on customer.CUSTOMER_ID= customer_order.CUSTOMER_ID;
```
## LEFT JOIN
```
select order_id, customer_id, order_date, 
ship_date, name, street_address, street_Address, 
city, state, zip,product_id, order_qty
from customer LEFT join customer_order 
on customer.CUSTOMER_ID= customer_order.CUSTOMER_ID;
```
```
SELECT ORDER_ID, CUSTOMER_ID,NAME 
FROM CUSTOMER LEFT JOIN CUSTOMER_ORDER
ON CUSTOMER.CUSTOMER_ID = CUSTOMER_ORDER.CUSTOMER_ID
WHERE ORDER_ID IS NULL;
```
## Other JOIN Types
### RIGHT JOIN and OUTER JOIN
Not featured in SQLite due to their highly niche nature.

## Joining Multiple Tables
```
SELECT ORDER_ID, customer.CUSTOMER_ID, name, 
street_address,city,state,zip,order_date,product_id,description, order_qty
FROM CUSTOMER 
INNER JOIN CUSTOMER_ORDER 
ON CUSTOMER.CUSTOMER_ID = CUSTOMER_ORDER.CUSTOMER_ID 
INNER JOIN PRODUCT
ON CUSTOMER_ORDER.PRODUCT_ID=PRODUCT.PRODUCT_ID ;
```
```
SELECT ORDER_ID, customer.CUSTOMER_ID, name, 
street_address,city,state,zip,order_date,product_id,description, order_qty, ORDER_QTY*PRICE AS REVENUE
FROM CUSTOMER 
INNER JOIN CUSTOMER_ORDER 
ON CUSTOMER.CUSTOMER_ID = CUSTOMER_ORDER.CUSTOMER_ID 
INNER JOIN PRODUCT
ON CUSTOMER_ORDER.PRODUCT_ID=PRODUCT.PRODUCT_ID ;
```
## Grouping JOINs
```

```
