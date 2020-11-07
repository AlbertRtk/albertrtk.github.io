---
author: albert
tags: python stock-market
---
Stooq.com is a web service that delivers information about stock markets around the world. The service provides also historical stock quotes in the form of CSV files. The data can be easily downloaded, and then analyzed, with Python.
<!--more-->

## Shortly about Stooq API
Historical stock market data are accessible with an URL in the form of https://stooq.com/q/d/l/?s=TICKER&i=INTERVAL, where TICKER is an abbreviation identifying traded share, and INTERVAL defines the time interval between quotations. You can find at Stooq information about tickers for all shares for which data are accessible. For Polish shares, it will be usually something like CCC, ALE, etc. In the case of US shares, it is required to add country code after the dot, e.g., APPL.US. Interval is one letter and can take the following values: d - daily, w - weekly, m - monthly, q - quarterly, and y - yearly.

## Downloading data
We will use ```urlretrieve``` to download the CSV file. Later, the data will be read to Pandas ```DataFrame```. There is also a need to import ```datetime``` to parse strings with date information. 


```python
from urllib.request import urlretrieve
from datetime import datetime
import pandas as pd
```

Let's download daily historical data for Warsaw Stock Exchange Index - WIG (polish: *Warszawski Indeks Giełdowy*).


```python
ticker = 'WIG'
interval = 'd'
```

The data should be accessible with the URL


```python
url = f'https://stooq.com/q/d/l/?s={ticker}&i={interval}'
```

I want to save the data into a CSV file with the ticker in the name.


```python
csv_file = ticker + '.csv'
```

To get the data, we just need to call ```urlretrieve```.


```python
urlretrieve(url, csv_file)
```



Done! Let's see if we can read the data into ```DataFrame```.


```python
data = pd.read_csv(csv_file, index_col='Date', parse_dates=['Date'],
            date_parser=lambda x: datetime.strptime(x, '%Y-%m-%d'))

data.tail()
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
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Volume</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2020-11-02</th>
      <td>44413.22</td>
      <td>45334.97</td>
      <td>44413.22</td>
      <td>45177.16</td>
      <td>53359303.0</td>
    </tr>
    <tr>
      <th>2020-11-03</th>
      <td>45543.15</td>
      <td>47177.81</td>
      <td>45510.32</td>
      <td>47160.46</td>
      <td>61401199.0</td>
    </tr>
    <tr>
      <th>2020-11-04</th>
      <td>46772.97</td>
      <td>47294.20</td>
      <td>46170.96</td>
      <td>47294.20</td>
      <td>51383416.0</td>
    </tr>
    <tr>
      <th>2020-11-05</th>
      <td>47809.56</td>
      <td>48422.93</td>
      <td>47656.12</td>
      <td>48295.31</td>
      <td>45575557.0</td>
    </tr>
    <tr>
      <th>2020-11-06</th>
      <td>48392.71</td>
      <td>49058.29</td>
      <td>47916.16</td>
      <td>48962.48</td>
      <td>46569222.0</td>
    </tr>
  </tbody>
</table>
</div>



We can also easily plot the data using ```mplfinance``` package.


```python
import mplfinance as mpl
mpl.plot(data.tail(100), type='candle', volume=True)
```


    
<img class="full_width" src="/assets/images/blog/2020-11-07/wig_candle.png" alt="WIG">    

