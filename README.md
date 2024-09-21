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

In modern cloud computing, designing a robust, scalable, and secure architecture is essential for deploying any application. The AWS cloud architecture diagram provided here shows a typical design pattern used in building cloud-native applications. It includes components such as EC2 instances, Elastic Load Balancers, and S3 storage, all of which are essential to achieving a high degree of availability, scalability, and security in the cloud.

## Data Analysis on Business Licences

Data analysis is the process of inspecting, cleansing, and modeling data to discover useful information, inform conclusions, and support decision-making. In this project, we will analyze a dataset of business licenses, which provides key information about various businesses in a specific area, such as their type, issuance date, number of employees, and other details. This kind of analysis can help city planners, regulators, and business owners understand market trends and operational dynamics.

### Cell 1: Load and clean the dataset

```python
import pandas as pd

# Load the dataset using semicolon as the delimiter
file_path = 'business-licences.csv'
business_data_cleaned = pd.read_csv(file_path, delimiter=';')

# Display the first few rows of the cleaned dataset to understand its structure
business_data_cleaned.head()
```

In this step, we load the dataset using the `pandas` library, a powerful tool for data manipulation and analysis in Python. The dataset is a CSV file containing business licenses, but it uses a semicolon as a delimiter instead of the more common comma. The `pd.read_csv()` function allows us to specify this delimiter and load the data properly.

### Cell 2: Check for missing values

```python
# Check for missing values in the dataset
missing_values = business_data_cleaned.isnull().sum()

# Display the columns with missing values and the count
missing_values[missing_values > 0]
```

After loading the data, the next critical step is to check for missing values. Missing values can cause errors in analysis or lead to inaccurate conclusions. By using the `isnull()` function in `pandas`, we can identify which columns have missing data and how many values are missing. This helps us decide how to handle the missing data, whether through deletion or imputation.

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

The output shows the number of missing values for each column. For example, `BusinessName` has 4180 missing values, and `BusinessTradeName` has 37,826 missing values. Depending on the significance of these columns, we may choose to drop rows or fill in missing values based on business logic.

### Cell 3: Drop rows with missing values

```python
# Drop rows with any missing values
business_data_dropped_na = business_data_cleaned.dropna()

# Display the shape of the new dataset to see how many rows remain
business_data_dropped_na.shape
```

To ensure that our analysis is based on complete and accurate data, we can remove rows with missing values using the `dropna()` function. While this reduces the number of rows in our dataset, it eliminates potential inaccuracies that could arise from incomplete data. However, it is essential to evaluate the impact of dropping rows—if too much data is removed, we may need to consider alternative approaches like filling missing values.

### Output 3

```
(69, 25)
```

Here, we see that after dropping rows with missing data, we are left with 69 rows and 25 columns. This tells us that a significant portion of the data was incomplete, and we should proceed carefully in the next steps.

### Cell 4: Select relevant columns for business analysis

```python
# Select relevant columns for business analysis
columns_of_interest = ['BusinessName', 'BusinessTradeName', 'BusinessType', 'IssuedDate', 'ExpiredDate', 'City', 'Province', 'PostalCode', 'LocalArea', 'NumberofEmployees']

# Create a new DataFrame with only the selected columns
business_data_filtered = business_data_dropped_na[columns_of_interest]

# Display the first few rows of the filtered dataset
business_data_filtered.head()
```

Not all columns in a dataset are necessarily useful for every analysis. In this step, we extract the most relevant columns for our specific analysis: business names, types, dates of issuance and expiration, location details, and the number of employees. By focusing on the most important columns, we streamline our dataset for further analysis.

### Cell 5: Convert 'IssuedDate' and 'ExpiredDate' to datetime format

```python
# Convert 'IssuedDate' and 'ExpiredDate' to datetime format
business_data_filtered['IssuedDate'] = pd.to_datetime(business_data_filtered['IssuedDate'])
business_data_filtered['ExpiredDate'] = pd.to_datetime(business_data_filtered['ExpiredDate'])

# Check if the conversion was successful
business_data_filtered.dtypes
```

For proper time series analysis and other operations, it is crucial to convert date columns to the correct `datetime` format. This allows us to perform date-based filtering, resampling, and trend analysis. The `pd.to_datetime()` function converts the strings in the date columns to datetime objects that can be manipulated in various ways.

### Cell 6: Summary statistics for numerical columns

```python
# Generate summary statistics for numerical columns
business_data_filtered.describe()
```

In this step, we generate summary statistics for the numerical columns in the dataset. This includes measures like the mean, standard deviation, minimum, and maximum values. Summary statistics provide a quick overview of the data and can help identify potential outliers or trends.

### Cell 7: Count the number of businesses by type

```python
# Count the number of businesses by their type
business_count_by_type = business_data_filtered['BusinessType'].value_counts()

# Display the count of businesses by type
business_count_by_type
```

Understanding the distribution of business types is important for market analysis. For instance, knowing which sectors have the most businesses can provide insights into the local economy and identify areas of growth or decline. The `value_counts()` function helps us determine how many businesses fall into each type category.

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

### Output 8

![](vertopal_5eef928284d744e38e5f94395c91097b/252a028b9220cf0fc23e1fbd300188183562c59b.png)

Visualization is key to understanding data distributions. Here, a bar chart is used to show the number of businesses in each type. This visual representation allows us to quickly see which business types dominate and where there might be gaps or opportunities for growth.

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

### Output 9

![](vertopal_5eef928284d744e38e5f94395c91097b/44c5e4d61d989dd863ebda19cc7eb83b84d25258.png)

In addition to understanding the types of businesses, it is valuable to analyze the size of businesses by the number of employees. This histogram provides insight into the employee distribution across businesses. For example, we can see whether most businesses are small (few employees) or if there are many large employers in the dataset.

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
plt.ylabel('Number of Licenses Issued
```markdown
')
plt.tight_layout()
plt.show()
```

### Output 10

![](vertopal_5eef928284d744e38e5f94395c91097b/1fb77d46a244d4e9a25ff44129381df7bf4427fd.png)

### Explanation of the Time Trend Analysis

Time series analysis is a powerful method used to detect trends, seasonality, and patterns over time. In this case, we are analyzing the issuance of business licenses over a period. By setting the `IssuedDate` as the index and resampling the data by month, we can track how the number of business licenses issued fluctuates over time.

This type of analysis is particularly useful for identifying peaks and troughs in business activity. For instance, if there is a consistent surge in licenses during certain months (e.g., during the holiday season or at the start of a fiscal year), it might indicate when businesses are more likely to start operating or expand. On the other hand, sudden drops may be linked to external factors such as economic downturns, regulatory changes, or unforeseen events like pandemics.

The resampling is done using the `.resample('M')` function, which aggregates the data into monthly bins. This allows us to create a smooth time series that can help in identifying long-term trends rather than focusing on daily fluctuations, which might obscure the bigger picture.

By plotting this trend, business analysts and city planners can make informed decisions about economic planning, policy adjustments, or resource allocation. For example, if a consistent increase in licenses is observed, the city might want to allocate more resources toward business support services, infrastructure, or workforce development programs. Conversely, if a decline is noted, it may prompt investigations into the underlying causes, such as economic challenges or a less favorable business environment.

### Conclusion

In this analysis, we have gone through a systematic process to analyze a dataset containing information about business licenses. By using Python and the `pandas` library, we have:
- Loaded and cleaned the data.
- Investigated missing values and handled them appropriately.
- Narrowed the dataset to relevant columns for analysis.
- Explored business distributions based on type, number of employees, and issuance dates.
- Visualized the distribution of business types and the employee count.
- Conducted a time-series analysis of license issuance to uncover trends over time.

Through this approach, we have demonstrated how effective data analysis techniques can yield valuable insights. These insights can inform strategic decisions, policy-making, and economic planning. Moreover, the tools used here, such as `pandas` for data manipulation and `matplotlib` for visualization, are essential for any data scientist or business analyst working with real-world datasets.

### Potential Future Work

1. **Predictive Modeling**: Based on the time-series data, predictive models (such as ARIMA or Prophet) could be used to forecast future trends in business license issuance. This would enable proactive planning and resource allocation.
2. **Correlation Analysis**: Further analysis could explore correlations between business success and factors such as location (city or local area), type of business, or the number of employees. This could provide deeper insights into what makes businesses more likely to thrive in certain areas or sectors.
3. **Geospatial Analysis**: By using the geographical data (latitude and longitude), we could map business density across the region, identifying hotspots and underdeveloped areas. Geospatial tools such as `GeoPandas` could be integrated to perform such an analysis.

Incorporating these additional analyses would further enhance our understanding of the business landscape, enabling more data-driven decisions to support economic growth and development.

