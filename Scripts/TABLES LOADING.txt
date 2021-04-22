
--Preparation of Dim_contracts table, containing one customer per line

SELECT
Customer_id, 
MAX(status) as Status,
SUM([annual revenue]) as Annual_Revenue,
'$' as Currency,
MAX(CASE WHEN [status] = 'renewed_or_still_active' THEN 100
         WHEN [status] = 'churned' THEN 0 END) as Final_Status_Scores
INTO [dim_contracts]
FROM [contracts]
WHERE [status] <> 'churned'
GROUP BY Customer_id 



--Preparation of Dim_product_usage table, containing one customer per line

SELECT  
Customer_id, 
COUNT(Customer_id) as Nb_months,
SUM(days_of_activity) as all_activity_in_days,
SUM(active_users) as Nb_active_users,
SUM(hours_spent) as Total_hours,
ROUND(SUM(days_of_activity) / COUNT(Customer_id),0) as Avg_score_per_month,
CASE WHEN (ROUND(SUM(days_of_activity) / COUNT(Customer_id),0)) = 0 THEN 0
	 WHEN (ROUND(SUM(days_of_activity) / COUNT(Customer_id),0)) > 0 AND (ROUND(SUM(days_of_activity) / COUNT(Customer_id),0))< 9 THEN 33
	 WHEN (ROUND(SUM(days_of_activity) / COUNT(Customer_id),0)) >= 9 AND (ROUND(SUM(days_of_activity) / COUNT(Customer_id),0)) < 15 THEN 66
	 WHEN (ROUND(SUM(days_of_activity) / COUNT(Customer_id),0)) >= 15 THEN 100
 END as Final_product_usage_score
INTO [Dim_product_usage]
FROM [product_usage]
GROUP BY Customer_id 


--Preparation of Dim_net_promoter_score table, containing one customer per line

SELECT customer_id,
ROUND(AVG(nps_score ),0) as Avg_score_nps,
CASE WHEN (ROUND(AVG(nps_score ),0)) > 0 AND (ROUND(AVG(nps_score ),0)) < 3 THEN 33
     WHEN (ROUND(AVG(nps_score ),0)) >= 3 AND (ROUND(AVG(nps_score ),0)) < 6 THEN 66
	 WHEN (ROUND(AVG(nps_score ),0)) >= 6 THEN 100
END as Final_nps_Scores
INTO [Dim_net_promoter_score]
FROM [net_promoter_score]
GROUP BY Customer_id 

--Preparation of Dim_support_tickets table, containing one customer per line


WITH support as ( 
SELECT *,
CASE WHEN [satisfaction_score] In ('bad','unoffered') THEN 0
	 WHEN [satisfaction_score] = 'offered'   THEN 50
     WHEN [satisfaction_score] = 'good'   THEN 100
END as Satisfaction_Scores_Numerical
FROM [dbo].[support_tickets]

)

SELECT customer_id,
COUNT(*) as Nb_tickets,
AVG (Satisfaction_Scores_Numerical) as Avg_satisfaction_Scores,
CASE WHEN (AVG (Satisfaction_Scores_Numerical)) = 0 THEN 0
	 WHEN (AVG (Satisfaction_Scores_Numerical)) > 0 AND (AVG (Satisfaction_Scores_Numerical)) <33 THEN 33
     WHEN (AVG (Satisfaction_Scores_Numerical)) >= 33 AND (AVG (Satisfaction_Scores_Numerical)) <66 THEN 66
	 WHEN (AVG (Satisfaction_Scores_Numerical)) >=66 THEN 100 
END as Final_support_tickets_Scores
INTO [Dim_support_tickets]
FROM support
GROUP BY Customer_id 

--Preparation of Dim_certifications table, containing one customer per line

WITH certification as ( 
SELECT cert.Customer_id, user_email, course_name, certif_level,
CASE WHEN certif_level = 'level 2' Then 100
WHEN certif_level = 'level 1' Then 50 
WHEN certif_level IS null OR certif_level = '' Then 0  end as Certification_score_Numerical
FROM [dbo].[certifications] cert
)

SELECT certification.Customer_id,
COUNT(*) as NB_certifications,
AVG(certification_score) as avg_certification_Scores,
CASE WHEN (AVG (Certification_score_Numerical)) > 0  AND (AVG (Certification_score_Numerical)) <33 THEN 33
     WHEN (AVG (Certification_score_Numerical)) >= 33 AND (AVG (Certification_score_Numerical)) <66 THEN 66
	 WHEN (AVG (Certification_score_Numerical)) >= 66 THEN 100 
END as Final_certification_Scores
INTO dbo.[dim_certifications]
FROM certification 
GROUP BY certification.Customer_id








