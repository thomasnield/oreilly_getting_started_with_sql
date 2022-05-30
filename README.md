# Queries for all the examples chapter-wise

# Chapter 4 **SELECT** 

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

# Chapter 5 **WHERE** 
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
SELECT ORDER_ID, customer.CUSTOMER_ID, name, 
street_address,city,state,zip,order_date,product_id,description, order_qty, sum(ORDER_QTY*PRICE) AS totalRevenue
FROM CUSTOMER 
INNER JOIN CUSTOMER_ORDER 
ON CUSTOMER.CUSTOMER_ID = CUSTOMER_ORDER.CUSTOMER_ID 
INNER JOIN PRODUCT
ON CUSTOMER_ORDER.PRODUCT_ID=PRODUCT.PRODUCT_ID 
group by customer.customer_id, name;
```
```
SELECT ORDER_ID, customer.CUSTOMER_ID, name, 
street_address,city,state,zip,order_date,product_id,description, order_qty, sum(ORDER_QTY*PRICE) AS totalRevenue
FROM CUSTOMER 
left JOIN CUSTOMER_ORDER 
ON CUSTOMER.CUSTOMER_ID = CUSTOMER_ORDER.CUSTOMER_ID 
LEFT JOIN PRODUCT
ON CUSTOMER_ORDER.PRODUCT_ID=PRODUCT.PRODUCT_ID 
group by 2,3;
```
*NOTE*: Upon mixing INNER and LEFT join, INNER will always win and all the NULL values will get filtered out.
```
SELECT ORDER_ID, customer.CUSTOMER_ID, name, 
street_address,city,state,zip,order_date,product_id,description, order_qty, coalesce(sum(ORDER_QTY*PRICE),0) AS totalRevenue
FROM CUSTOMER 
left JOIN CUSTOMER_ORDER 
ON CUSTOMER.CUSTOMER_ID = CUSTOMER_ORDER.CUSTOMER_ID 
LEFT JOIN PRODUCT
ON CUSTOMER_ORDER.PRODUCT_ID=PRODUCT.PRODUCT_ID 
group by 2,3;
```
# CHAPTER 9 **Database Design**
## Creating a New Database and Tables
```
CREATE TABLE Company (
    Company_Id   INTEGER      PRIMARY KEY AUTOINCREMENT,
    Company_name VARCHAR (30) NOT NULL,
    Description  VARCHAR (60),
    Contact      INTEGER (10) NOT NULL
);

CREATE TABLE Attendees (
    Attendee_Id Integer Primary key autoincrement,
    Attendee_Name Varchar (60) NOT NULL,
    Phone Integer (10) not null,
    Email varchar (60),
    VIP boolean default(0)
);

create table Room (
    Room_Id integer primary key autoincrement,
    Floor_No integer not null,
    Seat_Capacity integer not null
);

create table Presentations (
    Presentation_Id Integer primary key autoincrement,
    Company_Id integer not null,
    Room_Id integer not null,
    Start_Time time,
    End_Time time,
    constraint fk_room
    foreign key (room_id)
    references room(room_id),
   constraint fk_companies
    foreign key(company_id)
    references company(company_id)
);

create table Presentation_Attendance (
    Ticket_Id integer primary key autoincrement,
    Attendee_Id integer ,
    Presentation_Id integer,
   constraint fk_attendees
    foreign key (attendee_id)
    references attendees (attendee_id),
   constraint fk_presentation
    foreign key (presentation_id)
    references presentation(presentation_id)
    
);

```
## Creating Views
```
CREATE VIEW Presentation_VW AS
    SELECT company.company_name AS name,
           room.room_id AS room_no,
           room.floor_no AS floor_no,
           room.seat_capacity AS seat_capacity,
           start_time,
           end_time
      FROM presentations
           INNER JOIN
           company ON company.company_id = presentations.company_id
           INNER JOIN
           room ON room.room_id = presentations.room_id;
```

# CHAPTER 10 **Managing Data**
## Insert
```
insert into attendee (name)
values('arzoo');
```
## Multiple Inserts
```
insert into attendee(name)
values
('b'),
('c'),
('d');
```
## Testing Foreign Keys
### Correcting Mistake- Altering table company to have a foregin key attendee_id
```
pragma foreign_keys=off;
begin transaction;
alter table company rename to company_old;

CREATE TABLE company (
    Company_Id   INTEGER      PRIMARY KEY AUTOINCREMENT,
    Company_Name VARCHAR (30) NOT NULL,
    Description  VARCHAR (60),
    Attendee_Id  INTEGER      NOT NULL
                              REFERENCES Attendees (Attendee_Id),
    CONSTRAINT fk_Attendees FOREIGN KEY (
        Attendee_Id
    )
    REFERENCES Attendees (Attendee_Id) 
);

insert into company select * from company_old;
commit;
pragma foreign_keys=on;
```

### Testing Foreign Keys
The following snippet throws error : FOREIGN KEY constraint failed
```
insert into company (company_name, attendee_id)
values ('rexapp solutions', 4);
```
The following snippet works:
```
insert into company (company_name, attendee_id)
values ('rexapp solutions', 3);
```

## DELETE
It deletes all records
```
delete from company where description is null;
```

## TRUNCATE TABLE
Not supported by sqlite but it is the preferred way of deleting records in MySQL,etc.
```
TRUNCATE TABLE ATTENDEES;
```

## UPDATE
```
update attendees set email=upper(email),
attendee_name=upper(attendee_name)
```

```
update attendees 
set vip=1
where attendee_id in (1,3);
```

##  DROP TABLE
```
drop table company_old;
```