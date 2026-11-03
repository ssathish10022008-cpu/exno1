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
import matplotlib.pyplot as plt
from google.colab import files
uploaded = files.upload()
file_name = next(iter(uploaded))
df = pd.read_csv(file_name)
print("ORIGINAL DATASET")
display(df)
df.columns = df.columns.str.strip()
df["name"] = df["name"].astype("string").str.strip()
df["height"] = pd.to_numeric(df["height"], errors="coerce")


df["name"] = df["name"].replace("", pd.NA)


df = df.drop_duplicates()


df = df.dropna(subset=["height"])

print("\nDATASET AFTER DATA CLEANING")
display(df)

Q1 = df["height"].quantile(0.25)
Q3 = df["height"].quantile(0.75)
IQR = Q3 - Q1

lower_limit = Q1 - 1.5 * IQR
upper_limit = Q3 + 1.5 * IQR

print("\nQ1:", Q1)
print("Q3:", Q3)
print("IQR:", IQR)
print("Lower Limit:", lower_limit)
print("Upper Limit:", upper_limit)

outliers = df[
    (df["height"] < lower_limit) |
    (df["height"] > upper_limit)
]

print("\nDETECTED OUTLIERS")
display(outliers)


plt.figure(figsize=(6, 4))
plt.boxplot(df["height"])
plt.title("Height Before Outlier Removal")
plt.ylabel("Height")
plt.show()


df_clean = df[
    (df["height"] >= lower_limit) &
    (df["height"] <= upper_limit)
].copy()

print("\nDATASET AFTER OUTLIER REMOVAL")
display(df_clean)

print("Original rows:", len(df))
print("Outliers detected:", len(outliers))
print("Rows after removal:", len(df_clean))

plt.figure(figsize=(6, 4))
plt.boxplot(df_clean["height"])
plt.title("Height After Outlier Removal")
plt.ylabel("Height")
plt.show()
```
```

import pandas as pd
import matplotlib.pyplot as plt
from google.colab import files


uploaded = files.upload()
file_name = next(iter(uploaded))
df = pd.read_csv(file_name)

print("ORIGINAL DATASET")
display(df.head())


df.columns = df.columns.str.strip()


df = df.drop_duplicates()


text_columns = df.select_dtypes(include=["object", "string"]).columns

for col in text_columns:
    df[col] = df[col].apply(
        lambda x: x.strip() if isinstance(x, str) else x
    )
    df[col] = df[col].replace("", pd.NA)


numeric_columns = [
    "num_episodes",
    "rating",
    "current_overall_rank",
    "lifetime_popularity_rank",
    "watchers"
]

for col in numeric_columns:
    df[col] = pd.to_numeric(df[col], errors="coerce")


df["show_name"] = df["show_name"].fillna("Unknown")


categorical_columns = df.select_dtypes(
    include=["object", "string"]
).columns

for col in categorical_columns:
    if df[col].isnull().any():
        mode_value = df[col].mode()
        if not mode_value.empty:
            df[col] = df[col].fillna(mode_value.iloc[0])


for col in numeric_columns:
    if df[col].isnull().any():
        median_value = df[col].median()
        if pd.notna(median_value):
            df[col] = df[col].fillna(median_value)

print("\nMISSING VALUES AFTER CLEANING")
print(df.isnull().sum())

print("\nDATASET AFTER DATA CLEANING")
display(df.head())

# 2. OUTLIER DETECTION USING IQR

outlier_columns = [
    "num_episodes",
    "rating",
    "watchers"
]

outlier_rows = pd.Series(False, index=df.index)

for col in outlier_columns:
    Q1 = df[col].quantile(0.25)
    Q3 = df[col].quantile(0.75)
    IQR = Q3 - Q1

    lower_limit = Q1 - 1.5 * IQR
    upper_limit = Q3 + 1.5 * IQR

    mask = (df[col] < lower_limit) | (df[col] > upper_limit)
    outlier_rows = outlier_rows | mask

    print(f"\nColumn: {col}")
    print("Q1:", Q1)
    print("Q3:", Q3)
    print("IQR:", IQR)
    print("Lower Limit:", lower_limit)
    print("Upper Limit:", upper_limit)
    print("Outliers Detected:", int(mask.sum()))

    # Boxplot before removal
    plt.figure(figsize=(6, 4))
    plt.boxplot(df[col])
    plt.title(f"{col} Before Outlier Removal")
    plt.ylabel(col)
    plt.show()
print("\nDETECTED OUTLIER ROWS")
display(df[outlier_rows])

# 3. REMOVE OUTLIERS
df_clean = df[~outlier_rows].copy()

print("\nDATASET AFTER OUTLIER REMOVAL")
display(df_clean)

print("Rows before removal:", len(df))
print("Outlier rows removed:", int(outlier_rows.sum()))
print("Rows remaining:", len(df_clean))

# 4. BOXPLOTS AFTER REMOVAL
for col in outlier_columns:
    plt.figure(figsize=(6, 4))
    plt.boxplot(df_clean[col])
    plt.title(f"{col} After Outlier Removal")
    plt.ylabel(col)
    plt.show()


```
# Output
<img width="840" height="531" alt="Screenshot 2026-10-27 152811" src="https://github.com/user-attachments/assets/263bab15-53a3-4dcc-b667-046f1bfe2859" />
<img width="727" height="295" alt="Screenshot 2026-10-27 153108" src="https://github.com/user-attachments/assets/570368b6-207d-4811-9bfd-28a457562daa" />
<img width="412" height="502" alt="Screenshot 2026-10-27 153027" src="https://github.com/user-attachments/assets/ed2766a6-a610-457f-8873-e6bdff818aab" />
<img width="682" height="360" alt="Screenshot 2026-10-27 153250" src="https://github.com/user-attachments/assets/4e0c0677-b878-402c-80bc-193c40edb2e5" />
<img width="490" height="471" alt="Screenshot 2026-10-27 153220" src="https://github.com/user-attachments/assets/13c6e2d0-9cd7-4d03-8dad-d3c69debf37b" />
<img width="788" height="357" alt="Screenshot 2026-10-27 153143" src="https://github.com/user-attachments/assets/83cecca5-a38a-436f-800f-d7ca97140f80" />

# Output 
<img width="870" height="212" alt="Screenshot 2026-10-27 153422" src="https://github.com/user-attachments/assets/5bc682d5-a8de-465f-84b6-8e2691abdfb0" />

<img width="701" height="173" alt="Screenshot 2026-10-27 153525" src="https://github.com/user-attachments/assets/afb8ebb3-dca7-466c-9a55-76eb70a79821" />

<img width="865" height="253" alt="Screenshot 2026-10-27 153551" src="https://github.com/user-attachments/assets/f0dc1c8c-cb93-4c5b-93e3-89bb19bc55a1" />

<img width="648" height="412" alt="Screenshot 2026-10-27 153722" src="https://github.com/user-attachments/assets/a8898268-9399-4739-a77b-3a36638a86fd" />

<img width="560" height="393" alt="Screenshot 2026-10-27 153751" src="https://github.com/user-attachments/assets/136d845e-cf28-486d-85e9-533c327c2d4f" />

<img width="617" height="422" alt="Screenshot 2026-10-27 153835" src="https://github.com/user-attachments/assets/99f1325b-7739-4edb-9568-ed57839fbab3" />
<img width="1118" height="470" alt="Screenshot 2026-10-27 154038" src="https://github.com/user-attachments/assets/0d8d798f-faff-42ad-b60b-1ce28eaef20d" />
<img width="1070" height="371" alt="Screenshot 2026-10-27 154158" src="https://github.com/user-attachments/assets/73e0c572-f604-4808-8af5-8b33c9dce0a1" />
<img width="482" height="838" alt="Screenshot 2026-10-27 154318" src="https://github.com/user-attachments/assets/c0976fb4-3d9f-44bd-847d-de6e03d4993b" />




# Summary

I used Pandas for data cleaning and Matplotlib for data visualization.  The first program focuses on cleaning a height dataset and removing outliers using the IQR method. The second program performs advanced data cleaning by handling missing values using mode and median imputation, removing duplicates, and detecting outliers in multiple numerical columns. Finally, boxplots are used to compare the datasets before and after outlier removal. I chose the IQR method because it is simple, easy to implement and does not require the data to follow a normal distribution. It is less affected by extreme values compared to the Z-score method. It also helps to detect outliers using Q1, Q3 and the IQR range. Therefore, I selected IQR for my datasets."



# Result
          
Thus,the Data cleaning process and outliers detection and removal is verified.
