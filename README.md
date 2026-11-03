# Exno:1
Data Cleaning Process

# AIM
To read the given data and perform data cleaning and save the cleaned data to a file.

# Explanation
Data cleaning is the process of preparing data for analysis by removing or modifying data that is incorrect ,incompleted , irrelevant , duplicated or improperly formatted. Data cleaning is not simply about erasing data ,but rather finding a way to maximize datasets accuracy without necessarily deleting the information.

# Algorithm
STEP 1: Read the given Data

STEP 2: Get the information about the data

STEP 3: Remove the null values from the data

STEP 4: Save the Clean data to the file

STEP 5: Remove outliers using IQR

STEP 6: Use zscore of to remove outliers


# Coding and Output
```

import pandas as pd
import numpy as np
from scipy import stats
import matplotlib.pyplot as plt

df = pd.read_csv("Loan_data.csv")

print(df.head())           # First 5 rows
print(df.shape)            # Rows, columns
print(df.info())           # Data types
print(df.isnull().sum())   # Missing values
print(df.duplicated().sum()) # Duplicates
df = df.drop_duplicates()

# Numerical columns → median
for col in df.select_dtypes(include="number").columns:
    df[col] = df[col].fillna(df[col].median())

# Text columns → most common value (mode)
for col in df.select_dtypes(include="object").columns:
    if df[col].isnull().any() and not df[col].mode().empty:
        df[col] = df[col].fillna(df[col].mode()[0])

print(df.isnull().sum())
col = "ApplicantIncome"  # change if needed

Q1 = df[col].quantile(0.25)
Q3 = df[col].quantile(0.75)
IQR = Q3 - Q1

lower = Q1 - 1.5 * IQR
upper = Q3 + 1.5 * IQR

outliers = df[(df[col] < lower) |
              (df[col] > upper)]
print(outliers[[col]])

plt.boxplot(df[col].dropna())
plt.title("Possible outliers")
plt.show()
col = "ApplicantIncome"  # numeric column

z = np.abs(stats.zscore(
    df[col], nan_policy="omit"
))

outliers = df[z > 3]
print(outliers[[col]])

# Save the cleaned dataset
df.to_csv("cleaned_data.csv", index=False)
print("Saved successfully!")
```
# Result
[ds exp 1.pdf](https://github.com/user-attachments/files/33959573/ds.exp.1.pdf)
