26. Find out the average price of PCs and laptops produced by maker A.
Result set: one overall average price for all items.

```
> SELECT AVG(combined.price)
> FROM Product
> JOIN (SELECT pc.model, pc.price FROM PC as pc
>       UNION ALL SELECT lap.model, lap.price FROM Laptop as lap) AS combined
> ON Product.model = combined.model
> WHERE Product.maker = 'A';
```

27. Find out the average hard disk drive capacity of PCs produced by makers who also manufacture printers.
Result set: maker, average HDD capacity.

```
> SELECT Product.maker, AVG(PC.hd)
> FROM Product
> JOIN PC ON Product.model = PC.model
> WHERE Product.maker IN (SELECT maker FROM Product
> WHERE type = 'Printer')
> GROUP BY Product.maker;
```

Short database description "Painting":

The database schema consists of 3 tables:
utQ (Q_ID int, Q_NAME varchar(35)), utV (V_ID int, V_NAME varchar(35), V_COLOR char(1)), utB (B_Q_ID int, B_V_ID int, B_VOL tinyint, B_DATETIME datetime).
The utQ table contains the identifiers and names of squares, the initial color of which is black. (Note: black is not a color and is considered unpainted. Only Red, Green and Blue are colors.) 
The utV table contains the identifiers and names of spray cans and the color of paint they are filled with.
The utB table holds information on squares being spray-painted, and contains the square and spray can identifiers, the quantity of paint being applied, and the time of the painting event.
It should be noted that
- a spray can may contain paint of one of three colors: red (V_COLOR='R'), green (V_COLOR='G'), or blue (V_COLOR='B');
- any spray can initially contains 255 units of paint;
- the square color is defined in accordance with the RGB model, i.e. R=0, G=0, B=0 is black, whereas R=255, G=255, B=255 is white;
- any record in the utB table decreases the paint quantity in the corresponding spray can by B_VOL and accordingly increases the amount of paint applied to the square by the same value;
- B_VOL must be greater than 0 and less or equal to 255;
- the paint quantity of a single color applied to one square can’t exceed 255, and there can’t be a less than zero amount of paint in a spray can;
- the time of the painting event (B_DATETIME) is specified with one second precision, i.e. it does not contain milliseconds;
- for historical reasons, the spray cans are referred to as “balloons” by many of the exercises, and the utV table contains spray can names (V_NAME column) such as “Balloon # 01”, etc.

28. Determine the average quantity of paint per square with an accuracy of two decimal places.

```
> SELECT 
CAST(SUM(ISNULL(utB.B_VOL*1.0,0))/COUNT(DISTINCT(utQ.Q_ID)) AS NUMERIC(6,2))
> FROM utQ LEFT JOIN utB ON utQ.Q_ID = utB.B_Q_ID
``` 

Note the implicit conversion in ```utB..B_VOL*1.0```. LEFT JOIN is required because ```utB``` and ```utQ``` have different number of squares listed and ```utQ``` includes the full list including squares not painted on.


Short database description "Recycling firm":

The firm owns several buy-back centers for collection of recyclable materials. Each of them receives funds to be paid to the recyclables suppliers. Data on funds received is recorded in the table 
Income_o(point, date, inc)
The primary key is (point, date), where point holds the identifier of the buy-back center, and date corresponds to the calendar date the funds were received. The date column doesn’t include the time part, thus, money (inc) arrives no more than once a day for each center. Information on payments to the recyclables suppliers is held in the table
Outcome_o(point, date, out)
In this table, the primary key (point, date) ensures each buy-back center reports about payments (out) no more than once a day, too. 
For the case income and expenditure may occur more than once a day, another database schema with tables having a primary key consisting of the single column code is used:
Income(code, point, date, inc)
Outcome(code, point, date, out)
Here, the date column doesn’t include the time part, either.

29. Under the assumption that receipts of money (inc) and payouts (out) are registered not more than once a day for each collection point [i.e. the primary key consists of (point, date)], write a query displaying cash flow data (point, date, income, expense). 
Use Income_o and Outcome_o tables.


