# Greenhouse-sensor-analytics

## Table of Content

## Project Overview
This data analysis project is focused on monitoring environmental trends in a strawberry greenhouse using sensor data. The project identifies patterns in temperature, humidity, and infrared temperature to detect early signs of plant stress and optimize growing conditions.

## Data Source
This data is a " Strawberry_Greenhouse.csv " file containing detailed information about the environmental trends in the strawberry greenhouse located at Chichester, United kingdom.

## Tools
- Python : this tool was used for

 - Data preparation
 - Data cleaning.    
 - The entire data analysis (Exploratory Data Analysis)   
 - Data visualization

 ## Data Cleaning
I performed the following task in the data preparation phase;

 - Data inspection
 - Checking and handling duplicate values
 - Fixing column name typos
 - Handling wrong DateTime format
 - Removal of Outliers using the Zcore, IQR method (Please check the Report for more information)
 - Data formatting

## Exploratory Data Analysis
EDA involved exploring the greenhouse data to answer key questions, such as;
- How do temperature and humidity levels vary over time (daily, weekly, monthly), and are there identifiable seasonal or diurnal patterns?
- What is the average temperature, humidity, and irTemperature across the monitored environment, and how do these variables correlate?
- How does irTemperature vary across different times of day, and can it serve as a proxy for detecting plant heat stress or water deficiency?

## Data analysis 

``` Python
#importing libraries
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
import numpy as np
from datetime import datetime
from scipy import stats

#importing the dataset for analysis
df = pd.read_csv("Strawberry_Greenhouse.csv")

# Fixing column name typos
df.rename(columns={
    'temparature': 'temperature',
    'irTemparature': 'irTemperature'
}, inplace=True)

def parse_uk_datetime(dt_str):
    """Parse UK format dates with flexible handling of:
    - With/without leading zeros
    - With/without seconds
    - Various separators (/ or -)
    """
    dt_str = str(dt_str).strip()
    
    # Standardize separators
    dt_str = dt_str.replace('-', '/').replace('.', '/')
    
    # Try formats with seconds first
    try:
        return datetime.strptime(dt_str, '%d/%m/%Y %H:%M:%S')
    except ValueError:
        pass
    
    try:
        return datetime.strptime(dt_str, '%d/%m/%y %H:%M:%S')
    except ValueError:
        pass
    
    # Then try without seconds
    try:
        return datetime.strptime(dt_str, '%d/%m/%Y %H:%M')
    except ValueError:
        pass
    
    try:
        return datetime.strptime(dt_str, '%d/%m/%y %H:%M')
    except ValueError:
        pass
    
    # Handle single digit day/month without separators (e.g., 2/5/2023)
    try:
        return datetime.strptime(dt_str, '%d/%m/%Y %H:%M')
    except ValueError:
        pass
    
    return pd.NaT

# Apply the parser
df['DateTime'] = df['EnqueuedTimeUtc'].apply(parse_uk_datetime)

# Check results
print(f"Failed conversions: {df['DateTime'].isna().sum()}")
print("Date range:", df['DateTime'].min(), df['DateTime'].max())

```

## Data Visualizations
[Visualizations]
![Figure 4a_monthly_average_temperature](https://github.com/user-attachments/assets/146542b6-8e98-4ba4-a73d-d9fcb1df0c8c)
![Figure 4b_monthly_average_humidity](https://github.com/user-attachments/assets/4647023a-1208-49cf-a072-01ba9d97ed46)
![Figure 4c_Average_Temperature_by_time_of_the_day](https://github.com/user-attachments/assets/7706b47c-de90-42a1-9cf2-cb7b2d9ab90d)
![Figure 4d_Average_humidity_by_time_of_the_day](https://github.com/user-attachments/assets/d155fbdc-8bb2-45cd-8af5-e697df87b20a)
![Figure 1a_Temperature_distribution_before_outlier_removal](https://github.com/user-attachments/assets/dbb272af-aeca-4f28-b845-ec795faa2d27)
![Figure 1b_Temperature_distribution_after_outlier_removal](https://github.com/user-attachments/assets/456fa867-d412-4dd6-83c7-b59c4e37febb)
![Figure 1c_Humidity_distribution_before_removal_of_outliers](https://github.com/user-attachments/assets/1ba08a15-a9b9-49d7-b177-b66fba057cb7)
![Figure 1d_Humidity_distribution_after_removal_of_outliers](https://github.com/user-attachments/assets/b02d0064-6d4a-4cfb-b07e-971ccc9e9866)




## Results
- Temporal Patterns:
Visualisation 1(Figure 4a) revealed clear weekly and seasonal patterns in greenhouse temperature and humidity. Temperature peaked in warmer months (August, September), while humidity increased during the early growing season. Diurnal cycles (Figure 4b) showed expected day-night fluctuations, where temperature peaked in the afternoon (12.8°C) and humidity was highest in the morning (81.89%), consistent with dew formation and evaporative cooling.
- Environmental Conditions:
Visualisation 4 (Figures 4f) highlighted that the average greenhouse conditions (12.46°C temperature, 80.09% humidity, and 12.93°C irTemperature) were within the optimal range for strawberry growth (10–25°C, 60–85% RH) [1]. However, the humidity approached levels that may increase the risk of fungal outbreaks (>85%) [3], suggesting the need for ventilation control. The close alignment between irTemperature and ambient temperature also indicated uniform canopy health and minimal heat stress [2].
- Correlation Insights:
Contrary to expectations, the correlation heatmap Visualisation 5 (Figure 4g) showed a moderate positive correlation (r = 0.53) between temperature and humidity, challenging the commonly assumed inverse relationship in greenhouse settings [1]. This suggests a more complex microclimate potentially influenced by irrigation schedules or greenhouse design. Meanwhile, the correlation matrix (Visualisation 6) supported irTemperature as a thermal stress proxy, showing a moderate correlation with ambient temperature (r = 0.55) and a weak link with humidity (r = 0.19), aligning with Hypothesis H2 and confirming temperature as the dominant driver of plant stress.
