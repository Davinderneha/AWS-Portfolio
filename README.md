---
jupyter:
  colab:
    kernelspec:
      display_name: Python 3
      name: python3
    language_info:
      name: python
  nbformat: 4
  nbformat_minor: 0
---

# AWS Cloud Architecture Diagram

![](vertopal_5eef928284d744e38e5f94395c91097b/diagram-export-9-21-2024-7_54_53-PM.png)

## Data Analysis on Business Licences

### Cell 1: Load and clean the dataset

```python
import pandas as pd

# Load the dataset using semicolon as the delimiter
file_path = 'business-licences.csv'
business_data_cleaned = pd.read_csv(file_path, delimiter=';')

# Display the first few rows of the cleaned dataset to understand its structure
business_data_cleaned.head()
```

### Cell 2: Check for missing values

```python
# Check for missing values in the dataset
missing_values = business_data_cleaned.isnull().sum()

# Display the columns with missing values and the count
missing_values[missing_values > 0]
```

### Output 2

```json
{
  "BusinessName": 4180,
  "BusinessTradeName": 37826,
  "IssuedDate": 3851,
  "ExpiredDate": 3843,
  "BusinessSubType": 54720,
  "Unit": 45608,
  "UnitType": 45685,
  "House": 26911,
  "Street": 26907,
  "City": 12,
  "Province": 19,
  "Country": 8760,
  "PostalCode": 27212,
  "LocalArea": 2584,
  "FeePaid": 52428,
  "Geom": 29589,
  "geo_point_2d": 29589
}
```

### Cell 3: Drop rows with missing values

```python
# Drop rows with any missing values
business_data_dropped_na = business_data_cleaned.dropna()

# Display the shape of the new dataset to see how many rows remain
business_data_dropped_na.shape
```

### Output 3

```
(69, 25)
```

### Cell 4: Select relevant columns for business analysis

```python
# Select relevant columns for business analysis
columns_of_interest = ['BusinessName', 'BusinessTradeName', 'BusinessType', 'IssuedDate', 'ExpiredDate', 'City', 'Province', 'PostalCode', 'LocalArea', 'NumberofEmployees']

# Create a new DataFrame with only the selected columns
business_data_filtered = business_data_dropped_na[columns_of_interest]

# Display the first few rows of the filtered dataset
business_data_filtered.head()
```

### Cell 5: Convert 'IssuedDate' and 'ExpiredDate' to datetime format

```python
# Convert 'IssuedDate' and 'ExpiredDate' to datetime format
business_data_filtered['IssuedDate'] = pd.to_datetime(business_data_filtered['IssuedDate'])
business_data_filtered['ExpiredDate'] = pd.to_datetime(business_data_filtered['ExpiredDate'])

# Check if the conversion was successful
business_data_filtered.dtypes
```

### Cell 6: Summary statistics for numerical columns

```python
# Generate summary statistics for numerical columns
business_data_filtered.describe()
```

### Cell 7: Count the number of businesses by type

```python
# Count the number of businesses by their type
business_count_by_type = business_data_filtered['BusinessType'].value_counts()

# Display the count of businesses by type
business_count_by_type
```

### Cell 8: Plot the distribution of businesses by their type

```python
import matplotlib.pyplot as plt

# Plot the distribution of businesses by their type
plt.figure(figsize=(10, 6))
business_count_by_type.plot(kind='bar')
plt.title('Number of Businesses by Type')
plt.xlabel('Business Type')
plt.ylabel('Number of Businesses')
plt.xticks(rotation=45, ha='right')
plt.tight_layout()
plt.show()
```

### Cell 9: Plot the distribution of the number of employees

```python
# Plot the distribution of the number of employees
plt.figure(figsize=(8, 6))
business_data_filtered['NumberofEmployees'].plot(kind='hist', bins=20)
plt.title('Distribution of Number of Employees')
plt.xlabel('Number of Employees')
plt.ylabel('Frequency')
plt.tight_layout()
plt.show()
```

### Cell 10: Time trend of issued licenses

```python
# Create a time series of the number of businesses issued over time
business_data_filtered.set_index('IssuedDate', inplace=True)

# Resample by month to see the trend over time
business_trend_over_time = business_data_filtered.resample('M').size()

# Plot the time trend of issued licenses
plt.figure(figsize=(10, 6))
business_trend_over_time.plot()
plt.title('Trend of Business Licenses Issued Over Time')
plt.xlabel('Date')
plt.ylabel('Number of Licenses Issued')
plt.tight_layout()
plt.show()
```
