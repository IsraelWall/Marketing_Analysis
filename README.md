# Marketing_Analysis
Here I present an analysis using a dataset from Kaggle
----------------------------------------------------------------------------------------------------------------------------
--Factors that have most significant impact on the number of online purchases--
------------------------------------------------------------------
--Incom Impact on online purchases-- 
------------------------------------------------------------------
WITH IncomeBuckets AS 
  (
 SELECT [ Income ], NumWebPurchases,
        NTILE(3) OVER (ORDER BY CAST(REPLACE(REPLACE(REPLACE([ Income ], '$', ''), ',', ''), 'NULL', '0') AS FLOAT)) AS Income_Bucket
    FROM marketing_data
    WHERE [ Income ] IS NOT NULL
  )
SELECT CASE WHEN Income_Bucket = 1 THEN 'Low'
            WHEN Income_Bucket = 2 THEN 'Medium'
            ELSE 'High' END AS Income_Level,
       AVG(NumWebPurchases) AS Avg_Online_Purchases
FROM IncomeBuckets
GROUP BY Income_Bucket
ORDER BY Avg_Online_Purchases DESC

------------------------------------------------------------------
--kids at home impact on online purchases --
------------------------------------------------------------------
SELECT 
    Kidhome,
    AVG(NumWebPurchases) AS Avg_Online_Purchases
FROM marketing_data
GROUP BY Kidhome
ORDER BY Avg_Online_Purchases DESC

------------------------------------------------------------------
--teens at home impact on online purchases -- 
------------------------------------------------------------------
SELECT 
    Teenhome,
    AVG(NumWebPurchases) AS Avg_Online_Purchases
FROM marketing_data
GROUP BY Teenhome
ORDER BY Avg_Online_Purchases DESC

------------------------------------------------------------------
--Discounts Offered impact on online purchases --
------------------------------------------------------------------
SELECT 
    NumDealsPurchases,
    AVG(NumWebPurchases) AS Avg_Online_Purchases
FROM marketing_data
GROUP BY NumDealsPurchases
ORDER BY NumDealsPurchases DESC

------------------------------------------------------------------
-- education impact on online purchases --
------------------------------------------------------------------
SELECT 
    Education,
    AVG(NumWebPurchases) AS Avg_Online_Purchases
FROM marketing_data
GROUP BY Education
ORDER BY Avg_Online_Purchases DESC

------------------------------------------------------------------
-- website visits impact on online purchases 
------------------------------------------------------------------
SELECT NumWebVisitsMonth,
AVG(NumWebPurchases) AS Avg_Online_Purchases
FROM marketing_data
GROUP BY NumWebVisitsMonth
ORDER BY NumWebVisitsMonth DESC, Avg_Online_Purchases DESC

----------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------
--Which campaign were most succesfull?--

------------------------------------------------------------------
--Campaign success comparison -- campaign 1/4/5 are most successfull
------------------------------------------------------------------
SELECT 
    'Campaign 1' AS Campaign,
    AVG(NumWebPurchases) AS Avg_Online_Purchases
FROM marketing_data
WHERE AcceptedCmp1 = 1

UNION ALL

SELECT 
    'Campaign 2' AS Campaign,
    AVG(NumWebPurchases) AS Avg_Online_Purchases
FROM marketing_data
WHERE AcceptedCmp2 = 1

UNION ALL

SELECT 
    'Campaign 3' AS Campaign,
    AVG(NumWebPurchases) AS Avg_Online_Purchases
FROM marketing_data
WHERE AcceptedCmp3 = 1

UNION ALL

SELECT 
    'Campaign 4' AS Campaign,
    AVG(NumWebPurchases) AS Avg_Online_Purchases
FROM marketing_data
WHERE AcceptedCmp4 = 1

UNION ALL

SELECT 
    'Campaign 5' AS Campaign,
    AVG(NumWebPurchases) AS Avg_Online_Purchases
FROM marketing_data
WHERE AcceptedCmp5 = 1

----------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------
--The typical customer is--
--Who is the tipical consumer

------------------------------------------------------------------
--avg age
------------------------------------------------------------------
SELECT AVG(YEAR(GETDATE()) - Year_Birth) AS Avg_Age
FROM marketing_data;
--education level 
SELECT TOP 1 Education, COUNT(*) AS Count
FROM marketing_data
GROUP BY Education
ORDER BY Count DESC
--most common marital status
SELECT TOP 1 Marital_Status, COUNT(*) AS Count
FROM marketing_data
GROUP BY Marital_Status
ORDER BY Count DESC
--average income.
SELECT ROUND(AVG(CAST(REPLACE(REPLACE(REPLACE([ Income ], '$', ''), ',', ''), 'NULL', '0') AS FLOAT)),0) AS avg_income
FROM marketing_data
--avg kids/teens at home
SELECT AVG(Kidhome) AS Avg_Kidhome, AVG(Teenhome) AS Avg_Teenhome
FROM marketing_data;
--average number of purchases through different channels
WITH sum_of_purchase_methods AS
	(
SELECT 
    SUM(NumDealsPurchases) AS Avg_Deals_Purchases,
    SUM(NumWebPurchases) AS Avg_Web_Purchases,
    SUM(NumCatalogPurchases) AS Avg_Catalog_Purchases,
    SUM(NumStorePurchases) AS Avg_Store_Purchases,
	SUM(NumDealsPurchases + NumWebPurchases + NumCatalogPurchases + NumStorePurchases) AS Total_Purchases
FROM marketing_data
	)
SELECT    
	FORMAT(Avg_Deals_Purchases *100 / Total_Purchases, 'N0') +'%'  AS Deals_Purchases_Percentage,
    FORMAT(Avg_Web_Purchases *100 / Total_Purchases, 'N0') +'%' AS Web_Purchases_Percentage,
    FORMAT(Avg_Catalog_Purchases *100 / Total_Purchases, 'N0') +'%' AS Catalog_Purchases_Percentage,
    FORMAT(Avg_Store_Purchases *100 / Total_Purchases, 'N0') +'%' AS Store_Purchases_Percentage
FROM sum_of_purchase_methods
;
-- response rate to each marketing campaign.
WITH sum_of_accepted_cte AS
	(
SELECT 
    SUM(AcceptedCmp1) AS sum_AcceptedCmp1,
    SUM(AcceptedCmp2) AS sum_AcceptedCmp2,
    SUM(AcceptedCmp3) AS sum_AcceptedCmp3,
    SUM(AcceptedCmp4) AS sum_AcceptedCmp4,
    SUM(AcceptedCmp5) AS sum_AcceptedCmp5, 
	SUM(AcceptedCmp1+AcceptedCmp2+AcceptedCmp3+AcceptedCmp4+AcceptedCmp5) AS total_acceptence
FROM marketing_data
	)
SELECT 
    FORMAT(sum_AcceptedCmp1 *100 / total_acceptence, 'N0') +'%' AS precent_accept1,
    FORMAT(sum_AcceptedCmp2 *100 / total_acceptence, 'N0') +'%' AS precent_accept2,
    FORMAT(sum_AcceptedCmp3 *100 / total_acceptence, 'N0') +'%' AS precent_accept3,
    FORMAT(sum_AcceptedCmp4 *100 / total_acceptence, 'N0') +'%' AS precent_accept4, 
    FORMAT(sum_AcceptedCmp5 *100 / total_acceptence, 'N0') +'%' AS precent_accept5 
FROM sum_of_accepted_cte;

------------------------------------------------------------------
--Which products are the most effective 
------------------------------------------------------------------
WITH AvgProductPurchases AS 
	(
    SELECT 
        AVG(MntWines) AS Avg_Wines_Purchases,
        AVG(MntFruits) AS Avg_Fruits_Purchases,
        AVG(MntMeatProducts) AS Avg_Meat_Purchases,
        AVG(MntFishProducts) AS Avg_Fish_Purchases,
        AVG(MntSweetProducts) AS Avg_Sweet_Purchases,
        AVG(MntGoldProds) AS Avg_Gold_Purchases,
		AVG(MntWines+MntFruits+MntMeatProducts+MntFishProducts+MntSweetProducts+MntGoldProds) AS total_purchase_avg
    FROM marketing_data
	)	
SELECT 
    'Wines' AS Product, 
    FORMAT(CAST(Avg_Wines_Purchases * 100 / total_purchase_avg AS NUMERIC), 'N0') +'%' AS Avg_Purchases_Percentage
FROM AvgProductPurchases

UNION ALL

SELECT 
    'Fruits' AS Product, 
    FORMAT(CAST(Avg_Fruits_Purchases * 100 / total_purchase_avg AS NUMERIC), 'N0') +'%' AS Avg_Purchases_Percentage
FROM AvgProductPurchases

UNION ALL

SELECT 
    'Meat Products' AS Product, 
	FORMAT(CAST(Avg_Meat_Purchases * 100 / total_purchase_avg AS NUMERIC), 'N0') +'%' AS Avg_Purchases_Percentage

FROM AvgProductPurchases

UNION ALL

SELECT 
    'Fish Products' AS Product, 
	FORMAT(CAST(Avg_Fish_Purchases * 100 / total_purchase_avg AS NUMERIC), 'N0') +'%' AS Avg_Purchases_Percentage

FROM AvgProductPurchases

UNION ALL

SELECT 
    'Sweet Products' AS Product, 
	FORMAT(CAST(Avg_Sweet_Purchases * 100 / total_purchase_avg AS NUMERIC), 'N0') +'%' AS Avg_Purchases_Percentage

FROM AvgProductPurchases

UNION ALL

SELECT 
    'Gold Products' AS Product, 
	FORMAT(CAST(Avg_Gold_Purchases * 100 / total_purchase_avg AS NUMERIC), 'N0') +'%' AS Avg_Purchases_Percentage
FROM AvgProductPurchases

------------------------------------------------------------------
--Underperforming channels
------------------------------------------------------------------
;WITH channels_cte AS
	(
SELECT 
    AVG(NumCatalogPurchases) AS avg_CatalogPurchases,
    AVG(NumStorePurchases) AS avg_StorePurchases,
    AVG(NumWebPurchases) AS avg_WebPurchases,
	AVG(NumDealsPurchases) AS avg_DealsPurchases,
  	AVG(NumCatalogPurchases)+AVG(NumStorePurchases)+AVG(NumWebPurchases) + AVG(NumDealsPurchases) AS total_avg_purchases
FROM marketing_data
	)
SELECT 
   'deals' AS channel,
   FORMAT(avg_DealsPurchases *100 / total_avg_purchases, 'N0') + '%' AS precent_dealpurchases
FROM channels_cte 

UNION ALL

SELECT 
   'store' AS channel,
   FORMAT(avg_storePurchases *100 / total_avg_purchases, 'N0') + '%' AS precent_storePurchases
FROM channels_cte 

UNION ALL

SELECT 
   'catalog' AS channel,
   FORMAT(avg_catalogPurchases *100 / total_avg_purchases, 'N0') + '%' AS precent_catalogPurchases
FROM channels_cte 

UNION ALL

SELECT 
   'web' AS channel,
   FORMAT(avg_webPurchases *100 / total_avg_purchases, 'N0') + '%' AS precent_webPurchases
FROM channels_cte 

----------------------------------------------------------------------------------------
