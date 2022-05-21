# Queries for all the examples chapter-wise

## Chapter 4 **SELECT**

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

## Chapter 5 **WHERE**
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
## Chapter 6 **GROUP BY and ORDER BY**
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
