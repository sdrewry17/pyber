
# Pyber


```python
#Dependencies
import pandas as pd
import seaborn as sns
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
```


```python
# Assign path
citypath = ("city_data.csv")
ridepath = ("ride_data.csv")
```


```python
# read into dfs
city_df = pd.read_csv(citypath)
ride_df = pd.read_csv(ridepath)
```


```python
city_df.drop_duplicates(subset = "city", inplace = True)
```


```python
#create fare statistics
avg_fare_city = ride_df[['city', 'fare']].groupby("city").mean()
tot_fare_city = ride_df[['city', 'fare']].groupby("city").sum()
count_rides_city = ride_df[['city', 'ride_id']].groupby('city').count()
```


```python
#merge fare statistics into the city DataFrame
analysis_df = pd.merge(city_df, avg_fare_city, left_on = "city", right_index = True)
analysis_df = pd.merge(analysis_df, tot_fare_city, left_on = "city", right_index = True)
analysis_df = pd.merge(analysis_df, count_rides_city, left_on = "city", right_index = True)
analysis_df.rename(columns = {"driver_count": "Total Number of Drivers", "type": "City Type", "fare_x": "Average Fare", "fare_y": "Sum of Fares","ride_id": "Total Number of Rides"}, inplace = True)
analysis_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city</th>
      <th>Total Number of Drivers</th>
      <th>City Type</th>
      <th>Average Fare</th>
      <th>Sum of Fares</th>
      <th>Total Number of Rides</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>21.806429</td>
      <td>610.58</td>
      <td>28</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Nguyenbury</td>
      <td>8</td>
      <td>Urban</td>
      <td>25.899615</td>
      <td>673.39</td>
      <td>26</td>
    </tr>
    <tr>
      <th>2</th>
      <td>East Douglas</td>
      <td>12</td>
      <td>Urban</td>
      <td>26.169091</td>
      <td>575.72</td>
      <td>22</td>
    </tr>
    <tr>
      <th>3</th>
      <td>West Dawnfurt</td>
      <td>34</td>
      <td>Urban</td>
      <td>22.330345</td>
      <td>647.58</td>
      <td>29</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Rodriguezburgh</td>
      <td>52</td>
      <td>Urban</td>
      <td>21.332609</td>
      <td>490.65</td>
      <td>23</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Create three data frames to be easily used in teh plots
urban_analysis_df = analysis_df[analysis_df['City Type'] == "Urban"]
suburban_analysis_df = analysis_df[analysis_df['City Type'] == "Suburban"]
rural_analysis_df = analysis_df[analysis_df['City Type'] == "Rural"]
```


```python
# Set the colors
colors = ['gold', 'skyblue', 'lightcoral']
```


```python
# Create the bubblechart
plt.scatter(x = urban_analysis_df['Total Number of Rides'], y = urban_analysis_df['Average Fare'], 
           s = urban_analysis_df['Total Number of Drivers'] * 10, c = colors[0], label = "Urban")
plt.scatter(x = suburban_analysis_df['Total Number of Rides'], y = suburban_analysis_df['Average Fare'],
           s = suburban_analysis_df['Total Number of Drivers'] * 10, c = colors[1], label = "Suburban")
plt.scatter(x = rural_analysis_df['Total Number of Rides'], y = rural_analysis_df['Average Fare'],
           s = rural_analysis_df['Total Number of Drivers'] * 10, c = colors[2], label = "Rural")
plt.legend()
plt.xlabel("Total Number of Rides")
plt.ylabel("Average Fare")
plt.title("Average Fare vs. Total Number of Rides. Note - size of circle is in relation to number of Drivers")
```




    Text(0.5,1,'Average Fare vs. Total Number of Rides. Note - size of circle is in relation to number of Drivers')




![png](output_9_1.png)



```python
#create separate dataframes to use in the pie charts
fares_city_type = analysis_df[['Sum of Fares', 'City Type']].groupby('City Type').sum()
rides_city_type = analysis_df[['Total Number of Rides', 'City Type']].groupby('City Type').sum()
drivers_city_type = analysis_df[['Total Number of Drivers', 'City Type']].groupby('City Type').sum()
```


```python
# Sort by teh values so that they can be graphed similarly
fares_city_type.sort_values(by = "Sum of Fares", ascending = False)
rides_city_type.sort_values(by = "Total Number of Rides", ascending = False)
drivers_city_type.sort_values(by = "Total Number of Drivers", ascending = False)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Number of Drivers</th>
    </tr>
    <tr>
      <th>City Type</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Urban</th>
      <td>2607</td>
    </tr>
    <tr>
      <th>Suburban</th>
      <td>635</td>
    </tr>
    <tr>
      <th>Rural</th>
      <td>104</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Plot the % of Total Fares by City Type
plt.pie(fares_city_type, colors = colors, labels = fares_city_type.index, autopct = "%1.1f%%", startangle = 90)
plt.axis("equal")
plt.title("% of Total Fares by City Type")
```




    Text(0.5,1,'% of Total Fares by City Type')




![png](output_12_1.png)



```python
#Plot the % of Total Rides by City Type
plt.pie(rides_city_type, colors = colors, labels = rides_city_type.index, autopct = "%1.1f%%", startangle = 90)
plt.axis("equal")
plt.title("% of Total Rides by City Type")
```




    Text(0.5,1,'% of Total Rides by City Type')




![png](output_13_1.png)



```python
#Plot the % of Total Drivers by City Type
plt.pie(drivers_city_type, colors = colors, labels = drivers_city_type.index, autopct = "%1.1f%%", startangle = 90)
plt.axis("equal")
plt.title("% of Total Drivers by City Type")
```




    Text(0.5,1,'% of Total Drivers by City Type')




![png](output_14_1.png)

