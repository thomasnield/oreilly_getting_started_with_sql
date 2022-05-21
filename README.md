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
