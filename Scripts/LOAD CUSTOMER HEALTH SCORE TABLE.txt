with CHS as (SELECT cust.customer_id, 
cust.Industry,
cust.Region,
       cnt.STATUS AS STATUS, 
       cnt.Status_codes, 
	   cnt.Annual_Revenue,
	   cnt.Currency,
       ISNULL(certifications.[NB_certifications], '0') AS [NB_certifications], 
       ISNULL(certifications.[avg_certification_Scores], '0') AS [avg_certification_Scores],
       CASE
           WHEN certifications.[Final_certification_Scores] = 0
                OR certifications.[Final_certification_Scores] IS NULL
           THEN 0
           ELSE certifications.[Final_certification_Scores]
       END AS [Final_certification_Scores], 
       ISNULL(nps.avg_score_nps, '0') as avg_score_nps ,
       CASE
           WHEN nps.final_nps_scores = 0
                OR nps.final_nps_scores IS NULL
           THEN 0
           ELSE nps.final_nps_scores
       END AS Final_nps_scores, 
       ISNULL(tickets.[nb_tickets],'0') as Nb_tickets, 
       ISNULL(tickets.[Avg_satisfaction_Scores],'0') as Avg_satisfaction_Scores , 
	   CASE
           WHEN tickets.[nb_tickets] = 0 or tickets.[nb_tickets] IS NULL THEN 100
		   ELSE tickets.[Final_support_tickets_Scores] END as [Final_support_tickets_Scores] ,
       ISNULL(product.[Nb_months],'0') as Nb_months , 
       ISNULL(product.[all_activity_in_days],'0') as all_activity_in_days  , 
         ISNULL(product.[Nb_active_users],'0') as Nb_active_users , 
         ISNULL(product.[Total_hours],'0') as Total_hours , 
         ISNULL(product.[Avg_score_per_month],'0') as Avg_score_per_month , 
        CASE
           WHEN  product.[Final_product_usage_score] = 0 or product.[Final_product_usage_score] IS NULL THEN 0
		   ELSE product.[Final_product_usage_score] END AS [Final_product_usage_score]
FROM [Temp_DB_20200905].dbo.customers cust
     LEFT OUTER JOIN [Temp_DB_20200905].[dbo].[dim_contracts] cnt ON cnt.customer_id = cust.Customer_id
     LEFT OUTER JOIN [Temp_DB_20200905].[dbo].[dim_certifications] certifications ON certifications.Customer_id = cust.Customer_id
     LEFT OUTER JOIN [Temp_DB_20200905].[dbo].[Dim_net_promoter_score] nps ON nps.customer_id = cust.Customer_id
     LEFT OUTER JOIN [Temp_DB_20200905].[dbo].[dim_support_tickets] tickets ON tickets.customer_id = cust.Customer_id
     LEFT OUTER JOIN [Temp_DB_20200905].[dbo].[dim_product_usage] product ON product.customer_id = cust.Customer_id
WHERE cnt.STATUS IS NOT NULL)



select 
customer_id as [Customer Identifier],
Industry,
Region,
Annual_Revenue,
Currency,
[NB_certifications] AS [Number of certifications],
[Final_certification_Scores],
[Final_nps_scores],
[nb_tickets] as [Number of Tickets],
[Final_support_tickets_Scores] ,
[Nb_months] as [Number of activity months],
all_activity_in_days as [Number days of activity],
Nb_active_users as [Number of Active Users],
Total_hours as [Number hours of activity],
[Final_product_usage_score],
cast(round((Final_certification_Scores*0.1 + Final_nps_scores*0.3+ Final_product_usage_score*0.3+Final_support_tickets_Scores*0.3),0) as float) as Customer_health_score
INTO [Temp_DB_20200905].[dbo].customer_health_score
FROM CHS
order by 1 desc

