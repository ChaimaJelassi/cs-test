*****************************************************************************************************USEFUL STATISTICS****************************************************************************************************************
**************************************************************************************************************************************************************************************************************************************

                                                      ################################################CUSTOMERS TABLE######################################################

--Number of distinct customers :580

SELECT DISTINCT
COUNT(*)  as Nb_customers
FROM customers 

--Number of customers per region

SELECT 
region as Region, 
COUNT(customer_id) as Nb_customers 
FROM [customers] 
GROUP BY region 

--Number of distinct customers doesn't have a contract



                                                     ################################################CONTRACTS TABLE#######################################################
--Number of distinct contracts : 761
SELECT DISTINCT 
COUNT(*) 
FROM [contracts]


--Number of contracts by customer : we have 553 customers having one or more contracts regardless the status

SELECT customer_id as Customer,
COUNT(Contract_id) as Nb_contracts  
FROM [contracts]
GROUP BY customer_id
ORDER BY 2 desc 

--Number of contracts by customer : we have 535 distinct customers having a renewed or an active contract 
SELECT customer_id as Customer,
COUNT(Contract_id) as Nb_contracts  
FROM [contracts]
where Status <> 'churned'
GROUP BY customer_id
ORDER BY 2 desc 

-- Number of customers with no contratcs :45
-- dim_ contracts is a table containing distinct customer_ids with status <> 'churned'
SELECT 
COUNT(*) as Nb_customers_No_contracts
FROM [customers] 
LEFT OUTER JOIN [dim_contracts] on [dim_contracts].Customer_id = customers.Customer_id
WHERE [dim_contracts].Customer_id IS NULL

==> Number of distinct customers 580 - Nb of customers having a contract <> churned 535 =  45 customers doesn't have contracts


                                                   ################################################CERTIFICATIONS TABLE###################################################

--Number of certifications per Customer

SELECT customer_id as Customer,
COUNT(*) as Nb_certifications  
FROM [contracts]
GROUP BY customer_id
ORDER BY 2 desc 


--The highest score of certification by customer and conversion of categorical values to numerical

WITH HighestCertif as ( 
select cert.Customer_id,
user_email,
course_name, 
certif_level,
CASE WHEN certif_level = 'level 2' Then 100
     WHEN certif_level = 'level 1' Then 50 
     WHEN (certif_level IS NULL OR certif_level = '') Then 0  END As certif_score,
(ROW_NUMBER()  OVER(PARTITION BY cert.customer_id
ORDER BY certif_level DESC)) AS HighestLevel
from [dbo].[certifications] cert
)

SELECT * 
FROM HighestCertif 
WHERE HighestLevel = 1  


                                                  ################################################NET PROMOTER SCORE TABLE#################################################

--Number of certifications per Customer

SELECT customer_id as Customer,
COUNT(nps_score) as Nb_nps_score  
FROM [net_promoter_score]
GROUP BY customer_id
ORDER BY 2 desc 

                                                ################################################SUPPORT TICKETS TABLE####################################################

--Conversion of categorical values to numerical
SELECT *,
CASE WHEN[satisfaction_score] In ('bad','unoffered') Then 0
	 WHEN [satisfaction_score] = 'offered'   Then 50
     WHEN [satisfaction_score] = 'good'   Then 100
	 END AS Satisfaction_Scores_numerical
from [support_tickets]

--Number of Support tickets per Customer

SELECT customer_id as Customer,
COUNT(nps_score) as Nb_support_tickets  
FROM [support_tickets]
GROUP BY customer_id
ORDER BY 2 desc 

                                              ################################################PRODUCT USAGE TABLE#####################################################

-- All activity per customer.
-- Number of months where the customer was active.
-- Average score activity of customers per month.

SELECT Customer_id, 
COUNT(months) as Nb_months,
SUM(days_of_activity) as All_activity,
ROUND(SUM(days_of_activity) / COUNT(Customer_id),0) as Avg_score_activity_per_month
FROM [product_usage]
GROUP BY Customer_id 