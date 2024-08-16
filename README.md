# Advanced Data Analysis on Retail Performance Metrics

## Introduction
**Leveraging SQL and Python to Extract Actionable Insights from Retail Data and Drive Business Performance**

This project demonstrates my expertise in SQL for complex data analysis tasks, as well as Python for basket analysis. By calculating key metrics such as retention rates, growth indicators, burn rates, and more, I provide actionable insights to enhance business performance in the retail sector. The analysis is visualized using Plotly to provide clear and interactive insights.

## Project Overview

### Objective
Analyze retail performance data to uncover trends, identify growth opportunities, and optimize costs.

### Key Analyses
1. **Monthly Retention Rate**
   - Calculated for February, focusing on customers retained from January.
   - Retention rate analyzed by channel, with recommendations for improvement.

2. **Growth Metrics**
   - Percentage growth in orders, distinct retailers, average order price, and total revenues from January to February.

3. **Burn Analysis**
   - Monthly burn calculated for January and February, with strategies to reduce discount burn.

4. **Channel Contribution**
   - Weekly percentage contribution of each channel to total orders for January and February.

5. **Product Margin Analysis**
   - Top 20 products identified by total margin, along with those needing intervention.

6. **Category Contribution**
   - Change in category contribution between January and February, analyzed in terms of sales and margins.

7. **Retailer Ranking**
   - Retailers ranked based on business importance, with a discussion on additional relevant metrics.

8. **Basket Analysis**
   - Performed using Python and visualized with Plotly to uncover purchasing patterns and trends.

## Visualizations

### 1. Order Growth
![Order Growth](https://github.com/abdelrahmanfaheem/Growth-Sales-Analysis-Using-SQL/blob/main/Order%20Growth.png)

### 2. Retailer Growth
![Retailer Growth](https://github.com/abdelrahmanfaheem/Growth-Sales-Analysis-Using-SQL/blob/main/Reatiler%20Growth.png)

### 3. Retention Rate
![Retention Rate](https://github.com/abdelrahmanfaheem/Growth-Sales-Analysis-Using-SQL/blob/main/RetentionRate.png)

### 4. Top 10 Retailer Margins
![Top 10 Retailer Margins](https://github.com/abdelrahmanfaheem/Growth-Sales-Analysis-Using-SQL/blob/main/Top%2010%20Retailer%20Marging.png)

### 5. Total Revenues Growth for January and February
![Total Revenues Growth](https://github.com/abdelrahmanfaheem/Growth-Sales-Analysis-Using-SQL/blob/main/Total%20Revenues%20Growth%20for%20January%20and%20February.png)

### 6. Monthly Burn
![Monthly Burn](https://github.com/abdelrahmanfaheem/Growth-Sales-Analysis-Using-SQL/blob/main/monthly%20burn.png)

## SQL Code
### Functions Used in SQL Scripts
- **ALTER TABLE** - Used to add new columns to an existing table.
- **UPDATE** - Updates the existing records in a table.
- **CAST** - Converts an expression of one data type to another.
- **LEFT** - Extracts a number of characters from the left side of a string.
- **RIGHT** - Extracts a number of characters from the right side of a string.
- **GETDATE()** - Returns the current date and time.
- **YEAR()** - Extracts the year part from a date.
- **MONTH()** - Extracts the month part from a date.
- **BEGIN...END** - Used to group multiple SQL statements into a single block.
- **SELECT INTO** - Creates a new table and inserts the result of a query into it.
- **DISTINCT** - Removes duplicate records from the result set.
- **COUNT()** - Returns the number of rows that match a specified criterion.
- **INNER JOIN** - Combines rows from two or more tables based on a related column.
- **DECLARE** - Defines variables.
- **SET** - Assigns a value to a variable.
- **AVG()** - Returns the average value of a numeric column.
- **SUM()** - Returns the total sum of a numeric column.
- **GROUP BY** - Groups rows that have the same values in specified columns into aggregated data.
- **ROW_NUMBER()** - Assigns a unique sequential integer to rows within a result set.
- **ORDER BY** - Sorts the result set in either ascending or descending order.
- **WITH** - Defines a common table expression (CTE) that can be referenced within the query.

 

## Contact Information
- **LinkedIn:** [My LinkedIn Profile](https://www.linkedin.com/in/abdelrahmanfaheem/)  
- **GitHub:** [Link to Code](https://github.com/abdelrahmanfaheem/Growth-Sales-Analysis-Using-SQL/blob/main/MaxAB_Transfomation_And_analysis.sql)  

```sql


-- transform   Date Column to Sales & Product Tables
begin 
alter table sales 
add sales_date Date

UPDATE sales
SET sales_date = CAST(LEFT(date, 2) + ' ' + RIGHT(date, 3) + ' ' + CAST(YEAR(GETDATE()) AS VARCHAR(4)) AS DATE);
 

alter table Product 
add Proudct_date Date


UPDATE Product
SET Proudct_date = CAST(LEFT(date, 2) + ' ' + RIGHT(date, 3) + ' ' + CAST(YEAR(GETDATE()) AS VARCHAR(4)) AS DATE);
 
 end 
 ------------------------------------------------------------------
--- Q1
---------- Calculate the total monthly retention rate for Feb (customers retained from Jan) 
---------- and retention rate for every channel
---------- and state your recommendations on how we can enhance this KPI.

----------- 1- total monthly retention rate for Feb 
begin
			Select 
			distinct RETAILER_ID , Sales_Date 
			into #RETAILER_ID_IN_Jan
			From
			sales 
			where
			MONTH(Sales_Date)=1
 

			Select 
			distinct RETAILER_ID , Sales_Date 
			into #RETAILER_ID_IN_Feb
			From
			sales 
			where
			MONTH(Sales_Date)=2

			---- Retained Customers from jan and feb
			select distinct RETAILER_ID from #RETAILER_ID_IN_Jan --- 20,037
			SELECT COUNT(DISTINCT feb.RETAILER_ID) -- 15,499
			FROM #RETAILER_ID_IN_Jan jan
			INNER JOIN #RETAILER_ID_IN_Feb feb ON feb.RETAILER_ID = jan.RETAILER_ID


			---Retention Rate in feb over jan
			------(number of cutomer in feb who also by in jan / total number of ustomer in jan) *100

			Declare 
			@Cutomer_IN_Feb_and_Jan int , @Cutomer_IN_Jan int , @Retention_Rate float
			set @Cutomer_IN_Jan =
			(select count (distinct RETAILER_ID) from #RETAILER_ID_IN_Jan	)
			set @Cutomer_IN_Feb_and_Jan= 
			(SELECT COUNT(DISTINCT feb.RETAILER_ID)  
					FROM #RETAILER_ID_IN_Jan jan
					INNER JOIN 
					#RETAILER_ID_IN_Feb feb 
					ON feb.RETAILER_ID = jan.RETAILER_ID)

			set @Retention_Rate=   (@Cutomer_IN_Feb_and_Jan*1.0 /@Cutomer_IN_Jan )*100
			select @Retention_Rate -- 77.3518989868 %

			------------ 2- Retention rate for every channel ----------------

			with jan_customers_channel  as (
			select 
			  RETAILER_ID,CHANNEL 
			from
				Sales
			where
				MONTH (Sales_Date)=1
			group by 
				RETAILER_ID,CHANNEL
		 
			),
			Feb_customers_channel  as (
			select 
				 RETAILER_ID,CHANNEL
			from
				Sales 
			where
				MONTH (Sales_Date)=2
			group by RETAILER_ID,CHANNEL
			
			),Return_Customer_For_channel as
			(
			select 
				b.CHANNEL,count (Distinct b.RETAILER_ID)#Reta_Feb_retailer_Count
			from 
				Feb_customers_channel a inner join jan_customers_channel b
			on
				a.RETAILER_ID=b.RETAILER_ID and b.CHANNEL=a.CHANNEL
			group by
				b.CHANNEL

			),Channel_Count_retailr_for_Jan as
			(
			
			select
				CHANNEL,COUNT(distinct RETAILER_ID)#RETAILER_ID
			From 
				jan_customers_channel
			group by
				CHANNEL
		 )
		 select 
			a.CHANNEL ,a.#Reta_Feb_retailer_Count ,b.#RETAILER_ID , (a.#Reta_Feb_retailer_Count*1.0/b.#RETAILER_ID )*100  as Retain_rate
		 from
		  Return_Customer_For_channel a inner join Channel_Count_retailr_for_Jan b
		  on
		  a.CHANNEL=b.CHANNEL
		   
			
 
					

 
		 
  
end
	 
----------------------------------------------------------------------------------
--Q2

--Calculate   
------ 1-percentage growth of Feb over Jan in terms of number of orders, 
------ 2-distinct retailers, 
------ 3-average order price, 
------ 4-total revenues (sum of orders sales price) 


--  1-Orders Growth feb over jan
begin 

		Declare @Jan_Order int , @feb_Order int , @Order_growth decimal(6,2)
		set  @Jan_Order =  (select count (distinct  SALES_ORDER_ID)  From sales where Sales_Date between '2024-01-01'  and '2024-01-30')
		set  @feb_Order =  (select count (distinct  SALES_ORDER_ID)  From sales where Sales_Date between '2024-02-01'  and '2024-02-28')
		set @Order_growth=  (cast ((@feb_Order-@Jan_Order)as float )/@Jan_Order)*100  
		select  @Jan_Order as  Jan_Order ,@feb_Order as  feb_Order  , @Order_growth as Order_growth
  

		--- 2- Retailers Growth feb over jan

		Declare @Jan_retailers int , @feb_retailers int , @retailers_growth decimal(6,2)
 
		set  @Jan_retailers =  (select count (distinct  RETAILER_ID)  From sales where month ( Sales_Date)=1 )
		set  @feb_retailers =  (select count (distinct  RETAILER_ID)  From sales where month ( Sales_Date)=2 )
		set @retailers_growth=  (cast ((@feb_retailers-@Jan_retailers)as float )/@Jan_retailers)*100  
		select @Jan_retailers as  Jan_retailers , @feb_retailers as  feb_retailers ,@retailers_growth as Retailers_growth
  
		----3- Average Order Price Growth

		Declare @Jan_Avg_Order_Price float , @Feb_Avg_Order_Price float , @Growth_Avg_Order_Price float

		set @Jan_Avg_Order_Price =(select avg(UNIT_SALES_PRICE*SOLD_ITEM_COUNT) from Product where month (Proudct_date )=1)
		set @Feb_Avg_Order_Price =(select avg(UNIT_SALES_PRICE*SOLD_ITEM_COUNT) from Product where month (Proudct_date )=2)
		set @Growth_Avg_Order_Price = (@Feb_Avg_Order_Price-@Jan_Avg_Order_Price)*100/@Jan_Avg_Order_Price
  
		select @Jan_Avg_Order_Price as   Jan_Avg_Order_Price, @Feb_Avg_Order_Price as  Feb_Avg_Order_Price,@Growth_Avg_Order_Price as  Growth_Avg_Order_Price


		------ 4-total revenues (sum of orders sales price) 

		Declare @Jan_total_revenues float , @Feb_total_revenues float , @Growth_Total_revenues float

		set @Jan_total_revenues =(select avg(UNIT_SALES_PRICE) from Product where month (Proudct_date )=1)
		set @Feb_total_revenues =(select avg(UNIT_SALES_PRICE) from Product where month (Proudct_date )=2)
		set @Growth_Total_revenues = (@Feb_total_revenues-@Jan_total_revenues)*100/@Jan_total_revenues
  
		select @Jan_total_revenues as  Jan_total_revenues,@Feb_total_revenues as  Feb_total_revenues, @Growth_Total_revenues as   Growth_Total_revenues

End
------------------------------------------------------------------------------

-- 3-Calculate the total monthly burn (sum of discounts and wallet credit) for Jan and Feb 

Declare @Jan_discounts_and_wallet_credit decimal (15,2) , @Feb_discounts_and_wallet_credit decimal (15,2)  
set @Jan_discounts_and_wallet_credit =(select sum(DISCOUNT+WALLET_CREDIT) from Sales where month (Sales_Date )=1)
set @Feb_discounts_and_wallet_credit =(select sum(DISCOUNT+WALLET_CREDIT) from Sales where month (Sales_Date )=2)
select 
	@Jan_discounts_and_wallet_credit as    Jan_discounts_and_wallet_credit
	,@Feb_discounts_and_wallet_credit as   Feb_discounts_and_wallet_credit

-----------------------------------------------------------------------

--4.	Calculate the weekly percentage contribution of every
		--channel from the total weekly orders for Jan and Feb.

-- Calculate weekly percentage contribution for January
 
with Weekly_Orders_For_Channel AS (
    SELECT 
        DATEPART(WEEK, Sales_Date) AS Week_Number,
        CHANNEL,MONTH(Sales_Date) Month_Number,
        COUNT(DISTINCT SALES_ORDER_ID) AS Weekly_Orders_For_each_Channel
    FROM sales
    
    GROUP BY DATEPART(WEEK, Sales_Date),MONTH(Sales_Date ) ,CHANNEL
),
Weekly_Orders as (
select 
	DATEPART(week,Sales_Date)as Week_Number	,
	DATEPART (month,Sales_Date)as Month_Number,
	COUNT(distinct SALES_ORDER_ID)as Weekly_Orders
From 
	Sales
group by
DATEPART(week,Sales_Date),
DATEPART (month,Sales_Date)
 
)

 select a.Month_Number,a.Week_Number,a.CHANNEL ,cast ((a.Weekly_Orders_For_each_Channel*1.0/b.Weekly_Orders)*100 as decimal(6,4)) as  channel_weakly_percentage
 from Weekly_Orders_For_Channel a left join Weekly_Orders b
 on a.Month_Number=b.Month_Number and a.Week_Number=b.Week_Number
 order by a.Week_Number ,a.Month_Number

 ---------------------------------------------------------------------------
 --5.	Calculate the Total margin for each product and show the top 20 products that we should focus on
 ----	.and if there’s any products that should be considered for intervention.


--- 
 
with Total_Margin_For_Each_Product as (
 SELECT 
    PRODUCT_ID,
    Sum((UNIT_SALES_PRICE - UNIT_COST) * SOLD_ITEM_COUNT) AS Total_Margin
FROM 
	product
Group by 
	PRODUCT_ID

)
-- 1- Total Margin For Each Product
select  * From Total_Margin_For_Each_Product

-- 2- top 20 proudct
select top (20) * From Total_Margin_For_Each_Product order by Total_Margin desc


-- 3-Products for Intervention

select * From Total_Margin_For_Each_Product where Total_Margin<0
 


-----------------------------------------------------
----6.	Show the change in category contribution between Jan and Feb.
		--1 time in terms of Sales and another in terms of margins and 
		--2-show us your insights regarding this change and category contributions in general.

-- Total Sales for January and February



WITH Category_Sales_Jan as (
    SELECT 
        CATEGORY_ID,
        SUM(SOLD_ITEM_COUNT * UNIT_SALES_PRICE) AS Total_Sales_Jan
    FROM 
		product
    WHERE 
		MONTH(proudct_date) = 1
    GROUP BY 
		CATEGORY_ID
),
Category_Sales_Feb as (
    SELECT 
        CATEGORY_ID,
        SUM(SOLD_ITEM_COUNT * UNIT_SALES_PRICE) AS Total_Sales_Feb
    FROM
		product
    WHERE 
		MONTH(proudct_date) = 2
    GROUP BY	
		CATEGORY_ID
),
-- Total Margins for January and February
Category_Margins_Jan as (
    SELECT 
        CATEGORY_ID,
        SUM((UNIT_SALES_PRICE - UNIT_COST) * SOLD_ITEM_COUNT) AS Total_Margin_Jan
    FROM product
    WHERE MONTH(proudct_date) = 1
    GROUP BY CATEGORY_ID
),
Category_Margins_Feb as (
    SELECT 
        CATEGORY_ID,
        SUM((UNIT_SALES_PRICE - UNIT_COST) * SOLD_ITEM_COUNT) AS Total_Margin_Feb
    FROM product
    WHERE MONTH(proudct_date) = 2
    GROUP BY CATEGORY_ID
),Catgoery_IDS as(
Select 
	  CATEGORY_ID
from 
	Product
group by 
	CATEGORY_ID
),Sales_margin_for_each_category as 
(

Select
	a.CATEGORY_ID,
	csj.Total_Sales_Jan,
	csf.Total_Sales_Feb,
	cmj.Total_Margin_Jan,
	cmf.Total_Margin_Feb
from	
	Catgoery_IDS a left   JOIN Category_Sales_Jan csj
		on a.CATEGORY_ID=csj.CATEGORY_ID
	 left join Category_Sales_Feb csf
		on a.CATEGORY_ID=csf.CATEGORY_ID
	left join Category_Margins_Jan cmj
		on a.CATEGORY_ID=cmj.CATEGORY_ID
	left join Category_Margins_Feb cmf
		on a.CATEGORY_ID=cmf.CATEGORY_ID
	

),
 contribution_For_each_cateogry as 
(
select 
	CATEGORY_ID , 
	Total_Sales_Jan*1.0/(SELECT SUM(Total_Sales_Jan) from Sales_margin_for_each_category)* 100 as Jan_Sales
	,Total_Sales_Feb*1.0/(SELECT SUM(Total_Sales_Feb) from Sales_margin_for_each_category)* 100 as Feb_Sales
	,Total_Margin_Jan*1.0/(SELECT SUM(Total_Margin_Jan) from Sales_margin_for_each_category)* 100 as Margin_Jan
	,Total_Margin_Feb*1.0/(SELECT SUM(Total_Margin_Feb) from Sales_margin_for_each_category)* 100 as Margin_Feb
from	
Sales_margin_for_each_category	

)
--- 1- Sales And margin for each category 
select * From Sales_margin_for_each_category
--- 2-change in category contribution between Jan and Feb
select * From contribution_For_each_cateogry


-------------------------------------------------
--- 7- Rank Retailer According to the Total Margin 
WITH Retailer_Total_Margin AS (
    SELECT 
        RETAILER_ID,
        SUM((UNIT_SALES_PRICE - UNIT_COST) * SOLD_ITEM_COUNT) AS Total_Margin
		,ROW_NUMBER() OVER (ORDER BY SUM((UNIT_SALES_PRICE - UNIT_COST) * SOLD_ITEM_COUNT) DESC) AS Row_Num
    FROM Product
    GROUP BY RETAILER_ID
)
SELECT 
    RETAILER_ID, 
    Total_Margin,
      Row_Num
FROM    
    Retailer_Total_Margin
where Row_Num <10
	
order by
  Row_Num


 
