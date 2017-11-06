

```python
import csv
from citipy import citipy as cp
import os
import seaborn
import matplotlib.pyplot as plt 
import pandas as pd
import requests as req
import numpy as np
import json
import urllib.request
from urllib.request import urlopen
import sys
from datetime import datetime
 
```


```python
# provide a random selection of geos to call citipy which will return
# the nearest city in the worldcities.csv. 
lat_lng  = []
lngs = np.random.uniform(-180,180,1500)
lats = np.random.uniform(-90, 90,1500)

lat_lng[:] = zip(lats, lngs)
#print([lat_lng])

cities=[]

for pair in lat_lng:
   # print(pair)
    latitude, longitude = pair
    city = cp.nearest_city(latitude, longitude)
    if city not in cities:
       cities.append(city.city_name)
   
print(len(cities))
   
print(cities[10])     

```


```python
# Build partial query URL

api_key = "9ec4c13b501f2e96ec05ce20a6f5744c"
units = "imperial"
url = "http://api.openweathermap.org/data/2.5/weather?"

query_url = url + "units=" + units + "&appid=" + api_key

city_weather=[]
my_city_data={}
list_of_city_data = []

#pathname2url: handles any spaces in city & country names with a %20 "new%20york"
#parse JSON API data needed into py dict.
#put dict into list to use  to create dataframe

#for city in cities[0:5]:
for city in cities:    
    try:
        req_url = query_url + "&q=" + urllib.request.pathname2url(city)
        #print(city)
        city_weather = req.get(req_url).json()
        print(req_url)
        print(city_weather)
        
        city_name     = city_weather["name"]
        city_lat      = city_weather["coord"]["lat"]
        city_lng      = city_weather["coord"]["lon"]
        city_max_temp = city_weather["main"]["temp"]
        city_humidity = city_weather["main"]["humidity"]
        city_wind     = city_weather["wind"]["speed"]
        city_clouds   = city_weather["clouds"]["all"]
        city_country  = city_weather["sys"]["country"]
        city_id       = city_weather     ["id"]
        c_dt          = city_weather["dt"]
       
        city_date     = datetime.fromtimestamp(
                                 int(c_dt)).strftime('%m-%d-%Y')
                   
        my_city_data = {"city": city_name,
                          "lat": city_lat,
                          "lng": city_lng,
                          "max_temp": city_max_temp,
                          "humidity": city_humidity,
                          "cloudiness": city_clouds,
                          "wind_speed": city_wind,
                          "country": city_country,
                          "city_id": city_id,
                          "city_date" : city_date 
                       }
                           
      
        list_of_city_data.append(my_city_data)

    except Exception as e:
        print(e)            
    pass 
#print(my_city_data)

```


```python
#print(list_of_city_data)
weather_data = pd.DataFrame(list_of_city_data)
#print(weather_data)
weather_data.to_csv('city-weather-out.csv')

```


```python
#weather_data.plot(x=weather_data['lat'], y=weather_data['max_temp'])
# Build a scatter plot for each data type
plt.scatter(weather_data["lat"], weather_data["max_temp"], marker="o")

# # Incorporate the other graph properties
plt.title("Temperature in World Cities  " +  city_date)
plt.ylabel("Temperature (Fahrenheit)")
plt.xlabel("Latitude")
plt.grid(True)

plt.style.use('seaborn-white')

# # Save the figure
plt.savefig("TemperatureInWorldCities.png")

# Show plot
plt.show()
```


```python
#df.plot(x=df['lat'], y=df['wind_speed'])
# Build a scatter plot for each data type
plt.scatter(weather_data["lat"], weather_data["wind_speed"], marker="o")

# # Incorporate the other graph properties
plt.title("Wind in World Cities   " + city_date)
plt.ylabel("Wind Speed (MPH)")
plt.xlabel("Latitude")
plt.grid(True)
plt.style.use('seaborn-white')
# # Save the figure
plt.savefig("Current Wind Speed InWorldCities.png")

# Show plot
plt.show()
```


```python
#df.plot(x=df['lat'], y=df['clouds'])

# Build a scatter plot for each data type
plt.scatter(weather_data["lat"], weather_data["cloudiness"], marker="o")

# # Incorporate the other graph properties
plt.title("Cloudiness in World Cities   " + city_date)
plt.ylabel("Cloudiness (%)")
plt.xlabel("Latitude")
plt.grid(True)
plt.style.use('seaborn-white')
# # Save the figure
plt.savefig("Current Cloud Coverage InWorldCities.png")

# Show plot
plt.show()
```


```python
#df.plot(x=df['lat'], y=df['humidity'])

# Build a scatter plot for each data type
plt.scatter(weather_data["lat"], weather_data["humidity"], marker="o")

# # Incorporate the other graph properties
plt.title("Humidity in World Cities   " + city_date)
plt.ylabel("Humidity (%)")
plt.xlabel("Latitude")
plt.grid(True)
plt.style.use('seaborn-white')
# # Save the figure
plt.savefig("Current Humidity levels InWorldCities.png")

# Show plot
plt.show()
```
