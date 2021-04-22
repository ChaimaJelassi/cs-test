<p align="center">
</p>

# Table of contents
  * [What?](#What)
  * [Why?](#Why)
  * [How?](#How)
    * [Selected Features](#selected-features)
      * [Product Usage](#product-usage)
      * [Net Promoter Score](#net-promoter-score)
      * [Satisfaction](#satisfaction)
      * [Certification](#certification)
    * [Calculation Method](#calculation-method)
  * [Reporting](#Reporting)
     
     
## What is a Customer Health Score?
It is an index made up of several “vital” scores/KPIs, giving an overall idea of how healthy a customer’s relationship is with your product. It also enables you to predict how your relationship with a given customer may change in the future like <strong>renewing</strong> or <strong>churning</strong> subscriptions.</br> 
In a nutshell, we can think of it as an early warning system to reduce the risk of churns. It notifies decision makers to start to take actions at the right time.

## Why?
Some common objectives of implementing a Customer Health Score are to:
* Provide a data-driven definition of how healthy a customer is, rather than relying on gut feel and personal opinions.
* Enable proactive intervention long before a customer is at risk of churning.
* Identify advocates who can influence prospects to become customers.
* Detect highly engaged and loyal clients, who would make excellent advocates.

## How?
In this section, I will describe the **features** I have considered as key measures and the **method** used to calculate the Customer Health Score.

Before jumping to the implementation, the following are some notes to help you understand my methodology of work:

```
1. I worked only with customers who have a contract status = "renewed_or_still_active".
2. The product usage Tab collects the activity of customers per month. ( several lines per customer ) 
3. The cetifications Tab collects data about the certifications taken by customers. ( several lines per customer )
4. The net promoter score Tab collects data about the ratings of the product by customers. ( several lines per customer )
5. The support tickets Tab collects data about the tickets opened by customers. ( several lines per customer )
6. For 2,3,4,and 5, I processed data and got a line per customer as an output.
7. Categorical Features selected were converted to numerical. 
```


### Selected Features
The following are the four features selected among the 6 tabs in the given excel file and considered as the most relevant ones for the calculation of the CHS. <br/>
By the end, each Feature will be assigned a percentage based on its importance as follows:

* **product usage:  30% from the global CHS Score**
* **net promoter: score 30% from the global CHS Score**
* **satisfaction: 30% from the global CHS Score**
* **certification: 10% from the global CHS Score**

#### Product Usage 
Is how often the users of ContentSquare are connecting to the application and how much time they are spending on it.</br>
Among the **Product Usage** tab of the excel file, I selected the **days_of_activity** as the main column which refers to the activity of the users per month.
```
Let's consider Month is: 2019/06  
The customer X was active 21 days over 30, he will be then considered as an active user.
```
![alt text](https://github.com/ChaimaJelassi/cs-test/blob/main/images/productusage.PNG) </br>
After the processing of the feature as mentioned in this SQL query and depending on the score given, the **Final_product_usage_score** will be devided into 3 intervals over 100 as follows :

* Low = **Avg_score_per_month** **between 0** and **8**  then **33** </br>
* Good = **Avg_score_per_month** **between 9** and **14**  then **66** </br>
* Excellent = **Avg_score_per_month** **>= 15**  then **100** </br>

NOTE: when the **Avg_score_per_month** is equal to 0, I gave 0 as a score.
```
Where 
Avg_score_per_month = sum(days_of_activity) as AllActivity /NbOfMonths
```

#### Net Promoter Score
Is a customer loyalty and satisfaction measurement taken from asking customers how likely they are to recommend your product or service to others.</br>
Among the **Net Promoter Score** tab of the excel file, I selected the **nps_score** as the main column which refers to the score given by the customer for a given period of time.
```
Let's consider that the customer X, on the 2018/06 gave a score of 8 
				    on the 2019/01 gave a score of 7 
				    on the 2020/03 gave a score of 9 
The customer X has an overall score of 8, meaning he is satisfied with the product and will highly recommend it to others.
```
![alt text](https://github.com/ChaimaJelassi/cs-test/blob/main/images/nps.PNG)

After the processing of the feature as mentioned in this SQL query and depending on the score given to the Net Promoter question, three categories of customers can be distinguished:

* Low = **Avg_score_nps** **>0** and **<3**  then **33**
* Good =  **Avg_score_nps** **>=3** and **<6**  then **66**
* Excellent =  **Avg_score_nps** **>=6**  then **100**

NOTE: when the **Avg_score_nps** is equal to 0, I gave 0 as a score.

```
Where 
Avg_score_nps = AVG(nps_score) per customer
```

#### Satisfaction
Is how much users are satisfied with the support team.
Among the **Support Tickets** tab of the excel file, I selected the **satisfaction_score** as the main column which reflects the satisfaction of the customer for each JIRA ticket created.

![alt text](https://github.com/ChaimaJelassi/cs-test/blob/main/images/satisfaction.PNG)

```
Let's consider that the customer X : created a JIRA ticket on the 2020/02/19, the satisfaction score was "Good".
				     created a JIRA ticket on the 2020/02/19, the satisfaction score was "Unoffered".
				     created a JIRA ticket on the 2020/04/12, the satistfaction score was "Unoffered".
The customer X has a overall satisfaction score of 33, which means, he is recognized as an unsatisfied user.
```
 After the processing of the feature as mentioned in this SQL query and depending on the score given to the Satisfaction, three categories of customers can be distinguished:


* Low = **Avg_support_tickets_scores** **>0** and **<33** then **33**
* Good =  **Avg_support_tickets_scores** **>=33** and **<66**  then **66**
* Excellent =  **Avg_support_tickets_scores** **=>66**  then **100**

NOTE: 
* Categorical **Satisfaction_score** were converted to numerical.
* when the **Avg_support_tickets_scores** is equal to 0, I gave 0 as a score.

```
Where 
Avg_support_tickets_scores= AVG(Satisfaction_Scores_Numerical) per customer
```

#### Certification 
Is how much users are certified and recognized as potential power users.</br>
Among the **Certifications** tab of the excel file, I selected the **certif_level** as the main column which refers to the level of the certification achieved by the customer given a period of time and for a specific course.
![alt text](https://github.com/ChaimaJelassi/cs-test/blob/main/images/certification.PNG)

```
Let's consider that the customer X, on the 2018/06 took level1 certification in the "Contentsquare Fundamentals" course.
				    on the 2019/01 took level2 certification in the "Contentsquare Expert General" course.
				    on the 2020/03 took level1 certification in the "Certification Expert pour E-Merchandiser" course.
The customer X has a overall certification level score of 66, meaning he is recognized as a static user.
```

After the processing of the feature as mentioned in this SQL query and depending on the score given to the Certifications, three categories of customers can be distinguished:

* Low = **Avg_certification_Scores** **>0** and **<33**then **33**
* Good =  **Avg_certification_Scores** **>=33** and **<66**  then **66**
* Excellent =  **Avg_certification_Scores** **>=66**  then **100**
* 
NOTE: 
* Categorical **Certif_level** were converted to numerical.
* when the **Avg_certification_Scores** is equal to 0, I gave 0 as a score.

```
Where 
Avg_certification_Scores = AVG(Satisfaction_Scores_Numerical) per customer
```
### Calculation Method
Once the final scores of the columns selected are processed, we move now to the final calculation of the *Customer Health Score* using the following formula : 

```
SUM (Final_product_usage_score * 30% ,Final_nps_scores * 30% ,Final_support_tickets_Scores * 30%,Final_certification_Scores * 10%) 
```

Now that the formula is applied, it’s time to look at which customers are healthy or those that are at risk.
## Reporting

In this section, I will show you the final output table containing the CHS score and its SQL source code.</br>
To classify customers based on the calculated score, I used the following indicators: 
```
Healthy : GREEN COLOR  
Okay : ORANGE COLOR
Unhealthy : RED COLOR
```
**Unhealthy indicates at risk, Orange is ok and Green indicates a healthy customer.**

I chose powerbi as a visualization tool, I connected the final table to it and the following figures show my results :
