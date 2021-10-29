---
jupyter:
  jupytext:
    formats: ipynb,md
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.13.0
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

```python
import matplotlib.pyplot as plt
import numpy as np
%matplotlib widget
plt.rcParams['date.converter'] = 'concise'

```

<!-- #region -->
## Data source:
https://www.ncei.noaa.gov/pub/data/uscrn/products/hourly02/


```
Field#  Name                           Units
---------------------------------------------
   1    WBANNO                         XXXXX
   2    UTC_DATE                       YYYYMMDD
   3    UTC_TIME                       HHmm
   4    LST_DATE                       YYYYMMDD
   5    LST_TIME                       HHmm
   6    CRX_VN                         XXXXXX
   7    LONGITUDE                      Decimal_degrees
   8    LATITUDE                       Decimal_degrees
   9    T_CALC                         Celsius
   10   T_HR_AVG                       Celsius
   11   T_MAX                          Celsius
   12   T_MIN                          Celsius
   13   P_CALC                         mm
   14   SOLARAD                        W/m^2
   15   SOLARAD_FLAG                   X
   16   SOLARAD_MAX                    W/m^2
   17   SOLARAD_MAX_FLAG               X
   18   SOLARAD_MIN                    W/m^2
   19   SOLARAD_MIN_FLAG               X
   20   SUR_TEMP_TYPE                  X
   21   SUR_TEMP                       Celsius
   22   SUR_TEMP_FLAG                  X
   23   SUR_TEMP_MAX                   Celsius
   24   SUR_TEMP_MAX_FLAG              X
   25   SUR_TEMP_MIN                   Celsius
   26   SUR_TEMP_MIN_FLAG              X
   27   RH_HR_AVG                      %
   28   RH_HR_AVG_FLAG                 X
   29   SOIL_MOISTURE_5                m^3/m^3
   30   SOIL_MOISTURE_10               m^3/m^3
   31   SOIL_MOISTURE_20               m^3/m^3
   32   SOIL_MOISTURE_50               m^3/m^3
   33   SOIL_MOISTURE_100              m^3/m^3
   34   SOIL_TEMP_5                    Celsius
   35   SOIL_TEMP_10                   Celsius
   36   SOIL_TEMP_20                   Celsius
   37   SOIL_TEMP_50                   Celsius
   38   SOIL_TEMP_100                  Celsius
 ```
<!-- #endregion -->

```python
data = np.genfromtxt('data/all.txt')
date = data[:, 1]
time = data[:, 2]
datetime = []
for d, t in zip(date, time):
    st = f'{int(d):08d}T{int(t):04d}'
    st = st[:-2] + ':' + st[-2:]
    st = st[:4] + '-' + st[4:6] + '-' + st[6:]
    datetime += [np.datetime64(st)] 
datetime = np.asarray(datetime)
temp = data[:, 9]
temperature = np.where(temp>-1000, temp, np.NaN)

solar = data[:, 13]
solar = np.where(solar>-1000, solar, np.NaN)


fig, (ax0, ax1) = plt.subplots(2, 1, sharex=True, constrained_layout=True)
ax0.plot(datetime, temperature)

ax0.set_ylabel('$Temperature [^oC]$')

ax1.plot(datetime, solar)

ax1.set_ylabel('Solar Radiation $[W\,m^{-1}]$')

ax0.set_title('Dinosaur, Colorado')


```

```python
# Make day of the year
days = (datetime - np.datetime64('2018-01-01', 'D')).astype(np.float64) / 60 / 24
print(days.dtype)
print(days)
days = np.mod(days, 365)
fig, ax = plt.subplots()
ax.plot(datetime, days)
```

```python
with open('data/small.txt', 'w') as fout:
    fout.write('# Extracted from CRNH0203-2018-CO_Dinosaur_2_E.txt from https://www.ncei.noaa.gov/pub/data/uscrn/products/hourly02/ \n')
    fout.write('# datetime / day of year / temperature [deg C] / solar radiation [W/m^2] \n')

    for date, d, t, s in zip(datetime, days, temperature, solar):
        fout.write(f'{date} {d:8.3f} {t:6.1f} {s:9.1f}\n')
```

```python

```

## preprocess image data

https://neo.sci.gsfc.nasa.gov/view.php?datasetId=MOD_LSTD_E&date=2021-09-01

```python
a = 10
```

```python
dat = np.genfromtxt('data/MOD_LSTD_E_2021-08-29_gs_720x360.CSV', delimiter=',')
dat = np.where(dat<1000, dat, np.NaN)
dat = dat[::-1, :]
lon = np.arange(0, 360, 0.5)
lat = np.arange(-90, 90, 0.5)

```

```python
fig, ax = plt.subplots()
ax.pcolormesh(lon, lat, dat)
```

```python

```
