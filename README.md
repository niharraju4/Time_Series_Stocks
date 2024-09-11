
---

# Stock Analysis Project

## Table of Contents
1. [Introduction](#introduction)
2. [Data Collection](#data-collection)
3. [Data Preprocessing](#data-preprocessing)
4. [Exploratory Data Analysis](#exploratory-data-analysis)
5. [Moving Averages](#moving-averages)
6. [Daily Return Analysis](#daily-return-analysis)
7. [Resampling Analysis](#resampling-analysis)
8. [Correlation Analysis](#correlation-analysis)
9. [Correlation Analysis between Closing Price and Daily Return](#correlation-analysis-between-closing-price-and-daily-return)
10. [Results](#results)
11. [Conclusion](#conclusion)

---

## Introduction
This project aims to analyze the stock prices of major tech companies over a 5-year period. The analysis includes data preprocessing, exploratory data analysis, moving averages, daily return analysis, resampling analysis, and correlation analysis.

## Data Collection
The data was collected from CSV files containing stock prices for Apple (AAPL), Amazon (AMZN), Google (GOOG), and Monster Beverage (MNST).

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import glob

# To read all the data files
glob.glob(r'N:\Personal_Projects\Machine_learning_projects\S&P_resources\individual_stocks_5yr/*csv')
len(glob.glob(r'N:\Personal_Projects\Machine_learning_projects\S&P_resources\individual_stocks_5yr/*csv'))
```

## Data Preprocessing
The data from multiple CSV files was combined into a single DataFrame.

```python
company_list = [
    r'N:\\Personal_Projects\\Machine_learning_projects\\S&P_resources\\individual_stocks_5yr\\AAPL_data.csv',
    r'N:\\Personal_Projects\\Machine_learning_projects\\S&P_resources\\individual_stocks_5yr\\AMZN_data.csv',
    r'N:\\Personal_Projects\\Machine_learning_projects\\S&P_resources\\individual_stocks_5yr\\GOOG_data.csv',
    r'N:\\Personal_Projects\\Machine_learning_projects\\S&P_resources\\individual_stocks_5yr\\MNST_data.csv'
]
all_data = pd.DataFrame()

for file in company_list:
    current_df = pd.read_csv(file)
    all_data = pd.concat([all_data, current_df], ignore_index=True)

all_data.shape
all_data.head()
all_data.tail()
all_data['Name'].unique()
all_data.isnull()
all_data.isnull().sum()
all_data.dtypes
pd.to_datetime(all_data['date'])
tech_list = all_data['Name'].unique()
tech_list
```

## Exploratory Data Analysis
Visualizations of the closing prices for each company were created.

```python
plt.figure(figsize=(20, 12))

for index, company in enumerate(tech_list, 1):
    plt.subplot(2, 2, index)
    filter1 = all_data['Name'] == company
    df = all_data[filter1]
    plt.plot(df['date'], df['close'])
    plt.title(company)
```

## Moving Averages
Moving averages for different windows were calculated and plotted.

```python
new_data = all_data.copy()
ma_day = [10, 20, 50, 100, 200]

for ma in ma_day:
    new_data['close_' + str(ma)] = new_data['close'].rolling(ma).mean()

new_data.set_index('date', inplace=True)
new_data.columns

plt.figure(figsize=(20, 12))

for index, company in enumerate(tech_list, 1):
    plt.subplot(2, 2, index)
    filter1 = new_data['Name'] == company
    df = new_data[filter1]
    df[['close_10', 'close_20', 'close_50']].plot(ax=plt.gca())
    plt.title(company)
```

## Daily Return Analysis
The daily return for Apple's stock was calculated and visualized.

```python
apple = pd.read_csv(r'N:\\Personal_Projects\\Machine_learning_projects\\S&P_resources\\individual_stocks_5yr\\AAPL_data.csv')
apple['Daily return(in %)'] = apple['close'].pct_change() * 100

import plotly.express as px
px.line(apple, x="date", y="Daily return(in %)")
```

## Resampling Analysis
Resampling analysis was performed on the closing prices of Apple's stock.

```python
apple['date'] = pd.to_datetime(apple['date'])
apple.set_index('date', inplace=True)

apple['close'].resample('M').mean().plot()
apple['close'].resample('Y').mean().plot()
apple['close'].resample('Q').mean().plot()
```

## Correlation Analysis
Correlation analysis was performed to check if the closing prices of tech companies are correlated.

```python
company_list = [
    r'N:\\Personal_Projects\\Machine_learning_projects\\S&P_resources\\individual_stocks_5yr\\AAPL_data.csv',
    r'N:\\Personal_Projects\\Machine_learning_projects\\S&P_resources\\individual_stocks_5yr\\AMZN_data.csv',
    r'N:\\Personal_Projects\\Machine_learning_projects\\S&P_resources\\individual_stocks_5yr\\GOOG_data.csv',
    r'N:\\Personal_Projects\\Machine_learning_projects\\S&P_resources\\individual_stocks_5yr\\MNST_data.csv'
]

appl = pd.read_csv(company_list[0])
amz = pd.read_csv(company_list[1])
goog = pd.read_csv(company_list[2])
msft = pd.read_csv(company_list[3])

closing_price = pd.DataFrame()
closing_price['apple_close'] = appl['close']
closing_price['amazon_close'] = amz['close']
closing_price['google_close'] = goog['close']
closing_price['microsoft_close'] = msft['close']

sns.pairplot(closing_price)
sns.heatmap(closing_price.corr(), annot=True)
```

## Correlation Analysis between Closing Price and Daily Return
Correlation analysis was performed between the closing prices and the daily returns of the tech companies.

```python
for col in closing_price.columns:
    closing_price[col + 'pct_change'] = (closing_price[col] - closing_price[col].shift(1)) / closing_price[col].shift(1) * 100

closing_price.columns
closing_price_pct = closing_price[['apple_closepct_change', 'amazon_closepct_change', 'google_closepct_change', 'microsoft_closepct_change']]

g = sns.PairGrid(data=closing_price_pct)
g.map_diag(sns.histplot)
g.map_lower(sns.scatterplot)
g.map_upper(sns.kdeplot)

closing_price_pct.corr()
```

## Results

### Exploratory Data Analysis
- Visualizations of the closing prices for each company showed the trends over the 5-year period.

### Moving Averages
- Moving averages for different windows (10, 20, 50, 100, 200 days) were calculated and plotted. These visualizations helped in understanding the smoothed trends of the stock prices.

### Daily Return Analysis
- The daily return for Apple's stock was calculated and visualized. This analysis showed the percentage change in the stock price on a daily basis.

### Resampling Analysis
- Resampling analysis was performed on the closing prices of Apple's stock to understand the monthly, yearly, and quarterly trends.

### Correlation Analysis
- Correlation analysis showed that the closing prices of the tech companies were positively correlated. The heatmap and pairplot visualizations provided insights into the relationships between the stock prices.

### Correlation Analysis between Closing Price and Daily Return
- Correlation analysis between the closing prices and the daily returns of the tech companies showed the relationships between the price changes and the returns. The pairplot and heatmap visualizations provided insights into these relationships.

## Conclusion
This project provided a comprehensive analysis of the stock prices of major tech companies. The analysis included data preprocessing, exploratory data analysis, moving averages, daily return analysis, resampling analysis, and correlation analysis. The results showed the trends and correlations among the stock prices of these companies.

---

**Author: Nihar Raju**
