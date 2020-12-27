# Data Preprocessing

**CREDIT_CURRENCY**

Since the number of observations in the categories is low, it was set as 0 and 1.

```python
df.loc[(df["CREDIT_CURRENCY"] == "currency 1"),"CREDIT_CURRENCY"] = 0 "Current 1"
df.loc[(df["CREDIT_CURRENCY"] == "currency 2"),"CREDIT_CURRENCY"] = 1 "Current 2"
df.loc[(df["CREDIT_CURRENCY"] == "currency 3"),"CREDIT_CURRENCY"] = 1 "Current 3"
df.loc[(df["CREDIT_CURRENCY"] == "currency 4"),"CREDIT_CURRENCY"] = 1 "Current 4"
```

**CREDIT_ACTIVE**

------

In this variable, the **Sold ** and **Bad Debt ** classes do not provide a significant difference due to the smallness of the dataset, so are assigned as **Active **.

```python
bureau.loc[(bureau["CREDIT_ACTIVE"] == "Bad debt"),"CREDIT_ACTIVE"] = "Active"
bureau.loc[(bureau["CREDIT_ACTIVE"] == "Sold"),"CREDIT_ACTIVE"] = "Active"
```



**CNT_CREDIT_PROLONG **

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

0. Binary encoding is applied as 0 and 1 because it does not provide a meaningful variety except for class.

```python
df.loc[(df["CNT_CREDIT_PROLONG"] == 0),"CNT_CREDIT_PROLONG"] = 0 #people who have not extended their credit in other places
df.loc[(df["CNT_CREDIT_PROLONG"] != 0),"CNT_CREDIT_PROLONG"] = 1 #people who have extended their credit in other places
```



**CREDIT_TYPE **

------

There are 15 categories of variables that show the loan type;

```python
CREDIT_TYPE : has 15 unique category    - object                                              CREDIT_TYPE    Count Ratio  
    1251615  1716428 72.920 Consumer credit                         
     402195  1716428 23.432  Credit card                                  
      27690  1716428  1.613   Car loan
      18391  1716428  1.071   Mortgage                                  
```

We will apply rare encoding to categories below 1%.

```python
bureau.loc[(bureau["CREDIT_TYPE"] == "Microloan"),"CREDIT_TYPE"] = "Rare"
bureau.loc[(bureau["CREDIT_TYPE"] == "Loan for business development"),"CREDIT_TYPE"] = "Rare"
bureau.loc[(bureau["CREDIT_TYPE"] == "Another type of loan"),"CREDIT_TYPE"] = "Rare"
bureau.loc[(bureau["CREDIT_TYPE"] == "Loan for working capital replenishment"),"CREDIT_TYPE"] = "Rare"
bureau.loc[(bureau["CREDIT_TYPE"] == "Unknown type of loan"),"CREDIT_TYPE"] = "Rare"
bureau.loc[(bureau["CREDIT_TYPE"] == "Cash loan (non-earmarked)"),"CREDIT_TYPE"] = "Rare"
bureau.loc[(bureau["CREDIT_TYPE"] == "Real estate loan"),"CREDIT_TYPE"] = "Rare"
bureau.loc[(bureau["CREDIT_TYPE"] == "Loan for the purchase of equipment"),"CREDIT_TYPE"] = "Rare"
```



## **NEW FEATURES**



**Feature1**

**The number of credits a customer has differently:**

**BUREAU_LOAN_COUNT**

------



```python
grp = df[['SK_ID_CURR', 'DAYS_CREDIT']].groupby(by = ['SK_ID_CURR'])['DAYS_CREDIT'].count().reset_index().rename(index=str, columns={'DAYS_CREDIT': 'BUREAU_LOAN_COUNT'})
```

And we add these results to the table.

```python
df = df.merge(grp, on = ['SK_ID_CURR'], how = 'left')
```



**Feature2**

 **How many different types of loans a customer has :**

**BUREAU_LOAN_TYPES**

------



```python
grp = df[['SK_ID_CURR', 'CREDIT_TYPE']].groupby(by = ['SK_ID_CURR'])['CREDIT_TYPE'].nunique().reset_index().rename(index=str, columns={'CREDIT_TYPE': 'BUREAU_LOAN_TYPES'})
```

And we add these results to the table.

```python
df = df.merge(grp, on = ['SK_ID_CURR'], how = 'left')
```



**Feature3**  

**The ratio of different types of loans a customer takes:**

**AVERAGE_LOAN_TYPE**

------

We knew from the previous feature how many different types of credits a person had. (loan types)

```python
grp = df[['SK_ID_CURR', 'CREDIT_TYPE']].groupby(by = ['SK_ID_CURR'])['CREDIT_TYPE'].nunique().reset_index().rename(index=str, columns={'CREDIT_TYPE': 'BUREAU_LOAN_TYPES'})
```

To find the ratio

```python
df['AVERAGE_LOAN_TYPE'] = df['BUREAU_LOAN_COUNT']/df['BUREAU_LOAN_TYPES'] 
```

And we add these results to the table.

```python
df = df.merge(grp, on = ['SK_ID_CURR'], how = 'left')
del df['BUREAU_LOAN_COUNT'], df['BUREAU_LOAN_TYPES']
```



**Feature4** 

**Percentage of active loans compared to other loans in Bureau data:**

**ACTIVE_LOANS_PERCENTAGE**

------

The fact that the results are close to 1 in this variable shows us that their active loans are too bad for us.

We create a new variable to separate whether the loan is active or not.

```python
df['CREDIT_ACTIVE_BINARY'] = df['CREDIT_ACTIVE']

df.loc[(B['CREDIT_ACTIVE'] == "Closed"), 'CREDIT_ACTIVE_BINARY'] = 0 df.loc[(B['CREDIT_ACTIVE'] != "Closed"), 'CREDIT_ACTIVE_BINARY'] = 1
      
```

Calculate the average number of **ACTIVE** loans per customer

```python
['CREDIT_ACTIVE_BINARY'] = df['CREDIT_ACTIVE_BINARY'].astype('int32')# Mgrp = df.groupby(by = ['SK_ID_CURR'])
['CREDIT_ACTIVE_BINARY'].mean().reset_index().rename(index=str, columns={'CREDIT_ACTIVE_BINARY': 'ACTIVE_LOANS_PERCENTAGE'})
```

And we add these results to the table.

```python
df = df.merge(grp, on = ['SK_ID_CURR'], how = 'left') 
del df['CREDIT_ACTIVE_BINARY']
```



**Feature5**

**To find out how many days a person receives new loans: **

**DAYS_CREDIT_DIFF**

------

We have listed the days when the different loans a person gets;

```python
grp = B[['SK_ID_CURR', 'SK_ID_BUREAU', 'DAYS_CREDIT']].groupby(by = ['SK_ID_CURR'])
grp1 = grp.apply(lambda x: x.sort_values(['DAYS_CREDIT'], ascending = False)).reset_index(drop = True)#rename(index = str, columns = {'DAYS_CREDIT': 'DAYS_CREDIT_DIFF'})
```

The difference in days between credits is calculated;

```python
grp1['DAYS_CREDIT1'] = grp1['DAYS_CREDIT']*-1
grp1['DAYS_DIFF'] = grp1.groupby(by = ['SK_ID_CURR'])['DAYS_CREDIT1'].diff()
grp1['DAYS_DIFF'] = grp1['DAYS_DIFF'].fillna(0).astype('uint32')
del grp1['DAYS_CREDIT1'], grp1['DAYS_CREDIT'], grp1['SK_ID_CURR']
```

And we add these results to the main table;

```python
B = B.merge(grp1, on = ['SK_ID_BUREAU'], how = 'left')
```



**Feature6**

**Average of the number of loans with continuing payments on a customer basis **

**CREDIT_ENDDATE_PERCENTAGE**

------

The fact that our results are close to 1 here is a bad sign.

We create a new variable.

```python
B['CREDIT_ENDDATE_BINARY'] = B['DAYS_CREDIT_ENDDATE']
```

We give "0" to closed loans, and 1 to active loans.

```python
B.loc[(B['DAYS_CREDIT_ENDDATE'] < 0),"CREDIT_ENDDATE_BINARY"] = 0 
B.loc[(B['DAYS_CREDIT_ENDDATE'] >= 0),"CREDIT_ENDDATE_BINARY"] = 1
```

If the average result of the number of loans that continue to be paid is close to 1, it is bad for us, that is, it has too many active loans, and if it is close to 0, the result is good for us, so there are less actype loans.

```python
grp = B.groupby(by = ['SK_ID_CURR'])['CREDIT_ENDDATE_BINARY'].mean().reset_index().rename(index=str, columns={'CREDIT_ENDDATE_BINARY': 'CREDIT_ENDDATE_PERCENTAGE'})
```

And we add these results to the main table.

```python
B = B.merge(grp, on = ['SK_ID_CURR'], how = 'left') 

del B['CREDIT_ENDDATE_BINARY'] #gereksiz olan binary columnun düşürülmesi
```



**Feature7**

**Percentage of debt paid**

**NEW_AMT_PER_PAY**

------

- Its close to 0 is good, close to 1 is bad just like **Target**
- AMT_CREDIT_SUM shows the total credit amount.
- AMT_CREDIT_SUM_DEBT shows the remaining debt amount.
- It can be meant to all minus credits of a single customer. (to percentage of debt paid)

```python
df[NEW_AMT_PER_PAY]= 1 - ((df["AMT_CREDIT_SUM"]- df["AMT_CREDIT_SUM_DEBT"]) / df["AMT_CREDIT_SUM"])
```



**Feature8**

##### Day differences between outstanding loans

##### DAYS_ENDDATE_DIFF

------



```python
#NOTE: You can import mean and sum in Groupby aggregation operation.

B = bureau[0:10000]
B['CREDIT_ENDDATE_BINARY'] = B['DAYS_CREDIT_ENDDATE']

#Values for those whose payments are continuing (+) and those whose payments are over (0 or -)
B.loc[(bureau["DAYS_CREDIT_ENDDATE"] <= 0), "CREDIT_ENDDATE_BINARY"] = 0 #overpaid (Closed) loans
B.loc[(bureau["DAYS_CREDIT_ENDDATE"] > 0), "CREDIT_ENDDATE_BINARY"] = 1 #Active loans

# Operation will be made on credits with ongoing payments
B1 = B[B['CREDIT_ENDDATE_BINARY'] == 1]

# Calculation of the day difference between loans to completion

# Create Dummy Column for CREDIT_ENDDATE 
B1['DAYS_CREDIT_ENDDATE1'] = B1['DAYS_CREDIT_ENDDATE']
# Groupby Each Customer ID 
grp = B1[['SK_ID_CURR', 'SK_ID_BUREAU', 'DAYS_CREDIT_ENDDATE1']].groupby(by = ['SK_ID_CURR'])
# Sorting of loan payment periods from small to large.
grp1 = grp.apply(lambda x: x.sort_values(['DAYS_CREDIT_ENDDATE1'], ascending = True)).reset_index(drop = True) 
del grp
gc.collect()
print("Grouping and Sorting done")

#In the  diff function, the nan values in the first lines are filled with 0.
grp1['DAYS_ENDDATE_DIFF'] = grp1.groupby(by = ['SK_ID_CURR'])['DAYS_CREDIT_ENDDATE1'].diff()
grp1['DAYS_ENDDATE_DIFF'] = grp1['DAYS_ENDDATE_DIFF'].fillna(0).astype('uint32')
del grp1['DAYS_CREDIT_ENDDATE1'], grp1['SK_ID_CURR']
gc.collect()
print("Difference days calculated")

# merging with the main table
B = B.merge(grp1, on = ['SK_ID_BUREAU'], how = 'left')
del grp1
gc.collect()

```



**Feature9**

**The ratio of the person's total overdue debt to the total current debt **

**OVERDUE_DEBT_RATIO**

------

B = df[0:10000]

The **NaN** values are taken as no debt.

```python
B['AMT_CREDIT_SUM_DEBT'] = B['AMT_CREDIT_SUM_DEBT'].fillna(0)
```

**NaN** values received as no delay.

```python
B['AMT_CREDIT_SUM_OVERDUE'] = B['AMT_CREDIT_SUM_OVERDUE'].fillna(0) 


```

grp1 total debt of a person

```python
grp1 = B[['SK_ID_CURR', 'AMT_CREDIT_SUM_DEBT']].groupby(by = ['SK_ID_CURR'])['AMT_CREDIT_SUM_DEBT'].sum().reset_index().rename( index = str, columns = {'AMT_CREDIT_SUM_DEBT': 'TOTAL_CUSTOMER_DEBT'})
```

grp2 total outstanding debt of a person

```python
grp2 = B[['SK_ID_CURR', 'AMT_CREDIT_SUM_OVERDUE']].groupby(by = ['SK_ID_CURR'])['AMT_CREDIT_SUM_OVERDUE'].sum().reset_index().rename( index = str, columns = {'AMT_CREDIT_SUM_OVERDUE': 'TOTAL_CUSTOMER_OVERDUE'})


```

And we add our results to the main table.

```python
B = B.merge(grp1, on = ['SK_ID_CURR'], how = 'left')
B = B.merge(grp2, on = ['SK_ID_CURR'], how = 'left')
```

We delete variables that will no longer work for us.

```python
del grp1, grp2
gc.collect()
```

the ratio of the customer's total outstanding debt to total debt

```python
B['OVERDUE_DEBT_RATIO'] = B['TOTAL_CUSTOMER_OVERDUE']/B['TOTAL_CUSTOMER_DEBT'] 
```

Unnecessary generated columns are removed

```python
del B['TOTAL_CUSTOMER_OVERDUE'], B['TOTAL_CUSTOMER_DEBT'] 
gc.collect()
```

After this step, we make our categorical variables numeric by applying One Hot Encoding.

```python
bureau, bureau_cat = one_hot_encoder(bureau, nan_as_category=True)
```

Later, we derive numeric features for our Bureau set.

```python
num_aggregations = {
        'DAYS_CREDIT': ['min', 'max', 'mean', 'var'],
        'DAYS_CREDIT_ENDDATE': ['min', 'max', 'mean'],
        'DAYS_CREDIT_UPDATE': ['mean'],
        'CREDIT_DAY_OVERDUE': ['max', 'mean'],
        'AMT_CREDIT_MAX_OVERDUE': ['mean'],
        'AMT_CREDIT_SUM': ['max', 'mean', 'sum'],
        'AMT_CREDIT_SUM_DEBT': ['max', 'mean', 'sum'],
        'AMT_CREDIT_SUM_OVERDUE': ['mean'],
        'AMT_CREDIT_SUM_LIMIT': ['mean', 'sum'],
        'AMT_ANNUITY': ['max', 'mean'],
        'CNT_CREDIT_PROLONG': ['sum'],
        "NEW_DANGER" : ["max"],
        "NEW_BUREAU_LOAN_COUNT" : ["max"],
        "NEW_AVERAGE_LOAN_TYPE" : ["mean"],
        "NEW_ACTIVE_LOANS_PERCENTAGE" : ["max"],
        "NEW_DAYS_DIFF" : ["min","max","mean"],
        "NEW_CREDIT_ENDDATE_PERCENTAGE" : ["mean"],
        "NEW_AMT_PER_PAY" : ["min","max","mean"],
        "DAYS_ENDDATE_DIFF" : ["mean","sum","min"],
        "NEW_OVERDUE_DEBT_RATIO" : ["mean"]

}
```

For Categoric Featuers;

```python
cat_aggregations = {}
for cat in bureau_cat: cat_aggregations[cat] = ['mean']
#for cat in bb_cat: cat_aggregations[cat + "_MEAN"] = ['mean'] 
#Sadece bureau ile ilgilendigimiz icin bb'sı almıyoruz
```

Then we will groupby the bureau with these variables we derive.

```python
bureau_agg = bureau.groupby('SK_ID_CURR').agg({**num_aggregations, **cat_aggregations})
```

We would like to add BURO to the new variables we have introduced so that they can be understood.

```python
bureau_agg.columns = pd.Index(['BURO_' + e[0] + "_" + e[1].upper() for e in bureau_agg.columns.tolist()])
```

Pre-model variable numbers:

```python
train_df.shape
(307511, 55)

test_df.shape
(48744, 55)
```



##### * Model Result *:

------

![](C:\Users\acer\Desktop\HomeCreditDefaultRisk\TablesAnalysisReport\Bureau\images\basemodel.png)

![](C:\Users\acer\Desktop\HomeCreditDefaultRisk\TablesAnalysisReport\Bureau\images\importancelast.png)

------



