## Predict Power Generation of Solar Panels
### GMiS CAHSI Data Analytics Hackathon 2020 - Team 14: Mask-araid
----
__Question__: What will be the generated power voltage from a solar panel at a given time in the future given the weather conditions?

__Software Used__: Jupyter Lab

__Programming Language__: Python 

### Research
First, let's do some research about solar panels. According to [1876 Energy](https://www.1876energy.com/5-factors-that-affect-solar-panel-efficiency/) and [Trace Software](https://www.trace-software.com/blog/which-are-the-factors-that-affect-solar-panels-efficiency/), the highest contributing factors to solar panels are temperature, energy conversion efficiency (power), shade, solar radiation, and location (longitude and latitude). Additionally, solar panels work more efficiently in cold temperatures, allowing the panel to produce more voltage and more electricity. Rain and snow have no effect on solar panels however cloudy days and humidity can slow down production.

### Step I: EDA
First, we will perform some EDA so that we can get a feel for the data.


```python
import pandas as pd
```


```python
data_set = pd.read_csv("cahsi_data_2020/D1.csv")
```


```python
data_set.head(100)
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
      <th>weather_datetime</th>
      <th>solar_datetime</th>
      <th>solarRadiation</th>
      <th>uvHigh</th>
      <th>winddirAvg</th>
      <th>humidityHigh</th>
      <th>humidityLow</th>
      <th>humidityAvg</th>
      <th>qcStatus</th>
      <th>tempHigh</th>
      <th>...</th>
      <th>windchillAvg</th>
      <th>heatindexHigh</th>
      <th>heatindexLow</th>
      <th>heatindexAvg</th>
      <th>pressureMax</th>
      <th>pressureMin</th>
      <th>pressureTrend</th>
      <th>precipRate</th>
      <th>precipTotal</th>
      <th>DC</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2020-02-07 14:29:00</td>
      <td>2020-02-07 14:29:1</td>
      <td>627.70</td>
      <td>7.0</td>
      <td>195</td>
      <td>24</td>
      <td>24</td>
      <td>24</td>
      <td>-1</td>
      <td>65</td>
      <td>...</td>
      <td>65</td>
      <td>65</td>
      <td>65</td>
      <td>65</td>
      <td>30.06</td>
      <td>30.05</td>
      <td>0.60</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>42.036</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2020-02-07 14:34:00</td>
      <td>2020-02-07 14:34:1</td>
      <td>617.31</td>
      <td>7.0</td>
      <td>129</td>
      <td>24</td>
      <td>23</td>
      <td>23</td>
      <td>-1</td>
      <td>68</td>
      <td>...</td>
      <td>67</td>
      <td>68</td>
      <td>66</td>
      <td>67</td>
      <td>30.06</td>
      <td>30.05</td>
      <td>-0.15</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>42.126</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2020-02-07 14:39:00</td>
      <td>2020-02-07 14:39:1</td>
      <td>608.13</td>
      <td>6.0</td>
      <td>108</td>
      <td>24</td>
      <td>23</td>
      <td>23</td>
      <td>-1</td>
      <td>68</td>
      <td>...</td>
      <td>67</td>
      <td>68</td>
      <td>67</td>
      <td>67</td>
      <td>30.06</td>
      <td>30.05</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>42.264</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2020-02-07 14:44:00</td>
      <td>2020-02-07 14:44:1</td>
      <td>582.57</td>
      <td>6.0</td>
      <td>87</td>
      <td>25</td>
      <td>24</td>
      <td>24</td>
      <td>-1</td>
      <td>67</td>
      <td>...</td>
      <td>66</td>
      <td>67</td>
      <td>66</td>
      <td>66</td>
      <td>30.06</td>
      <td>30.05</td>
      <td>-0.15</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>42.204</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2020-02-07 14:49:00</td>
      <td>2020-02-07 14:49:1</td>
      <td>571.67</td>
      <td>6.0</td>
      <td>38</td>
      <td>24</td>
      <td>24</td>
      <td>24</td>
      <td>-1</td>
      <td>66</td>
      <td>...</td>
      <td>66</td>
      <td>66</td>
      <td>66</td>
      <td>66</td>
      <td>30.05</td>
      <td>30.04</td>
      <td>-0.15</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>42.360</td>
    </tr>
    <tr>
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
      <td>95</td>
      <td>2020-02-07 22:24:00</td>
      <td>2020-02-07 22:24:1</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>255</td>
      <td>41</td>
      <td>40</td>
      <td>40</td>
      <td>1</td>
      <td>51</td>
      <td>...</td>
      <td>51</td>
      <td>51</td>
      <td>51</td>
      <td>51</td>
      <td>30.15</td>
      <td>30.14</td>
      <td>0.15</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.186</td>
    </tr>
    <tr>
      <td>96</td>
      <td>2020-02-07 22:29:00</td>
      <td>2020-02-07 22:29:1</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>3</td>
      <td>43</td>
      <td>41</td>
      <td>42</td>
      <td>1</td>
      <td>51</td>
      <td>...</td>
      <td>51</td>
      <td>51</td>
      <td>50</td>
      <td>51</td>
      <td>30.15</td>
      <td>30.14</td>
      <td>0.15</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.192</td>
    </tr>
    <tr>
      <td>97</td>
      <td>2020-02-07 22:34:00</td>
      <td>2020-02-07 22:34:1</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>299</td>
      <td>42</td>
      <td>40</td>
      <td>41</td>
      <td>1</td>
      <td>51</td>
      <td>...</td>
      <td>50</td>
      <td>51</td>
      <td>50</td>
      <td>50</td>
      <td>30.15</td>
      <td>30.14</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.192</td>
    </tr>
    <tr>
      <td>98</td>
      <td>2020-02-07 22:39:00</td>
      <td>2020-02-07 22:39:1</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>233</td>
      <td>42</td>
      <td>41</td>
      <td>41</td>
      <td>1</td>
      <td>51</td>
      <td>...</td>
      <td>51</td>
      <td>51</td>
      <td>51</td>
      <td>51</td>
      <td>30.15</td>
      <td>30.15</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.192</td>
    </tr>
    <tr>
      <td>99</td>
      <td>2020-02-07 22:44:00</td>
      <td>2020-02-07 22:44:1</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>248</td>
      <td>41</td>
      <td>39</td>
      <td>40</td>
      <td>1</td>
      <td>51</td>
      <td>...</td>
      <td>51</td>
      <td>51</td>
      <td>51</td>
      <td>51</td>
      <td>30.16</td>
      <td>30.15</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.198</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 29 columns</p>
</div>




```python
data_set.tail()
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
      <th>weather_datetime</th>
      <th>solar_datetime</th>
      <th>solarRadiation</th>
      <th>uvHigh</th>
      <th>winddirAvg</th>
      <th>humidityHigh</th>
      <th>humidityLow</th>
      <th>humidityAvg</th>
      <th>qcStatus</th>
      <th>tempHigh</th>
      <th>...</th>
      <th>windchillAvg</th>
      <th>heatindexHigh</th>
      <th>heatindexLow</th>
      <th>heatindexAvg</th>
      <th>pressureMax</th>
      <th>pressureMin</th>
      <th>pressureTrend</th>
      <th>precipRate</th>
      <th>precipTotal</th>
      <th>DC</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>7955</td>
      <td>2020-03-30 21:29:00</td>
      <td>2020-03-30 21:29:1</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>153</td>
      <td>25</td>
      <td>25</td>
      <td>25</td>
      <td>1</td>
      <td>62</td>
      <td>...</td>
      <td>62</td>
      <td>62</td>
      <td>62</td>
      <td>62</td>
      <td>30.25</td>
      <td>30.24</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.030</td>
    </tr>
    <tr>
      <td>7956</td>
      <td>2020-03-30 21:34:00</td>
      <td>2020-03-30 21:34:1</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>160</td>
      <td>25</td>
      <td>25</td>
      <td>25</td>
      <td>1</td>
      <td>62</td>
      <td>...</td>
      <td>62</td>
      <td>62</td>
      <td>62</td>
      <td>62</td>
      <td>30.25</td>
      <td>30.24</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.024</td>
    </tr>
    <tr>
      <td>7957</td>
      <td>2020-03-30 21:39:00</td>
      <td>2020-03-30 21:39:1</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>188</td>
      <td>25</td>
      <td>25</td>
      <td>25</td>
      <td>1</td>
      <td>62</td>
      <td>...</td>
      <td>62</td>
      <td>62</td>
      <td>62</td>
      <td>62</td>
      <td>30.25</td>
      <td>30.24</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.030</td>
    </tr>
    <tr>
      <td>7958</td>
      <td>2020-03-30 21:44:00</td>
      <td>2020-03-30 21:44:1</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>153</td>
      <td>25</td>
      <td>25</td>
      <td>25</td>
      <td>1</td>
      <td>62</td>
      <td>...</td>
      <td>62</td>
      <td>62</td>
      <td>62</td>
      <td>62</td>
      <td>30.25</td>
      <td>30.24</td>
      <td>-0.15</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.024</td>
    </tr>
    <tr>
      <td>7959</td>
      <td>2020-03-30 21:49:00</td>
      <td>2020-03-30 21:49:1</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>107</td>
      <td>25</td>
      <td>25</td>
      <td>25</td>
      <td>1</td>
      <td>62</td>
      <td>...</td>
      <td>62</td>
      <td>62</td>
      <td>62</td>
      <td>62</td>
      <td>30.25</td>
      <td>30.25</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.024</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 29 columns</p>
</div>



Observation: Notice that as it becomes later in the day, the solar radiation, uv, and temperature decreases. The DC voltage also decreases.


```python
# what other columns are there?
data_set.columns
```




    Index(['weather_datetime', 'solar_datetime', 'solarRadiation', 'uvHigh',
           'winddirAvg', 'humidityHigh', 'humidityLow', 'humidityAvg', 'qcStatus',
           'tempHigh', 'tempLow', 'tempAvg', 'windspeedHigh', 'windgustLow',
           'windspeedAvg', 'dewptHigh', 'dewptLow', 'dewptAvg', 'windchillHigh',
           'windchillAvg', 'heatindexHigh', 'heatindexLow', 'heatindexAvg',
           'pressureMax', 'pressureMin', 'pressureTrend', 'precipRate',
           'precipTotal', 'DC'],
          dtype='object')




```python
# what's the size of our data?
data_set.shape
```




    (7960, 29)




```python
# how distributed is the data?
data_set.describe()
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
      <th>solarRadiation</th>
      <th>uvHigh</th>
      <th>winddirAvg</th>
      <th>humidityHigh</th>
      <th>humidityLow</th>
      <th>humidityAvg</th>
      <th>qcStatus</th>
      <th>tempHigh</th>
      <th>tempLow</th>
      <th>tempAvg</th>
      <th>...</th>
      <th>windchillAvg</th>
      <th>heatindexHigh</th>
      <th>heatindexLow</th>
      <th>heatindexAvg</th>
      <th>pressureMax</th>
      <th>pressureMin</th>
      <th>pressureTrend</th>
      <th>precipRate</th>
      <th>precipTotal</th>
      <th>DC</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>count</td>
      <td>7960.000000</td>
      <td>7960.000000</td>
      <td>7960.000000</td>
      <td>7960.000000</td>
      <td>7960.000000</td>
      <td>7960.000000</td>
      <td>7960.000000</td>
      <td>7960.000000</td>
      <td>7960.000000</td>
      <td>7960.000000</td>
      <td>...</td>
      <td>7960.000000</td>
      <td>7960.000000</td>
      <td>7960.000000</td>
      <td>7960.000000</td>
      <td>7960.000000</td>
      <td>7960.000000</td>
      <td>7960.000000</td>
      <td>7960.000000</td>
      <td>7960.000000</td>
      <td>7960.000000</td>
    </tr>
    <tr>
      <td>mean</td>
      <td>180.382851</td>
      <td>1.844472</td>
      <td>182.790075</td>
      <td>45.861683</td>
      <td>44.912437</td>
      <td>45.077261</td>
      <td>0.893970</td>
      <td>53.745729</td>
      <td>53.420603</td>
      <td>53.554397</td>
      <td>...</td>
      <td>53.418970</td>
      <td>53.581533</td>
      <td>53.236935</td>
      <td>53.380653</td>
      <td>30.200309</td>
      <td>30.193024</td>
      <td>0.000974</td>
      <td>0.000469</td>
      <td>0.012936</td>
      <td>19.539283</td>
    </tr>
    <tr>
      <td>std</td>
      <td>264.082275</td>
      <td>2.846040</td>
      <td>78.432376</td>
      <td>21.862087</td>
      <td>21.940977</td>
      <td>21.924786</td>
      <td>0.311143</td>
      <td>11.622671</td>
      <td>11.565509</td>
      <td>11.589908</td>
      <td>...</td>
      <td>11.720226</td>
      <td>11.353402</td>
      <td>11.265849</td>
      <td>11.305647</td>
      <td>0.137469</td>
      <td>0.137532</td>
      <td>0.095205</td>
      <td>0.005050</td>
      <td>0.053583</td>
      <td>19.753129</td>
    </tr>
    <tr>
      <td>min</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>11.000000</td>
      <td>10.000000</td>
      <td>10.000000</td>
      <td>-1.000000</td>
      <td>27.000000</td>
      <td>27.000000</td>
      <td>27.000000</td>
      <td>...</td>
      <td>26.000000</td>
      <td>27.000000</td>
      <td>27.000000</td>
      <td>27.000000</td>
      <td>29.850000</td>
      <td>29.830000</td>
      <td>-0.600000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>25%</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>139.000000</td>
      <td>28.000000</td>
      <td>27.000000</td>
      <td>27.000000</td>
      <td>1.000000</td>
      <td>45.000000</td>
      <td>45.000000</td>
      <td>45.000000</td>
      <td>...</td>
      <td>45.000000</td>
      <td>45.000000</td>
      <td>45.000000</td>
      <td>45.000000</td>
      <td>30.100000</td>
      <td>30.100000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.030000</td>
    </tr>
    <tr>
      <td>50%</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>197.000000</td>
      <td>43.000000</td>
      <td>42.000000</td>
      <td>42.000000</td>
      <td>1.000000</td>
      <td>54.000000</td>
      <td>54.000000</td>
      <td>54.000000</td>
      <td>...</td>
      <td>54.000000</td>
      <td>54.000000</td>
      <td>54.000000</td>
      <td>54.000000</td>
      <td>30.180000</td>
      <td>30.180000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>8.379000</td>
    </tr>
    <tr>
      <td>75%</td>
      <td>333.120000</td>
      <td>3.000000</td>
      <td>215.000000</td>
      <td>60.000000</td>
      <td>59.000000</td>
      <td>60.000000</td>
      <td>1.000000</td>
      <td>62.000000</td>
      <td>62.000000</td>
      <td>62.000000</td>
      <td>...</td>
      <td>62.000000</td>
      <td>62.000000</td>
      <td>62.000000</td>
      <td>62.000000</td>
      <td>30.250000</td>
      <td>30.250000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>39.823500</td>
    </tr>
    <tr>
      <td>max</td>
      <td>986.880000</td>
      <td>10.000000</td>
      <td>359.000000</td>
      <td>98.000000</td>
      <td>98.000000</td>
      <td>98.000000</td>
      <td>1.000000</td>
      <td>83.000000</td>
      <td>83.000000</td>
      <td>83.000000</td>
      <td>...</td>
      <td>83.000000</td>
      <td>80.000000</td>
      <td>80.000000</td>
      <td>80.000000</td>
      <td>30.610000</td>
      <td>30.600000</td>
      <td>0.600000</td>
      <td>0.130000</td>
      <td>0.370000</td>
      <td>43.710000</td>
    </tr>
  </tbody>
</table>
<p>8 rows × 27 columns</p>
</div>




```python
# Use pd.DataFrame.corr function to see what correlations can be identified between DC and other features.
data_set.corr(method="spearman")
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
      <th>solarRadiation</th>
      <th>uvHigh</th>
      <th>winddirAvg</th>
      <th>humidityHigh</th>
      <th>humidityLow</th>
      <th>humidityAvg</th>
      <th>qcStatus</th>
      <th>tempHigh</th>
      <th>tempLow</th>
      <th>tempAvg</th>
      <th>...</th>
      <th>windchillAvg</th>
      <th>heatindexHigh</th>
      <th>heatindexLow</th>
      <th>heatindexAvg</th>
      <th>pressureMax</th>
      <th>pressureMin</th>
      <th>pressureTrend</th>
      <th>precipRate</th>
      <th>precipTotal</th>
      <th>DC</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>solarRadiation</td>
      <td>1.000000</td>
      <td>0.937831</td>
      <td>-0.342999</td>
      <td>-0.399324</td>
      <td>-0.408921</td>
      <td>-0.405694</td>
      <td>-0.051987</td>
      <td>0.452500</td>
      <td>0.444020</td>
      <td>0.448101</td>
      <td>...</td>
      <td>0.446924</td>
      <td>0.452235</td>
      <td>0.442935</td>
      <td>0.447495</td>
      <td>0.046178</td>
      <td>0.045571</td>
      <td>-0.073512</td>
      <td>0.001359</td>
      <td>0.035785</td>
      <td>0.814947</td>
    </tr>
    <tr>
      <td>uvHigh</td>
      <td>0.937831</td>
      <td>1.000000</td>
      <td>-0.316591</td>
      <td>-0.407405</td>
      <td>-0.418103</td>
      <td>-0.414418</td>
      <td>-0.048438</td>
      <td>0.454691</td>
      <td>0.445485</td>
      <td>0.449924</td>
      <td>...</td>
      <td>0.448278</td>
      <td>0.454565</td>
      <td>0.444371</td>
      <td>0.449403</td>
      <td>0.062698</td>
      <td>0.062510</td>
      <td>-0.096479</td>
      <td>-0.043045</td>
      <td>0.006510</td>
      <td>0.700912</td>
    </tr>
    <tr>
      <td>winddirAvg</td>
      <td>-0.342999</td>
      <td>-0.316591</td>
      <td>1.000000</td>
      <td>0.319881</td>
      <td>0.321128</td>
      <td>0.320633</td>
      <td>-0.058951</td>
      <td>-0.362586</td>
      <td>-0.361985</td>
      <td>-0.362065</td>
      <td>...</td>
      <td>-0.361569</td>
      <td>-0.362126</td>
      <td>-0.361636</td>
      <td>-0.361757</td>
      <td>0.196054</td>
      <td>0.195846</td>
      <td>0.011043</td>
      <td>-0.018541</td>
      <td>0.003610</td>
      <td>-0.259023</td>
    </tr>
    <tr>
      <td>humidityHigh</td>
      <td>-0.399324</td>
      <td>-0.407405</td>
      <td>0.319881</td>
      <td>1.000000</td>
      <td>0.998860</td>
      <td>0.999456</td>
      <td>-0.132945</td>
      <td>-0.765681</td>
      <td>-0.763568</td>
      <td>-0.764586</td>
      <td>...</td>
      <td>-0.759300</td>
      <td>-0.765219</td>
      <td>-0.763032</td>
      <td>-0.764093</td>
      <td>0.169556</td>
      <td>0.169504</td>
      <td>0.027204</td>
      <td>0.197191</td>
      <td>0.362307</td>
      <td>-0.102328</td>
    </tr>
    <tr>
      <td>humidityLow</td>
      <td>-0.408921</td>
      <td>-0.418103</td>
      <td>0.321128</td>
      <td>0.998860</td>
      <td>1.000000</td>
      <td>0.999736</td>
      <td>-0.132788</td>
      <td>-0.767797</td>
      <td>-0.765245</td>
      <td>-0.766474</td>
      <td>...</td>
      <td>-0.761256</td>
      <td>-0.767374</td>
      <td>-0.764697</td>
      <td>-0.765993</td>
      <td>0.167956</td>
      <td>0.167909</td>
      <td>0.029168</td>
      <td>0.196765</td>
      <td>0.360056</td>
      <td>-0.109135</td>
    </tr>
    <tr>
      <td>humidityAvg</td>
      <td>-0.405694</td>
      <td>-0.414418</td>
      <td>0.320633</td>
      <td>0.999456</td>
      <td>0.999736</td>
      <td>1.000000</td>
      <td>-0.132995</td>
      <td>-0.766938</td>
      <td>-0.764562</td>
      <td>-0.765708</td>
      <td>...</td>
      <td>-0.760461</td>
      <td>-0.766498</td>
      <td>-0.764012</td>
      <td>-0.765218</td>
      <td>0.168278</td>
      <td>0.168241</td>
      <td>0.028430</td>
      <td>0.196955</td>
      <td>0.361020</td>
      <td>-0.106906</td>
    </tr>
    <tr>
      <td>qcStatus</td>
      <td>-0.051987</td>
      <td>-0.048438</td>
      <td>-0.058951</td>
      <td>-0.132945</td>
      <td>-0.132788</td>
      <td>-0.132995</td>
      <td>1.000000</td>
      <td>0.050799</td>
      <td>0.052421</td>
      <td>0.051585</td>
      <td>...</td>
      <td>0.058814</td>
      <td>0.050671</td>
      <td>0.052195</td>
      <td>0.051358</td>
      <td>-0.130895</td>
      <td>-0.131655</td>
      <td>-0.013845</td>
      <td>0.023923</td>
      <td>0.051293</td>
      <td>-0.128036</td>
    </tr>
    <tr>
      <td>tempHigh</td>
      <td>0.452500</td>
      <td>0.454691</td>
      <td>-0.362586</td>
      <td>-0.765681</td>
      <td>-0.767797</td>
      <td>-0.766938</td>
      <td>0.050799</td>
      <td>1.000000</td>
      <td>0.999030</td>
      <td>0.999402</td>
      <td>...</td>
      <td>0.998271</td>
      <td>0.999708</td>
      <td>0.998698</td>
      <td>0.999141</td>
      <td>-0.451769</td>
      <td>-0.452192</td>
      <td>-0.045211</td>
      <td>-0.126698</td>
      <td>-0.170193</td>
      <td>0.176902</td>
    </tr>
    <tr>
      <td>tempLow</td>
      <td>0.444020</td>
      <td>0.445485</td>
      <td>-0.361985</td>
      <td>-0.763568</td>
      <td>-0.765245</td>
      <td>-0.764562</td>
      <td>0.052421</td>
      <td>0.999030</td>
      <td>1.000000</td>
      <td>0.999544</td>
      <td>...</td>
      <td>0.998397</td>
      <td>0.998736</td>
      <td>0.999677</td>
      <td>0.999288</td>
      <td>-0.455311</td>
      <td>-0.455749</td>
      <td>-0.043467</td>
      <td>-0.125614</td>
      <td>-0.170110</td>
      <td>0.170358</td>
    </tr>
    <tr>
      <td>tempAvg</td>
      <td>0.448101</td>
      <td>0.449924</td>
      <td>-0.362065</td>
      <td>-0.764586</td>
      <td>-0.766474</td>
      <td>-0.765708</td>
      <td>0.051585</td>
      <td>0.999402</td>
      <td>0.999544</td>
      <td>1.000000</td>
      <td>...</td>
      <td>0.998819</td>
      <td>0.999124</td>
      <td>0.999221</td>
      <td>0.999748</td>
      <td>-0.453805</td>
      <td>-0.454220</td>
      <td>-0.044260</td>
      <td>-0.126174</td>
      <td>-0.170251</td>
      <td>0.173594</td>
    </tr>
    <tr>
      <td>windspeedHigh</td>
      <td>0.397328</td>
      <td>0.383542</td>
      <td>-0.321357</td>
      <td>-0.520153</td>
      <td>-0.516107</td>
      <td>-0.517716</td>
      <td>-0.011902</td>
      <td>0.500599</td>
      <td>0.503205</td>
      <td>0.502059</td>
      <td>...</td>
      <td>0.484797</td>
      <td>0.500218</td>
      <td>0.502660</td>
      <td>0.501699</td>
      <td>-0.048984</td>
      <td>-0.049268</td>
      <td>-0.030898</td>
      <td>-0.107910</td>
      <td>-0.222856</td>
      <td>0.242005</td>
    </tr>
    <tr>
      <td>windgustLow</td>
      <td>0.330530</td>
      <td>0.317268</td>
      <td>-0.276300</td>
      <td>-0.407748</td>
      <td>-0.403040</td>
      <td>-0.404775</td>
      <td>-0.000308</td>
      <td>0.397769</td>
      <td>0.401008</td>
      <td>0.399604</td>
      <td>...</td>
      <td>0.382369</td>
      <td>0.397298</td>
      <td>0.400612</td>
      <td>0.399310</td>
      <td>-0.041768</td>
      <td>-0.041718</td>
      <td>-0.019151</td>
      <td>-0.086551</td>
      <td>-0.173471</td>
      <td>0.214411</td>
    </tr>
    <tr>
      <td>windspeedAvg</td>
      <td>0.387350</td>
      <td>0.372748</td>
      <td>-0.316086</td>
      <td>-0.489531</td>
      <td>-0.485025</td>
      <td>-0.486786</td>
      <td>-0.003781</td>
      <td>0.472159</td>
      <td>0.475211</td>
      <td>0.473931</td>
      <td>...</td>
      <td>0.456297</td>
      <td>0.471763</td>
      <td>0.474679</td>
      <td>0.473568</td>
      <td>-0.044306</td>
      <td>-0.044478</td>
      <td>-0.025686</td>
      <td>-0.101311</td>
      <td>-0.205448</td>
      <td>0.245529</td>
    </tr>
    <tr>
      <td>dewptHigh</td>
      <td>-0.052317</td>
      <td>-0.050672</td>
      <td>0.068162</td>
      <td>0.567230</td>
      <td>0.563345</td>
      <td>0.565181</td>
      <td>-0.143246</td>
      <td>0.050101</td>
      <td>0.052756</td>
      <td>0.051704</td>
      <td>...</td>
      <td>0.057759</td>
      <td>0.050857</td>
      <td>0.053645</td>
      <td>0.052601</td>
      <td>-0.235636</td>
      <td>-0.236398</td>
      <td>-0.020742</td>
      <td>0.119437</td>
      <td>0.299852</td>
      <td>0.049395</td>
    </tr>
    <tr>
      <td>dewptLow</td>
      <td>-0.080215</td>
      <td>-0.080862</td>
      <td>0.079540</td>
      <td>0.593152</td>
      <td>0.592413</td>
      <td>0.593069</td>
      <td>-0.141533</td>
      <td>0.017321</td>
      <td>0.020574</td>
      <td>0.019246</td>
      <td>...</td>
      <td>0.025345</td>
      <td>0.018005</td>
      <td>0.021514</td>
      <td>0.020128</td>
      <td>-0.227196</td>
      <td>-0.227908</td>
      <td>-0.017020</td>
      <td>0.124784</td>
      <td>0.308419</td>
      <td>0.035530</td>
    </tr>
    <tr>
      <td>dewptAvg</td>
      <td>-0.066127</td>
      <td>-0.065882</td>
      <td>0.074241</td>
      <td>0.580213</td>
      <td>0.577895</td>
      <td>0.579206</td>
      <td>-0.140929</td>
      <td>0.034221</td>
      <td>0.037206</td>
      <td>0.036009</td>
      <td>...</td>
      <td>0.042133</td>
      <td>0.034966</td>
      <td>0.038134</td>
      <td>0.036922</td>
      <td>-0.231846</td>
      <td>-0.232595</td>
      <td>-0.018377</td>
      <td>0.122124</td>
      <td>0.304470</td>
      <td>0.041853</td>
    </tr>
    <tr>
      <td>windchillHigh</td>
      <td>0.452330</td>
      <td>0.454327</td>
      <td>-0.363098</td>
      <td>-0.764098</td>
      <td>-0.766250</td>
      <td>-0.765379</td>
      <td>0.055493</td>
      <td>0.999509</td>
      <td>0.998592</td>
      <td>0.998945</td>
      <td>...</td>
      <td>0.998978</td>
      <td>0.999217</td>
      <td>0.998260</td>
      <td>0.998684</td>
      <td>-0.453846</td>
      <td>-0.454269</td>
      <td>-0.045599</td>
      <td>-0.126133</td>
      <td>-0.168847</td>
      <td>0.176803</td>
    </tr>
    <tr>
      <td>windchillAvg</td>
      <td>0.446924</td>
      <td>0.448278</td>
      <td>-0.361569</td>
      <td>-0.759300</td>
      <td>-0.761256</td>
      <td>-0.760461</td>
      <td>0.058814</td>
      <td>0.998271</td>
      <td>0.998397</td>
      <td>0.998819</td>
      <td>...</td>
      <td>1.000000</td>
      <td>0.997993</td>
      <td>0.998074</td>
      <td>0.998567</td>
      <td>-0.459162</td>
      <td>-0.459578</td>
      <td>-0.044587</td>
      <td>-0.124342</td>
      <td>-0.166472</td>
      <td>0.173580</td>
    </tr>
    <tr>
      <td>heatindexHigh</td>
      <td>0.452235</td>
      <td>0.454565</td>
      <td>-0.362126</td>
      <td>-0.765219</td>
      <td>-0.767374</td>
      <td>-0.766498</td>
      <td>0.050671</td>
      <td>0.999708</td>
      <td>0.998736</td>
      <td>0.999124</td>
      <td>...</td>
      <td>0.997993</td>
      <td>1.000000</td>
      <td>0.998743</td>
      <td>0.999269</td>
      <td>-0.452040</td>
      <td>-0.452460</td>
      <td>-0.045153</td>
      <td>-0.126702</td>
      <td>-0.170172</td>
      <td>0.176960</td>
    </tr>
    <tr>
      <td>heatindexLow</td>
      <td>0.442935</td>
      <td>0.444371</td>
      <td>-0.361636</td>
      <td>-0.763032</td>
      <td>-0.764697</td>
      <td>-0.764012</td>
      <td>0.052195</td>
      <td>0.998698</td>
      <td>0.999677</td>
      <td>0.999221</td>
      <td>...</td>
      <td>0.998074</td>
      <td>0.998743</td>
      <td>1.000000</td>
      <td>0.999439</td>
      <td>-0.455523</td>
      <td>-0.455950</td>
      <td>-0.043394</td>
      <td>-0.125619</td>
      <td>-0.170043</td>
      <td>0.170265</td>
    </tr>
    <tr>
      <td>heatindexAvg</td>
      <td>0.447495</td>
      <td>0.449403</td>
      <td>-0.361757</td>
      <td>-0.764093</td>
      <td>-0.765993</td>
      <td>-0.765218</td>
      <td>0.051358</td>
      <td>0.999141</td>
      <td>0.999288</td>
      <td>0.999748</td>
      <td>...</td>
      <td>0.998567</td>
      <td>0.999269</td>
      <td>0.999439</td>
      <td>1.000000</td>
      <td>-0.454029</td>
      <td>-0.454441</td>
      <td>-0.044092</td>
      <td>-0.126179</td>
      <td>-0.170233</td>
      <td>0.173561</td>
    </tr>
    <tr>
      <td>pressureMax</td>
      <td>0.046178</td>
      <td>0.062698</td>
      <td>0.196054</td>
      <td>0.169556</td>
      <td>0.167956</td>
      <td>0.168278</td>
      <td>-0.130895</td>
      <td>-0.451769</td>
      <td>-0.455311</td>
      <td>-0.453805</td>
      <td>...</td>
      <td>-0.459162</td>
      <td>-0.452040</td>
      <td>-0.455523</td>
      <td>-0.454029</td>
      <td>1.000000</td>
      <td>0.998638</td>
      <td>-0.016431</td>
      <td>-0.101865</td>
      <td>-0.224125</td>
      <td>0.081685</td>
    </tr>
    <tr>
      <td>pressureMin</td>
      <td>0.045571</td>
      <td>0.062510</td>
      <td>0.195846</td>
      <td>0.169504</td>
      <td>0.167909</td>
      <td>0.168241</td>
      <td>-0.131655</td>
      <td>-0.452192</td>
      <td>-0.455749</td>
      <td>-0.454220</td>
      <td>...</td>
      <td>-0.459578</td>
      <td>-0.452460</td>
      <td>-0.455950</td>
      <td>-0.454441</td>
      <td>0.998638</td>
      <td>1.000000</td>
      <td>-0.016344</td>
      <td>-0.103346</td>
      <td>-0.224628</td>
      <td>0.081169</td>
    </tr>
    <tr>
      <td>pressureTrend</td>
      <td>-0.073512</td>
      <td>-0.096479</td>
      <td>0.011043</td>
      <td>0.027204</td>
      <td>0.029168</td>
      <td>0.028430</td>
      <td>-0.013845</td>
      <td>-0.045211</td>
      <td>-0.043467</td>
      <td>-0.044260</td>
      <td>...</td>
      <td>-0.044587</td>
      <td>-0.045153</td>
      <td>-0.043394</td>
      <td>-0.044092</td>
      <td>-0.016431</td>
      <td>-0.016344</td>
      <td>1.000000</td>
      <td>0.018658</td>
      <td>0.018448</td>
      <td>-0.027936</td>
    </tr>
    <tr>
      <td>precipRate</td>
      <td>0.001359</td>
      <td>-0.043045</td>
      <td>-0.018541</td>
      <td>0.197191</td>
      <td>0.196765</td>
      <td>0.196955</td>
      <td>0.023923</td>
      <td>-0.126698</td>
      <td>-0.125614</td>
      <td>-0.126174</td>
      <td>...</td>
      <td>-0.124342</td>
      <td>-0.126702</td>
      <td>-0.125619</td>
      <td>-0.126179</td>
      <td>-0.101865</td>
      <td>-0.103346</td>
      <td>0.018658</td>
      <td>1.000000</td>
      <td>0.410878</td>
      <td>0.094109</td>
    </tr>
    <tr>
      <td>precipTotal</td>
      <td>0.035785</td>
      <td>0.006510</td>
      <td>0.003610</td>
      <td>0.362307</td>
      <td>0.360056</td>
      <td>0.361020</td>
      <td>0.051293</td>
      <td>-0.170193</td>
      <td>-0.170110</td>
      <td>-0.170251</td>
      <td>...</td>
      <td>-0.166472</td>
      <td>-0.170172</td>
      <td>-0.170043</td>
      <td>-0.170233</td>
      <td>-0.224125</td>
      <td>-0.224628</td>
      <td>0.018448</td>
      <td>0.410878</td>
      <td>1.000000</td>
      <td>0.140794</td>
    </tr>
    <tr>
      <td>DC</td>
      <td>0.814947</td>
      <td>0.700912</td>
      <td>-0.259023</td>
      <td>-0.102328</td>
      <td>-0.109135</td>
      <td>-0.106906</td>
      <td>-0.128036</td>
      <td>0.176902</td>
      <td>0.170358</td>
      <td>0.173594</td>
      <td>...</td>
      <td>0.173580</td>
      <td>0.176960</td>
      <td>0.170265</td>
      <td>0.173561</td>
      <td>0.081685</td>
      <td>0.081169</td>
      <td>-0.027936</td>
      <td>0.094109</td>
      <td>0.140794</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
<p>27 rows × 27 columns</p>
</div>



__Observation__: In relation to DC, it appears there is a strong correlation with: 
* ```solarRadiation``` - 0.8
* ```uvHigh``` - 0.7

and loose correlation with:

* ```tempHigh```
* ```tempLow```
* ```tempAvg```
* ```windchillAvg```
* ```heatindexHigh```
* ```heatindexLow```
* ```heatindexAvg```
* ```precipTotal```

Does this reflect any information gathered from our research?

### Step II: Feature Selection

We will split the data into features and labels and convert them into arrays to be used for our model.


```python
import numpy as np
```


```python
# we want to perdict DC
labels = np.array(data_set['DC'])
```


```python
# Remove the labels and unimportant features from the features list.

col = [
 'weather_datetime',
 'solar_datetime',
 'winddirAvg',
 'humidityHigh',
 'humidityLow',
 'humidityAvg',
 'heatindexLow',
 'heatindexHigh',
 'heatindexAvg',
 'qcStatus',
 'windspeedHigh',
 'windgustLow',
 'windspeedAvg',
 'dewptHigh',
 'dewptLow',
 'dewptAvg',
 'windchillHigh',
 'windchillAvg',
 'pressureMax',
 'pressureMin',
 'pressureTrend',
 'precipRate',
 'precipTotal',
 'DC']

features= data_set.drop(col, axis = 1)
feature_list = list(features.columns)
features = np.array(features)
```

### Step III: Build and Train Model
Split the data into train and test sets.


```python
from sklearn.model_selection import train_test_split
```


```python
# Note here that the test size is so low because I want to overfit the model since we have a separate test set.
train_features, test_features, train_labels, test_labels = train_test_split(features, labels, test_size = 0.1)
```


```python
print('Training Features Shape:', train_features.shape)
print('Training Labels Shape:', train_labels.shape)
print('Testing Features Shape:', test_features.shape)
print('Testing Labels Shape:', test_labels.shape)
```

    Training Features Shape: (7164, 5)
    Training Labels Shape: (7164,)
    Testing Features Shape: (796, 5)
    Testing Labels Shape: (796,)



```python
# the features we will be using to predict DC
feature_list
```




    ['solarRadiation', 'uvHigh', 'tempHigh', 'tempLow', 'tempAvg']



#### Step III.i: Hyper Parameters Tuning
Hyper Parameters Tuning is good for figuring out what parameters will work the best for building the model. It's much better than guessing. Although it isn't perfect, it gives us some clues on what to try.


```python
from sklearn.model_selection import RandomizedSearchCV
from sklearn.ensemble import RandomForestRegressor
from sklearn.ensemble import GradientBoostingRegressor

from scipy.stats import uniform as sp_randFloat
from scipy.stats import randint as sp_randInt
```

----
```Gradient Boost```

----


```python
gradient_boost_model = GradientBoostingRegressor()
gradient_params = {'learning_rate': sp_randFloat(),
                'subsample'    : sp_randFloat(),
                'n_estimators' : sp_randInt(200, 2000),
                'max_depth'    : sp_randInt(10, 110)
             }
```


```python
random_gradient = RandomizedSearchCV(estimator= gradient_boost_model, param_distributions = gradient_params, cv = 3, verbose=2, n_iter = 100, n_jobs=-1)
random_gradient.fit(train_features, train_labels)
```

    Fitting 3 folds for each of 100 candidates, totalling 300 fits


    [Parallel(n_jobs=-1)]: Using backend LokyBackend with 4 concurrent workers.
    [Parallel(n_jobs=-1)]: Done  33 tasks      | elapsed:  2.9min
    [Parallel(n_jobs=-1)]: Done 154 tasks      | elapsed: 14.9min
    [Parallel(n_jobs=-1)]: Done 300 out of 300 | elapsed: 28.2min finished





    RandomizedSearchCV(cv=3, estimator=GradientBoostingRegressor(), n_iter=100,
                       n_jobs=-1,
                       param_distributions={'learning_rate': <scipy.stats._distn_infrastructure.rv_frozen object at 0x7fe6ae2aedd0>,
                                            'max_depth': <scipy.stats._distn_infrastructure.rv_frozen object at 0x7fe6ae2aecd0>,
                                            'n_estimators': <scipy.stats._distn_infrastructure.rv_frozen object at 0x7fe6ae2ae050>,
                                            'subsample': <scipy.stats._distn_infrastructure.rv_frozen object at 0x7fe6ae2aee10>},
                       verbose=2)




```python
# Results from Random Search
print(" Results from Random Search " )
print("\n The best estimator across ALL searched params:\n", random_gradient.best_estimator_)
print("\n The best score across ALL searched params:\n", random_gradient.best_score_)
print("\n The best parameters across ALL searched params:\n", random_gradient.best_params_)
print(random_gradient.score(test_features , test_labels))
```

     Results from Random Search 
    
     The best estimator across ALL searched params:
     GradientBoostingRegressor(learning_rate=0.01794706377831745, max_depth=32,
                              n_estimators=785, subsample=0.2873167459093807)
    
     The best score across ALL searched params:
     0.9655473309872132
    
     The best parameters across ALL searched params:
     {'learning_rate': 0.01794706377831745, 'max_depth': 32, 'n_estimators': 785, 'subsample': 0.2873167459093807}
    0.9688789434674687


### Step III.ii: Random Forest Model


```python
# Instantiate model with 1500 decision trees
rf = RandomForestRegressor(n_estimators = 785, 
                           criterion="mse", 
                           max_depth = 32, 
                           min_samples_split = 2)
```


```python
# Train the model on training data
rf.fit(train_features, train_labels)
```




    RandomForestRegressor(max_depth=32, n_estimators=785)



#### Step III.iii: Accuracy - R2 Score
Let's see what the accuracy our model is using the training set provided.


```python
y_pred = rf.predict(test_features)
```


```python
from sklearn.metrics import r2_score

r2_score(test_labels, y_pred)
```




    0.9709460517127418



__Comment__: Our model has a accuracy of 97%! That's not bad at all.

### Sept IV: Predictions Using Test Dataset
Now we will test our model using the test set. Remember that whatever we did to the training set must also be done to the testing set!


```python
test_set = pd.read_csv("cahsi_data_2020/D2.csv")


col = [
 'weather_datetime',
 'solar_datetime',
 'winddirAvg',
 'humidityHigh',
 'humidityLow',
 'humidityAvg',
 'heatindexLow',
 'heatindexHigh',
 'heatindexAvg',
 'qcStatus',
 'windspeedHigh',
 'windgustLow',
 'windspeedAvg',
 'dewptHigh',
 'dewptLow',
 'dewptAvg',
 'windchillHigh',
 'windchillAvg',
 'pressureMax',
 'pressureMin',
 'pressureTrend',
 'precipRate',
 'precipTotal']

testset_features = test_set.drop(col, axis = 1)
testset_features = np.array(testset_features)
```


```python
# Use the forest's predict method on the test data
predictions = rf.predict(testset_features)
```


```python
predictions
```




    array([1.20963236, 1.20963236, 0.70364704, ..., 6.45444127, 6.45444127,
           6.45444127])



### Step V: Dump predictions into text file for later use.


```python
print('Predictions:\n', predictions) 
file = open("answer.txt", "w") 

for num in predictions:

    content = str(num)
    file.write(content)
    file.write("\n")

file.close()
```

    Predictions:
     [1.20963236 1.20963236 0.70364704 ... 6.45444127 6.45444127 6.45444127]

