# FEATURE ANALYSIS



# **MISSING VALUES**

| Missing Values         | % of Total Values |        |
| ---------------------- | ----------------- | ------ |
| AMT_ANNUITY            | 1226791           | 71.500 |
| AMT_CREDIT_MAX_OVERDUE | 1124488           | 65.500 |
| DAYS_ENDDATE_FACT      | 633653            | 36.900 |
| AMT_CREDIT_SUM_LIMIT   | 591780            | 34.500 |
| AMT_CREDIT_SUM_DEBT    | 257669            | 15.000 |
| DAYS_CREDIT_ENDDATE    | 105553            | 6.100  |
| AMT_CREDIT_SUM         | 13                | 0.000  |



**DAYS_CREDIT**

------

The customer applied to the Credit office several days before the current application. (For another loan) (Variable based on days)

Our min value is 2922 and this value corresponds to 8 years.

![](C:\Users\acer\Desktop\HomeCreditDefaultRisk\TablesAnalysisReport\Bureau\images\days_credit.png)

Differences were observed at values higher than -1000 days.



**CREDIT_CURRENCY**

------

It is defined as the recoded currency of the credit bureau's loan.

There are 4 subgroups:

```
currency 1 , currency 2 , currency 3 , currency 4  
```



**CREDIT_ACTIVE**

------

Credits with reported status have subsets of **Closed**, **Active **, **Sold **, **Bad Debt.**

In this variable, the **Sold ** and **Bad Debt ** classes do not provide a significant difference due to the small number in the dataset, so they can be assigned as **Active **.



**CNT_CREDIT_PROLONG**

---

Variable indicating how many times the loan has been extended.

```python
CNT_CREDIT_PROLONG : has 10 unique category 	- int64

   CNT_CREDIT_PROLONG    Count  Ratio
0             1707314  1716428 99.469
1                7620  1716428  0.444
2                1222  1716428  0.071
3                 191  1716428  0.011
4                  54  1716428  0.003
5                  21  1716428  0.001
9                   2  1716428  0.000
6                   2  1716428  0.000
8                   1  1716428  0.000
7                   1  1716428  0.000
```



**CREDIT_TYPE**

------

There are 15 categories of variables that show the loan type;

```python
CREDIT_TYPE : has 15 unique category    - object                                              CREDIT_TYPE    Count Ratio  
    1251615  1716428 72.920 Consumer credit                         
     402195  1716428 23.432  Credit card                                  
      27690  1716428  1.613   Car loan
      18391  1716428  1.071   Mortgage                                  
```



**CREDIT_DAY_OVERDUE**

------

Expresses the customer delaying other loans in days ;

```python
# 0: not delayed 1: delayed
```

**NOT APPLIED **



**DAYS_CREDİT_ENDDATE**

------

- It gives the information about how many days before the loan application expired or how many days left.
- According to sector researches, mortgage, which is the longest loan type in the world, was determined for 50 years by Oguz.
- The oldest applicant is 69 years old. Even if this person applied for a loan at the age of 18, he is at the age of 50 years to afford the payment. With this in mind, we will take our max limit as 50 years in the days credit variable. We will discard data over 50 years.
- Those who go back from 0 form the audience that completed their payment. Credit_Active statuses: **CLOSED**
- Payments of those who move in a positive direction from 0 continue. Credit_Active statuses: **ACTIVE**



**DAYS_ENDDATE_FACT**

------

- The period in days since the expiry of the old loan at the Credi office at the time of the home loan application (Closed loan only). The maximum value was seen as 0, and the reason for this is that only the loans that are closed, that is to be paid, are included in this variable.
- The minimum values in the **DAYS_CREDİT_ENDDATE and DAYS_ENDDATE_FACT** variables were found to be -42023 (corresponding to a value close to 115 years) and these values were abnormal and this value was called noise.



**AMT_CREDIT_OVERDUE**

------

- The maximum amount of overdue credit in the loan with a credit bureau (until the Home Credit Loan application date)
- When missing values are excluded from the equation (112,000)
- Since it makes sense to None values, 0 is assigned, this will be checked after the model is created.
- When the variable is analyzed, 75% of the customers do not have delayed loan payments. We can classify the delayed ones according to their delayed amounts.
- When the slice larger than 99% was analyzed, it was seen that the lowest debt amount was 41.989, while the highest amount was 115.987.185.

