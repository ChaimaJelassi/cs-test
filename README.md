<p align="center">
This repository contains the <strong>Customer Health Score</strong> calculation.
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
     
     
## What?
It is an index made up of several “vital” scores/KPIs. It gives you an overall idea of how healthy a customer’s relationship is with your product. It also 
enables you to predict how your relationship with a given customer may change in the future like <strong>renewing</strong> or <strong>churning</strong> subscriptions.</br>

## Why?
Some common objectives of implementing a Customer Health Score are to:
* Provide a data-driven definition of how healthy a customer is, rather than relying on gut feel and personal opinions.
* Enable proactive intervention long before a customer is at risk of churning.
* Identify advocates who can influence prospects to become customers.
* Detect highly engaged and loyal clients, who would make excellent advocates.

## How?
In this section, I will describe the **features** I have considered as key measures and the **method** used to calculate the Customer Health Score.

### Selected Features
The following are the four features selected among the 6 tabs in the given excel file and considered as the most relevant ones for the calculation of the CHS. <br/>
By the end, each Feature will be assigned a percentage based on its importance as follows:

* **product usage 30%**
* **net promoter score 30%**
* **satisfaction 30%**
* **certification 10%**

#### Product Usage 
Is how often the users of ContentSquare are connecting to the applcation and how much time they are spending on it.</br>
Among the **Product Usage** tab of the excel file, I selected the **days_of_activity** as the main column which refers to the activity of the users per month.
```
Let's consider Month is: 2019/06  
The customer X was active 21 days over 30 and thus considered as an active user.
```
![alt text](https://github.com/ChaimaJelassi/cs-test/blob/main/images/productusage.PNG) </br>
After the processing of the feature as mentioned in this SQL query and depending on the score given,the **Product Usage final score** is devided into 3 intervals as follows :

* Inactive = **avg_usage_score_per_month** **between 0** and **9**  then **0** </br>
* Occasional = **avg_usage_score_per_month** **between 10** and **14**  then **50** </br>
* Active = **avg_usage_score_per_month** **>= 15**  then **100** </br>

```
Where 
avg_usage_score_per_month = sum(days_of_activity) as AllActivity /NbOfMonths
```

#### Net Promoter Score
Is a customer loyalty and satisfaction measurement taken from asking customers how likely they are to recommend your product or service to others.</br>
Among the **Net Promoter Score** tab of the excel file, I selected the **nps_score** as the main column which refers to the score given by the customer for a given period of time.
```
Let's consider that the customer X, on the 2018/06 gave a score of 8 
				    on the 2019/01 gave a score of 7 
				    on the 2020/03 gave a score of 9 
				    Avg_score = sum(8,7,9)/3 = 8
The customer X has a score of 8, meaning he is satisfied with the product and will highly recommend it to others.
```
![alt text](https://github.com/ChaimaJelassi/cs-test/blob/main/images/nps.PNG)

Depending on the score given to the Net Promoter question, three categories of people can be distinguished:

* Detractors = **avg_Score_nps** **>0** and **<=3**  then **0**
* Passives =  **avg_Score_nps** **>3** and **<=6**  then **50**
* Promoters =  **avg_Score_nps** **>6**  then **100**

#### Satisfaction
Is how much users are satisfied with the support team.
![alt text](https://github.com/ChaimaJelassi/cs-test/blob/main/images/satisfaction.PNG)

#### Certification 
Is how much users are certified and recognized as potential power users.
![alt text](https://github.com/ChaimaJelassi/cs-test/blob/main/images/certification.PNG)
### Calculation Method


