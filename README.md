# Ecommerce Sales Data Analysis

## Overview
I performed a thorough analysis of the Arpand ecommerce dataset that I obtained from Kaggle. This process involved cleaning and transforming the data using SQL, followed by an exploration to uncover valuable insights that could be beneficial to the ecommerce store owner.

## Location of Dataset
https://www.kaggle.com/datasets/aliazimieco/rfm-dataset?select=arpand_ecommerce_dataset.csv

## Exploring the Dataset and Understanding it 
- Order Number: The order number for each order number	
- Date	
- Sales Rep	
- Region	
- Customer ID	
- Company Name	
- Company Rep	
- Model	
- Color	
- Item Code	
- Quantity	
- Price Per Unit	
- Total Sales



## Analyzing the Dataset (Queries)
```sql
select * from arpand_ecommerce_dataset
```
1. **Japan is the best performing country** and **france is the worst performing**. we can also see that **germeny has the most amount of transactiosn while SA has the least**.
```sql
select Country, Sum(TotalSpent) as Total_Sales, count(TotalTransactions) as Total_Transactions
from arpand_ecommerce_dataset
group by Country
order by Total_Sales desc
```
2. The average number of transactions per customer is 
```sql
select (sum(TotalTransactions)/count(distinct CustomerID)) as Avg_Transaction_Per_Customer
from arpand_ecommerce_dataset
```
3. The gender that **spent the most amount of money is male**
```sql
select Gender, sum(TotalSpent) as Total_Spent
from arpand_ecommerce_dataset
group by Gender
order by Total_Spent desc
```
4. We can see that **30% of men** are using credit cards for their payment type and **28% of women** are using credit cards for thier payment types
```sql
SELECT 
    Gender, 
    SUM(TotalSpent) AS Total_Spent,
    COUNT(CASE WHEN PaymentType = 'Credit' THEN 1 END) AS Credit_Card_Payments,
    COUNT(CASE WHEN PaymentType = 'Instant' THEN 1 END) AS Instant_Card_Payments,
    (COUNT(CASE WHEN PaymentType = 'Credit' THEN 1 END) * 1.0) / 
    (COUNT(CASE WHEN PaymentType = 'Credit' THEN 1 END) + COUNT(CASE WHEN PaymentType = 'Instant' THEN 1 END)) AS credit_payment_percentage
FROM arpand_ecommerce_dataset
GROUP BY Gender
ORDER BY Total_Spent DESC;
```
5. **Arpand tokyo prefromed the best** meanwhile arpand paris performed the worst 
```sql
select BranchID, BranchName, sum(TotalSpent) as total_sales
from arpand_ecommerce_dataset
group by BranchID, BranchName
order by total_sales desc
```
6. (I want to look at the marketing strategies of the top 3 performing branches) We can see that the **top three performing branches** used **influencer, TV and email marketing**
```sql
select top 3 a.BranchID, a.BranchName, h.AdStrategy, sum(a.TotalSpent) as Total_Spent
from arpand_ecommerce_dataset a
join ad_history h
on a.BranchID=h.BranchID
group by a.BranchID, a.BranchName, h.AdStrategy
order by Total_Spent desc
```
7. (I want to take a look at the best performing event types) We see the best performing days were **valentines day, black friday and christmas in order**
```sql
select EventType, sum(SaleAmountEUR) as total_sales
from transactions
group by EventType
order by total_sales desc

8. (On valentines day branches performed really well lets take a look at the avg traffic multiplier and the types of abtestgroups) We can see the **average traffic multiplier** for the top 3 performing branches during valentines day 
```sql
select top 3 BranchID, EventType, avg(TrafficMultiplier) as avg_TrafficMulti, sum(SaleAmountEUR) as sale_amount
from transactions
where EventType = 'Valentine'
group by BranchID, EventType
order by sale_amount
```

9. (Lets take a look at the types of ABtestgroups that were using during valentines day) We can see **control test** was the most used ab test groups.
```sql
select EventType, 
	count(case when ABTestGroup = 'Control' then 1 end) as num_control_test, 
	count(case when ABTestGroup = 'Variation1' then 1 end) as num_variation1_test,
	count(case when ABTestGroup = 'Variation2' then 1 end) as num_variation2_test,
	sum(SaleAmountEUR) total_sales
from transactions
where EventType = 'Valentine'
group by EventType, ABTestGroup
```
10. (Lets take a look at the age groups of customers for arpand ecommerce store) Had to remove the person who was **150 years old** since its an error, we can also see that the age **group 60+ were the top spenders** followed by the 40-50 group
```sql
select min(Age), max(Age)
from arpand_ecommerce_dataset

select distinct Age
from arpand_ecommerce_dataset
order by Age desc

delete from arpand_ecommerce_dataset
where Age = '150'

select 
	case 
	when Age between 18 and 24 then '18-24'
	when Age between 24 and 30 then '24-30'
	when Age between 30 and 35 then '30-35'
	when Age between 35 and 40 then '35-40'
	when Age between 40 and 50 then '40-50'
	when Age between 50 and 60 then '50-60'
	when Age between 60 and 70 then '60-70'
	when Age between 70 and 80 then '70-80'
	else 'unknown' end as Age_Group
from arpand_ecommerce_dataset

SELECT Age_Group, COUNT(Age_Group) AS Count, SUM(TotalSpent) AS Total_spent
FROM (
    SELECT 
        TotalSpent,  -- Included the column name inside the subquery
        CASE 
            WHEN Age BETWEEN 18 AND 24 THEN '18-24'
            WHEN Age BETWEEN 25 AND 30 THEN '25-30'
            WHEN Age BETWEEN 40 AND 50 THEN '40-50'
            WHEN Age BETWEEN 50 AND 60 THEN '50-60'
            WHEN Age BETWEEN 60 AND 100 THEN '60-100'
            ELSE 'Unknown' 
        END AS Age_Group
    FROM arpand_ecommerce_dataset
) AS Age_Categories
GROUP BY Age_Group
ORDER BY Total_spent DESC;
```
11. (Lets take a look at the total sales by a month by month bases based on the last purchase date data) We can see from the data that our** top 3 performing months inlcude August, November and May**
```sql
select top 3 format(LastPurchaseDate, 'MMMM') as The_Month, sum (TotalSpent) as Total_spent
from arpand_ecommerce_dataset
group by format(LastPurchaseDate, 'MMMM')
order by Total_spent desc

select * from arpand_ecommerce_dataset
select * from branches
select * from transactions
```
## Insights
_The analysis of the Arpand ecommerce dataset revealed several interesting insights._
- Japan emerged as the best-performing country in terms of total sales, while France lagged behind.
- Gender-wise, males spent the most money, and a significant portion of both men and women used credit cards for transactions.
- Arpand Tokyo was the top-performing branch, with influencer, TV, and email marketing strategies being the most successful across the top branches.
- Valentine's Day stood out as a key event, with the highest traffic multiplier seen in top-performing branches.
- The 60+ age group spent the most, followed closely by those in the 40-50 age group.
- August, November, and May were identified as the top sales months.

