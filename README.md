# EX-04 EXPLORATORY DATA ANALYSIS
# AIM:
To perform Exploratory Data Analysis on the given data set.

# EXPLANATION:
The primary aim with exploratory analysis is to examine the data for distribution, outliers and anomalies to direct specific testing of your hypothesis.

# ALGORITHM:
# STEP 1:
Import the required packages to perform Data Cleansing,Removing Outliers and Exploratory Data Analysis.

# STEP 2:
Use boxplot method to analyze the outliers of the given dataset.

# STEP 3:
Remove the outliers using Inter Quantile Range method.

# STEP 4:
Use Countplot method to analyze in a graphical method for categorical data.

# STEP 5:
Use displot method to represent the univariate distribution of data.

# STEP 6:
Use cross tabulation method to quantitatively analyze the relationship between multiple variables.

# STEP 7:
Use heatmap method of representation to show relationships between two variables, one plotted on each axis.

# CODE:
```
import pandas as pd
import numpy as np
import seaborn as sns
df=pd.read_csv("supermarket.csv")
df.info()
df.isnull().sum()
df.boxplot()
Q1 = df.quantile(0.25)
Q3 = df.quantile(0.75)
IQR = Q3 - Q1
df = df[~((df < (Q1 - 1.5 * IQR)) |(df > (Q3 + 1.5 * IQR))).any(axis=1)]
df.boxplot()
df["City"].value_counts()
df["Customer type"].value_counts()
df["Gender"].value_counts()
df["Product line"].value_counts()
df["Payment"].value_counts()
df["Branch"].value_counts()
sns.countplot(x="Branch",data=df)
sns.countplot(x="Gender",data=df)
sns.countplot(x="Product line",data=df)
sns.countplot(x="Payment",data=df)
sns.countplot(x="Gender",hue="Product line",data=df)
sns.countplot(x="Customer type",hue="City",data=df)
sns.displot(df[df["Branch"]=="A"]["Gender"])
sns.displot(df["Branch"])
sns.displot(df[df["Product line"]=="Electronic accessories"]["Gender"])
sns.displot(df[df["Payment"]=="Credit card"]["Branch"])
pd.crosstab(df["Gender"],df["Branch"])
pd.crosstab(df["City"],df["Customer type"])
df.corr()
sns.heatmap(df.corr(),annot=True)
```

# OUTPUT:

# EXAMINING THE DATA:
![img1](https://user-images.githubusercontent.com/93427923/162758075-29f81f79-29e5-40d0-88ad-7a30af27708a.png)

# BOXPLOT METHOD TO ANALYZE OUTLIERS:
![img2](https://user-images.githubusercontent.com/93427923/162758201-9c7dfe5a-c7ea-4467-b9b2-d37d5726013e.png)

# REMOVING OUTLIERS USING IQR METHOD:
![img3](https://user-images.githubusercontent.com/93427923/162758329-2e08357d-c01f-4752-b4b0-14424826b05b.png)

# COUNTPLOT METHOD FOR DATA ANALYSIS:
![img4 1](https://user-images.githubusercontent.com/93427923/162758438-377a5b64-bf16-4bb9-b47e-ddc9db889c45.png)
![4 2](https://user-images.githubusercontent.com/93427923/162758484-5c4c5224-004b-454d-a9fb-07476d638255.png)
![img4 3](https://user-images.githubusercontent.com/93427923/162758516-7889eeff-2843-45ec-a03e-d6ec90e54289.png)

# DISPLOT METHOD FOR DATA ANALYSIS:
![img5](https://user-images.githubusercontent.com/93427923/162758681-a5c1192e-34ef-431c-82d6-47a821eb4ec7.png)
![img5 1](https://user-images.githubusercontent.com/93427923/162758713-5920e387-ef09-43dc-81b0-44d3661c97d4.png)

# COUNTPLOT METHOD TO COMPARE TWO ENTITIES:
![img6](https://user-images.githubusercontent.com/93427923/162758888-e4907048-c0bb-49b3-af8e-95c276853349.png)

# CROSS TABULATION FOR DATA ANALYSIS:
![img7](https://user-images.githubusercontent.com/93427923/162759041-4ff0ca5d-097b-4350-a663-c68ff318d5aa.png)

# CORRELATION METHOD:
![img8](https://user-images.githubusercontent.com/93427923/162759166-7a0db8b3-8a6c-4864-be9e-9f225be5a52c.png)

# INFERENCE:
1. More customers visited the supermarket from the city Yangon.
2. The most purchased department in the supermarket is Fashion accessories.
3. Cash and E-wallet is the most preferred payment mode by the customers.
4. Maximum number of customers visited the Branch A of supermarket.
5. Male customers are highest visitors of supermarket in Branch B.

# RESULT:
Hence the given data set has undergone data cleansing and outlier removal.Later Exploratory data analysis is done to get inferences from the given data set.













