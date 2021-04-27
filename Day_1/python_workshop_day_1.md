# Python Workshop - Day 1

**Date**: 23 April 2021

**Instructor**: Andres Patrignani | Soil Water Processes | Department of Agronomy | Kansas State University


Workshop organized by the Agronomy Graduate Students Association


### Exercise description

To learn the basic data analysis workflow we will use a dataset of weather data obtained from the Kansas Mesonet. After loading and replacing missing data, our goal is to define the start and end of the winter wheat growing season and compute the cumulative thermal units. The last step consists of implementing a simple model for estimating the potential leaf area index and grain yield based on the computed thermal units. In this tutorial you will learn how to:

- load tabular data
- replace missing values
- select data between a specific date range
- plot time series
- define and use python functions


## Useful commands

- `cmd + Enter` Evaluates the code in the current cell

- `shift + Enter` Evaluates the code in the current cell and moves to a new cell

- `cmd + s` Save current notebook

- Use the `Tab` key after typing few letters of a variable or function to autocomplete

## Few points for writting clear Python code

Python code was developed with better code readibility in mind.

- Indentation and white space matters

- Comment your code using `#`

- Use descriptive variable names. Usually variables should be named using all lower case. Constants are usually all upper case. No spaces allowed. Use underscores to join words. Feel free to deviate from these conventions if you are following a set of equations from a paper or book and you want to match the notation in the example.


Learn more about Python coding conventions at: 'https://www.python.org/dev/peps/pep-0008/'

## Basic computation example

A simple example to highlight few concepts mentioned above.


```python
soil_temperature_F = 56.0 # degrees Fahrenheit
soil_temperature_C = (soil_temperature_F-32) * 5/9 # Celsius

print('Hello world, today the soil temperature in Manhattan, KS is', round(soil_temperature_C, 1), 'Celsius')

```

    Hello world, today the soil temperature in Manhattan, KS is 13.3 Celsius



```python
# Show all the varaibles defined so far
%whos
```

    Variable             Type     Data/Info
    ---------------------------------------
    soil_temperature_C   float    13.333333333333334
    soil_temperature_F   float    56.0


## Importing modules

Python relies on modules containing more Python code that provide new utilities. Think of the basic language as a toolbox with only a few tools (e.g. a screwdrive, a hammer, and a wrench) and think of modules as an additional kit of tools (e.g. a new set of wrenches of different sizes) that will allow you to do something very specific without having to write a large amount of code. In other words, modules were written by other programmers and that code can now be used to resolve a specific task.

The Anaconda package comes with hundreds of pre-installed modules. You can also install other modules available in the web that are not included with Anaconda.

A common convention is to use notation similar tot hat in the official documentation of each module.


```python
# Import modules
import numpy as np
import pandas as pd
from bokeh.plotting import figure, show, output_notebook
from bokeh.layouts import gridplot
output_notebook() # This ensures that figures display in the notebook
```



<div class="bk-root">
    <a href="https://bokeh.org" target="_blank" class="bk-logo bk-logo-small bk-logo-notebook"></a>
    <span id="1001">Loading BokehJS ...</span>
</div>




## Load tabular data


```python
# Load data
#df = pd.read_csv('ashland_bottoms_2019_2020.csv')
URL = 'https://raw.githubusercontent.com/soilwater/python-workshop/main/Day_1/ashland_bottoms_2019_2020.csv'
df = pd.read_csv(URL, comment='#')
```


```python
# Check loaded data
df.head(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TIMESTAMP</th>
      <th>PRESSUREAVG</th>
      <th>TEMP2MMIN</th>
      <th>TEMP2MMAX</th>
      <th>RELHUM2MMAX</th>
      <th>RELHUM2MMIN</th>
      <th>PRECIP</th>
      <th>SR</th>
      <th>WSPD2MAVG</th>
      <th>WDIR2M</th>
      <th>SOILTMP5AVG</th>
      <th>VWC5CM</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1/1/2019</td>
      <td>97.27</td>
      <td>-9.77</td>
      <td>2.76</td>
      <td>90.74</td>
      <td>67.15</td>
      <td>0.0</td>
      <td>2.14</td>
      <td>3.62</td>
      <td>273.20</td>
      <td>-0.14</td>
      <td>0.3034</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1/2/2019</td>
      <td>99.05</td>
      <td>-11.67</td>
      <td>-7.85</td>
      <td>77.15</td>
      <td>62.65</td>
      <td>0.0</td>
      <td>3.74</td>
      <td>2.82</td>
      <td>299.05</td>
      <td>-1.27</td>
      <td>0.1902</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1/3/2019</td>
      <td>98.33</td>
      <td>-7.85</td>
      <td>3.03</td>
      <td>86.36</td>
      <td>35.25</td>
      <td>0.0</td>
      <td>11.57</td>
      <td>2.85</td>
      <td>197.42</td>
      <td>-1.03</td>
      <td>0.1744</td>
    </tr>
  </tbody>
</table>
</div>




```python
type(df)
```




    pandas.core.frame.DataFrame



## Convert timestamps to datetime format

`%d` = day represented as a one or two-digit number

`%m` = month represented as a one or two-digit number

`%Y` = year represented as a four-digit number (use `%y` for a two-digit representation)



```python
# Convert dates from string to datetime format
df['TIMESTAMP'] = pd.to_datetime(df['TIMESTAMP'], format='%m/%d/%Y')

# Check our work
df.head(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TIMESTAMP</th>
      <th>PRESSUREAVG</th>
      <th>TEMP2MMIN</th>
      <th>TEMP2MMAX</th>
      <th>RELHUM2MMAX</th>
      <th>RELHUM2MMIN</th>
      <th>PRECIP</th>
      <th>SR</th>
      <th>WSPD2MAVG</th>
      <th>WDIR2M</th>
      <th>SOILTMP5AVG</th>
      <th>VWC5CM</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2019-01-01</td>
      <td>97.27</td>
      <td>-9.77</td>
      <td>2.76</td>
      <td>90.74</td>
      <td>67.15</td>
      <td>0.0</td>
      <td>2.14</td>
      <td>3.62</td>
      <td>273.20</td>
      <td>-0.14</td>
      <td>0.3034</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2019-01-02</td>
      <td>99.05</td>
      <td>-11.67</td>
      <td>-7.85</td>
      <td>77.15</td>
      <td>62.65</td>
      <td>0.0</td>
      <td>3.74</td>
      <td>2.82</td>
      <td>299.05</td>
      <td>-1.27</td>
      <td>0.1902</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2019-01-03</td>
      <td>98.33</td>
      <td>-7.85</td>
      <td>3.03</td>
      <td>86.36</td>
      <td>35.25</td>
      <td>0.0</td>
      <td>11.57</td>
      <td>2.85</td>
      <td>197.42</td>
      <td>-1.03</td>
      <td>0.1744</td>
    </tr>
  </tbody>
</table>
</div>



>In the previous step we overwrote the column `df['TIMESTAMP']` that was a string format (this is how it was initially imported by Pandas) to another version of itself in `datetime` format. In other words, because of there are many different ways of representing dates, we need to indicate the Python interpreter what each part of the string represents.

## Find missing values


```python
# Check for entries denoted as missing values
df.isna()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TIMESTAMP</th>
      <th>PRESSUREAVG</th>
      <th>TEMP2MMIN</th>
      <th>TEMP2MMAX</th>
      <th>RELHUM2MMAX</th>
      <th>RELHUM2MMIN</th>
      <th>PRECIP</th>
      <th>SR</th>
      <th>WSPD2MAVG</th>
      <th>WDIR2M</th>
      <th>SOILTMP5AVG</th>
      <th>VWC5CM</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>726</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>727</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>728</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>729</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>730</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
<p>731 rows × 12 columns</p>
</div>




```python
# Summary of columns with missing values
df.isna().sum()
```




    TIMESTAMP       0
    PRESSUREAVG     3
    TEMP2MMIN       4
    TEMP2MMAX       4
    RELHUM2MMAX     4
    RELHUM2MMIN     4
    PRECIP          3
    SR              3
    WSPD2MAVG       3
    WDIR2M          3
    SOILTMP5AVG     3
    VWC5CM         10
    dtype: int64



## Replace missing values


```python
# Replace missing records for each variable separately 
# to gain more control of the interpolation method
df['TEMP2MMIN'].interpolate(method='linear', limit_area='inside', inplace=True)
df['TEMP2MMAX'].interpolate(method='pchip', limit_area='inside', inplace=True)
```


```python
# Compute the sum of all missing values (represented as NaN) for each variable of the DataFrame
df.isna().sum()
```




    TIMESTAMP       0
    PRESSUREAVG     3
    TEMP2MMIN       0
    TEMP2MMAX       0
    RELHUM2MMAX     4
    RELHUM2MMIN     4
    PRECIP          3
    SR              3
    WSPD2MAVG       3
    WDIR2M          3
    SOILTMP5AVG     3
    VWC5CM         10
    dtype: int64



## Visualize weather data

It's always a good idea to visualize your dataset (or at least the varaibles in question) before you get too far. Figures can reveal anomalies, values out of range, and gaps in the weather record that we failed to account with our previous code.

>Tip: Always plot your data, particularly during code development.


```python
# Create a figure to visualize temperature data
f1 = figure(x_axis_type='datetime', plot_width=800, plot_height=300, title='Ashland Bottoms, KS')
f1.line(df['TIMESTAMP'], df['TEMP2MMIN'], name='Tmin', color='navy')
f1.line(df['TIMESTAMP'], df['TEMP2MMAX'], name='Tmax', color='tomato')
f1.yaxis.axis_label = 'Minimum Air Temperature (Celsius)'
show(f1)
```








<div class="bk-root" id="c380776b-ab44-42c8-bda1-61544f185983" data-root-id="1002"></div>





Learn more about Bokeh plotting configuration and options:

- [Basic charts](https://docs.bokeh.org/en/latest/docs/user_guide/plotting.html)
- [Figure options](https://docs.bokeh.org/en/latest/docs/user_guide/tools.html)
- [Layouts](https://docs.bokeh.org/en/latest/docs/user_guide/layout.html)
- [Annotations](https://docs.bokeh.org/en/latest/docs/user_guide/annotations.html)

## Define growing season period


```python
# Define the start and end of the growing season for winter wheat in central Kansas
planting_date = pd.to_datetime('15-Oct-2019', format='%d-%b-%Y')
harvest_date = pd.to_datetime('1-Jun-2020', format='%d-%b-%Y')
growing_season_duration = harvest_date - planting_date

print('Planting date:', planting_date)
print('Harvest date:', harvest_date)
print('Growing season duration:', growing_season_duration.days + 1) # Add one day to include the last day

```

    Planting date: 2019-10-15 00:00:00
    Harvest date: 2020-06-01 00:00:00
    Growing season duration: 231


## Select weather data for the growing season

Our dataset set spans two years (i.e. 2019 and 2020), but we are only interested in computing thermal units for the duration of the growing season.


```python
# First, select rows that belong to the growing season
idx_season = (df['TIMESTAMP'] >= planting_date) & (df['TIMESTAMP'] <= harvest_date)
print(idx_season) # Use print(idx_season[280:295]) to see the transition
```

    0      False
    1      False
    2      False
    3      False
    4      False
           ...  
    726    False
    727    False
    728    False
    729    False
    730    False
    Name: TIMESTAMP, Length: 731, dtype: bool



```python
# Now that we have the boolean, we can use to select the rows within the growing season
# We will create another dataframe

df_season = df.loc[idx_season,:] # Alternatively you can do df_season = df[idx_season]
df_season.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TIMESTAMP</th>
      <th>PRESSUREAVG</th>
      <th>TEMP2MMIN</th>
      <th>TEMP2MMAX</th>
      <th>RELHUM2MMAX</th>
      <th>RELHUM2MMIN</th>
      <th>PRECIP</th>
      <th>SR</th>
      <th>WSPD2MAVG</th>
      <th>WDIR2M</th>
      <th>SOILTMP5AVG</th>
      <th>VWC5CM</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>287</th>
      <td>2019-10-15</td>
      <td>97.47</td>
      <td>1.66</td>
      <td>25.02</td>
      <td>99.35</td>
      <td>33.92</td>
      <td>0.00</td>
      <td>18.64</td>
      <td>1.64</td>
      <td>143.85</td>
      <td>13.25</td>
      <td>0.42474</td>
    </tr>
    <tr>
      <th>288</th>
      <td>2019-10-16</td>
      <td>97.83</td>
      <td>5.05</td>
      <td>17.89</td>
      <td>99.76</td>
      <td>25.41</td>
      <td>0.00</td>
      <td>18.10</td>
      <td>2.88</td>
      <td>299.71</td>
      <td>13.80</td>
      <td>0.42305</td>
    </tr>
    <tr>
      <th>289</th>
      <td>2019-10-17</td>
      <td>98.32</td>
      <td>2.66</td>
      <td>15.85</td>
      <td>92.79</td>
      <td>37.50</td>
      <td>0.00</td>
      <td>18.09</td>
      <td>1.32</td>
      <td>238.41</td>
      <td>12.57</td>
      <td>0.41737</td>
    </tr>
    <tr>
      <th>290</th>
      <td>2019-10-18</td>
      <td>97.39</td>
      <td>3.02</td>
      <td>24.75</td>
      <td>96.25</td>
      <td>26.42</td>
      <td>0.00</td>
      <td>18.03</td>
      <td>1.94</td>
      <td>152.22</td>
      <td>13.15</td>
      <td>0.41356</td>
    </tr>
    <tr>
      <th>291</th>
      <td>2019-10-19</td>
      <td>96.67</td>
      <td>12.25</td>
      <td>24.88</td>
      <td>91.98</td>
      <td>35.83</td>
      <td>1.27</td>
      <td>17.54</td>
      <td>3.32</td>
      <td>141.37</td>
      <td>14.43</td>
      <td>0.40949</td>
    </tr>
  </tbody>
</table>
</div>




```python
# The last step, although not necessary, is to reset the index
df_season.reset_index(inplace=True)
df_season.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>index</th>
      <th>TIMESTAMP</th>
      <th>PRESSUREAVG</th>
      <th>TEMP2MMIN</th>
      <th>TEMP2MMAX</th>
      <th>RELHUM2MMAX</th>
      <th>RELHUM2MMIN</th>
      <th>PRECIP</th>
      <th>SR</th>
      <th>WSPD2MAVG</th>
      <th>WDIR2M</th>
      <th>SOILTMP5AVG</th>
      <th>VWC5CM</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>287</td>
      <td>2019-10-15</td>
      <td>97.47</td>
      <td>1.66</td>
      <td>25.02</td>
      <td>99.35</td>
      <td>33.92</td>
      <td>0.00</td>
      <td>18.64</td>
      <td>1.64</td>
      <td>143.85</td>
      <td>13.25</td>
      <td>0.42474</td>
    </tr>
    <tr>
      <th>1</th>
      <td>288</td>
      <td>2019-10-16</td>
      <td>97.83</td>
      <td>5.05</td>
      <td>17.89</td>
      <td>99.76</td>
      <td>25.41</td>
      <td>0.00</td>
      <td>18.10</td>
      <td>2.88</td>
      <td>299.71</td>
      <td>13.80</td>
      <td>0.42305</td>
    </tr>
    <tr>
      <th>2</th>
      <td>289</td>
      <td>2019-10-17</td>
      <td>98.32</td>
      <td>2.66</td>
      <td>15.85</td>
      <td>92.79</td>
      <td>37.50</td>
      <td>0.00</td>
      <td>18.09</td>
      <td>1.32</td>
      <td>238.41</td>
      <td>12.57</td>
      <td>0.41737</td>
    </tr>
    <tr>
      <th>3</th>
      <td>290</td>
      <td>2019-10-18</td>
      <td>97.39</td>
      <td>3.02</td>
      <td>24.75</td>
      <td>96.25</td>
      <td>26.42</td>
      <td>0.00</td>
      <td>18.03</td>
      <td>1.94</td>
      <td>152.22</td>
      <td>13.15</td>
      <td>0.41356</td>
    </tr>
    <tr>
      <th>4</th>
      <td>291</td>
      <td>2019-10-19</td>
      <td>96.67</td>
      <td>12.25</td>
      <td>24.88</td>
      <td>91.98</td>
      <td>35.83</td>
      <td>1.27</td>
      <td>17.54</td>
      <td>3.32</td>
      <td>141.37</td>
      <td>14.43</td>
      <td>0.40949</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Plot air temperature for growing season only (NOTE: We are using an alternative plotting notation in Bokeh)
f2 = figure(x_axis_type='datetime', plot_width=800, plot_height=300, title='Ashland Bottoms, KS | 2019-2020 Wheat growing season')
f2.line(source=df_season, x='TIMESTAMP', y='TEMP2MMIN', name='Tmin', color='navy')
f2.line(source=df_season, x='TIMESTAMP', y='TEMP2MMAX', name='Tmax', color='tomato')
f2.yaxis.axis_label = 'Minimum Air Temperature (Celsius)'
show(f2)
```








<div class="bk-root" id="523bd639-3663-4f4e-81eb-8506e239b79b" data-root-id="1177"></div>






```python
# Find day of highest air temperature

T_max = df_season['TEMP2MMAX'].max()
idx_T_max = df_season['TEMP2MMAX'].idxmax()
T_max_day = df_season.loc[idx_T_max, 'TIMESTAMP'] # Add .date() to only display the date portion of the timestamp

print('The maximum air temperature was', T_max, 'Celsius on', T_max_day)


# Find day of lowest air temperature

T_min = df_season['TEMP2MMIN'].min()
idx_T_min = df_season['TEMP2MMAX'].idxmin()
T_min_day = df_season.loc[idx_T_min, 'TIMESTAMP']

print('The minimum air temperature was', T_min, 'Celsius on', T_min_day)
```

    The maximum air temperature was 30.2 Celsius on 2020-04-08 00:00:00
    The minimum air temperature was -16.75 Celsius on 2020-02-14 00:00:00


## Compute thermal units

Since there are few ways of computing thermal units, we will formally define our method(McMaster and Wilhelm, 1997):

$$ if \ T_{avg} < T_{base}, \ then \ TU = T_{base} $$

$$ if \ T_{avg} > T_{upper}, \ then \ TU = T_{upper} $$

$$ if \ T_{avg} >= T_{base} and \ T_{avg} <= T_{upper}, \ then \ \ TU = T_{avg} - T_{base}$$



```python
# Define winter wheat cardinal temperatures
T_base = 0   # Celsius
T_upper = 35 # Celsius
```


```python
# Compute average temperature
T_avg = df_season['TEMP2MMIN'] + df_season['TEMP2MMAX']

# Insert Tavg into DataFrame after Tmax using same notation as other temperature variables
df_season.insert(5, 'TEMP2MAVG', T_avg)

# Check our work
df_season.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>index</th>
      <th>TIMESTAMP</th>
      <th>PRESSUREAVG</th>
      <th>TEMP2MMIN</th>
      <th>TEMP2MMAX</th>
      <th>TEMP2MAVG</th>
      <th>RELHUM2MMAX</th>
      <th>RELHUM2MMIN</th>
      <th>PRECIP</th>
      <th>SR</th>
      <th>WSPD2MAVG</th>
      <th>WDIR2M</th>
      <th>SOILTMP5AVG</th>
      <th>VWC5CM</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>287</td>
      <td>2019-10-15</td>
      <td>97.47</td>
      <td>1.66</td>
      <td>25.02</td>
      <td>26.68</td>
      <td>99.35</td>
      <td>33.92</td>
      <td>0.00</td>
      <td>18.64</td>
      <td>1.64</td>
      <td>143.85</td>
      <td>13.25</td>
      <td>0.42474</td>
    </tr>
    <tr>
      <th>1</th>
      <td>288</td>
      <td>2019-10-16</td>
      <td>97.83</td>
      <td>5.05</td>
      <td>17.89</td>
      <td>22.94</td>
      <td>99.76</td>
      <td>25.41</td>
      <td>0.00</td>
      <td>18.10</td>
      <td>2.88</td>
      <td>299.71</td>
      <td>13.80</td>
      <td>0.42305</td>
    </tr>
    <tr>
      <th>2</th>
      <td>289</td>
      <td>2019-10-17</td>
      <td>98.32</td>
      <td>2.66</td>
      <td>15.85</td>
      <td>18.51</td>
      <td>92.79</td>
      <td>37.50</td>
      <td>0.00</td>
      <td>18.09</td>
      <td>1.32</td>
      <td>238.41</td>
      <td>12.57</td>
      <td>0.41737</td>
    </tr>
    <tr>
      <th>3</th>
      <td>290</td>
      <td>2019-10-18</td>
      <td>97.39</td>
      <td>3.02</td>
      <td>24.75</td>
      <td>27.77</td>
      <td>96.25</td>
      <td>26.42</td>
      <td>0.00</td>
      <td>18.03</td>
      <td>1.94</td>
      <td>152.22</td>
      <td>13.15</td>
      <td>0.41356</td>
    </tr>
    <tr>
      <th>4</th>
      <td>291</td>
      <td>2019-10-19</td>
      <td>96.67</td>
      <td>12.25</td>
      <td>24.88</td>
      <td>37.13</td>
      <td>91.98</td>
      <td>35.83</td>
      <td>1.27</td>
      <td>17.54</td>
      <td>3.32</td>
      <td>141.37</td>
      <td>14.43</td>
      <td>0.40949</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Now we can compute thermal units
TU_daily = np.minimum(np.maximum(df_season['TEMP2MAVG'], T_base), T_upper)

# Cumulative thermal time
TU_cumulative = TU_daily.cumsum() # To directly insert into DataFrame use df['TU_cumulative'] = TU_daily.cumsum()

# Add the cumulative thermal units to the DataFrame for the growing season (just for completeness, but not really needed)
df_season.insert(len(df.columns), 'TU_cumulative', TU_cumulative)
```


```python
# Plot cumulative thermal units
f3 = figure(x_axis_type='datetime', plot_width=800, plot_height=300, title='2019-2020 Wheat growing season')
f3.line(source=df_season, x='TIMESTAMP', y='TU_cumulative', name='Thermal Units', color='navy')
f3.yaxis.axis_label = 'Thermal Units (C-day)'
show(f3)
```








<div class="bk-root" id="47eb42c1-8f61-4a87-a1cf-12d0cc1becad" data-root-id="1376"></div>






```python
# Find total thermal units for growing season
TU_total = df_season['TU_cumulative'].iloc[-1] 

# Alternatively:
# TU_total = TU_cumulative.values[-1]
# This is possible because we already defined TU_cuulative (which is a Pandas series) 
# and because Pandas values are ultimatedly Numpy arrays.

print('The 2019-2020 winter wheat growing season had a total of', round(TU_total), 'thermal units')
```

    The 2019-2020 winter wheat growing season had a total of 3209 thermal units


## Implement simple crop model

This part consists of three steps:

1. Define model equations
2. Define model parameters and units
3. Compute potential leaf area index and above-ground plant biomass


The model is simple and assumes no water and nutrient limitations:

**Leaf Area Index (LAI)**
The LAI represents the area covered by all leaves in a unit area.

$$ LAI_t = LAI_{max} \Bigg[ \frac{1}{1+e^{-\alpha(T_t - T_1)}} -e^{\beta(T_t-T_2)} \Bigg] $$



**Above-ground plant biomass**

$$ B_t = B_{t-1} + E_b \; E_{imax} \Bigg[ 1 - e^{-K \; LAI_t} \Bigg] PAR_t $$

where the parameter $T_2$ is defined by:

$$ T_2 = \frac{1}{\beta} log[1 + e^{(\alpha \; T_1)}]$$


$AGB$ is above-ground plant dry biomass in $g m^{-2}$
$T$ is cumulative growing degree days

$t$ is time in days

$LAI$ is the leaf area index

$LAI_{max}$ is the maximum leaf area index during the entire growing season.

$PAR$ is the photosynthetically active radiation

$K$ is the coefficient of extiction

$T_1$ is a growth threshold

$E_b$ is the intercepted radiation use efficiency

$E_{imax}$ is the maximal value of intercepted to incident solar radiation 

$\alpha$ and $\beta$ are empirical parameters


```python
# Define model parameters

# Estimate Photosynthetically Active Radiation
PAR = df_season['SR'] * 0.48

# Define model parameters
Eb = 1.85 # g/MJ/m^2
Eimax = 0.95
K = 0.7
LAI_max = 7
T1 = 900 # Thermal units
alpha = 0.005
beta = 0.002
T2 = 1/beta * np.log(1 + np.exp(alpha*T1)) # Thermal units
harvest_index = 0.45 # Approximate harvest index

```


```python
# Compute LAI
LAI = LAI_max * ( 1/(1+np.exp(-alpha*(TU_cumulative-T1))) - np.exp(beta*(TU_cumulative-T2)) )
LAI = np.maximum(LAI, 0)

# Compute biomass
AGB = Eb * Eimax * (1-np.exp(-K*LAI)) * PAR
AGB_cumulative = AGB.cumsum()

# Print estimated total above-ground plant biomass
AGB_total = AGB_cumulative.iloc[-1]
print('Potential above-ground biomass was:', round(AGB_total), 'g/m^2' )

# Print estimated potential grain yield
grain_yield = AGB_total * 10 * harvest_index
print('Potential grain yield was:', round(grain_yield), 'kg/ha')
```

    Potential above-ground biomass was: 1433 g/m^2
    Potential grain yield was: 6450 kg/ha



```python
# Plot LAI and biomass
f4 = figure(x_axis_type='datetime', title='2019-2020 Wheat growing season')
f4.line(df_season['TIMESTAMP'], LAI, name='Leaf Area Index', color='green')
f4.yaxis.axis_label = 'Leaf Area Index'

f5 = figure(x_axis_type='datetime', title='2019-2020 Wheat growing season')
f5.line(df_season['TIMESTAMP'], AGB_cumulative, name='Leaf Area Index', color='tomato')
f5.yaxis.axis_label = 'Above-ground plant biomass (g/m^2)'

grid = gridplot([[f4, f5]], plot_width=500, plot_height=300)

show(grid)

```








<div class="bk-root" id="4d1a794b-15b6-4502-adff-ad18118ba2a5" data-root-id="1771"></div>





## References

Baret, F. 1986. Contribution au suivi radiometriqué de cultures de céréales. Ph.D. Dissertation, Université Orsay, Orsay, France.

McMaster, G.S. and Wilhelm, W.W., 1997. Growing degree-days: one equation, two interpretations. Agricultural and forest meteorology, 87(4), pp.291-300.

Wallach, D., Makowski, D., and Jones, J.W. 2006. Working with dynamic crop models. Chapter 3: UNcertainty and sensitivity analysis for crop models by Monod, H., Naud, C., and Makowski, D.

Patrignani, A., Knapp, M., Redmond, C. and Santos, E., 2020. Technical overview of the Kansas Mesonet. Journal of Atmospheric and Oceanic Technology, 37(12), pp.2167-2183.

Van Rossum, G., Warsaw, B. and Coghlan, N., 2001. PEP 8: style guide for Python code. Python Software Foundation. Link: https://www.python.org/dev/peps/pep-0008/ Accessed: 25-April-2021.
