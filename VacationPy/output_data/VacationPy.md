# VacationPy
----

#### Note
* Keep an eye on your API usage. Use https://developers.google.com/maps/reporting/gmp-reporting as reference for how to monitor your usage and billing.

* Instructions have been included for each segment. You do not have to follow them exactly, but they are included to help you think through the steps.


```python
# Dependencies and Setup
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
import requests
import json
import gmaps
import os

# Import API key
from api_keys import g_key
```

### Store Part I results into DataFrame
* Load the csv exported in Part I to a DataFrame


```python
# Read .csv file created from WeatherPy
csv_file = pd.read_csv("../WeatherPy/output_data/cities.csv")
csv_file.head()
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
      <th>City</th>
      <th>Lat</th>
      <th>Long</th>
      <th>Max Temp</th>
      <th>Humidity</th>
      <th>Clouds</th>
      <th>Wind Speed</th>
      <th>Country</th>
      <th>Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Borba</td>
      <td>-4.39</td>
      <td>-59.59</td>
      <td>80.65</td>
      <td>90</td>
      <td>100</td>
      <td>2.24</td>
      <td>BR</td>
      <td>1603563472</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Taldan</td>
      <td>53.67</td>
      <td>124.80</td>
      <td>27.73</td>
      <td>94</td>
      <td>0</td>
      <td>5.39</td>
      <td>RU</td>
      <td>1603563472</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Tiksi</td>
      <td>71.69</td>
      <td>128.87</td>
      <td>12.33</td>
      <td>91</td>
      <td>59</td>
      <td>21.47</td>
      <td>RU</td>
      <td>1603563473</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ostrovnoy</td>
      <td>68.05</td>
      <td>39.51</td>
      <td>32.45</td>
      <td>97</td>
      <td>100</td>
      <td>13.38</td>
      <td>RU</td>
      <td>1603563473</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Albany</td>
      <td>42.60</td>
      <td>-73.97</td>
      <td>59.00</td>
      <td>66</td>
      <td>89</td>
      <td>12.80</td>
      <td>US</td>
      <td>1603563467</td>
    </tr>
  </tbody>
</table>
</div>



### Humidity Heatmap
* Configure gmaps.
* Use the Lat and Lng as locations and Humidity as the weight.
* Add Heatmap layer to map.


```python
gmaps.configure(api_key=g_key)
```


```python
# Define variables 
locations = csv_file[["Lat", "Long"]]
humidity = csv_file["Humidity"]

# Plot Heatmap
fig = gmaps.figure(center=(46.0, -5.0), zoom_level=2)
max_intensity = np.max(humidity)

# Create heat layer
heat_layer = gmaps.heatmap_layer(locations, weights = humidity, dissipating=False, max_intensity=100, point_radius=3)

# Add layer
fig.add_layer(heat_layer)

# Display figure
fig
```


    Figure(layout=FigureLayout(height='420px'))


### Create new DataFrame fitting weather criteria
* Narrow down the cities to fit weather conditions.
* Drop any rows will null values.


```python
# New DataFrame showing cities with ideal weather: 
# A max temperature lower than 85 degrees but higher than 70.
# Wind speed less than 15 mph.
# Zero cloudiness.

ideal_city = csv_file.loc[((csv_file['Max Temp'] <= 85) & (csv_file['Max Temp'] >= 70) \
                           & (csv_file['Wind Speed'] <= 15) & (csv_file['Clouds'] == 0).dropna(how='all'))]

ideal_city
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
      <th>City</th>
      <th>Lat</th>
      <th>Long</th>
      <th>Max Temp</th>
      <th>Humidity</th>
      <th>Clouds</th>
      <th>Wind Speed</th>
      <th>Country</th>
      <th>Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>101</th>
      <td>Talara</td>
      <td>-4.58</td>
      <td>-81.27</td>
      <td>78.80</td>
      <td>74</td>
      <td>0</td>
      <td>13.87</td>
      <td>PE</td>
      <td>1603563480</td>
    </tr>
    <tr>
      <th>127</th>
      <td>Los Andes</td>
      <td>-32.83</td>
      <td>-70.60</td>
      <td>79.00</td>
      <td>77</td>
      <td>0</td>
      <td>3.00</td>
      <td>CL</td>
      <td>1603563482</td>
    </tr>
    <tr>
      <th>166</th>
      <td>Kalakamati</td>
      <td>-20.59</td>
      <td>27.31</td>
      <td>80.60</td>
      <td>36</td>
      <td>0</td>
      <td>5.82</td>
      <td>BW</td>
      <td>1603563485</td>
    </tr>
    <tr>
      <th>178</th>
      <td>Salalah</td>
      <td>17.02</td>
      <td>54.09</td>
      <td>80.60</td>
      <td>42</td>
      <td>0</td>
      <td>4.70</td>
      <td>OM</td>
      <td>1603563486</td>
    </tr>
    <tr>
      <th>183</th>
      <td>Ovalle</td>
      <td>-30.60</td>
      <td>-71.20</td>
      <td>77.20</td>
      <td>32</td>
      <td>0</td>
      <td>10.98</td>
      <td>CL</td>
      <td>1603563486</td>
    </tr>
    <tr>
      <th>184</th>
      <td>Saint-Philippe</td>
      <td>-21.36</td>
      <td>55.77</td>
      <td>73.40</td>
      <td>68</td>
      <td>0</td>
      <td>10.29</td>
      <td>RE</td>
      <td>1603563486</td>
    </tr>
    <tr>
      <th>205</th>
      <td>Saint-Pierre</td>
      <td>-21.34</td>
      <td>55.48</td>
      <td>71.60</td>
      <td>68</td>
      <td>0</td>
      <td>10.29</td>
      <td>RE</td>
      <td>1603563487</td>
    </tr>
    <tr>
      <th>207</th>
      <td>Najrān</td>
      <td>17.49</td>
      <td>44.13</td>
      <td>75.20</td>
      <td>17</td>
      <td>0</td>
      <td>4.70</td>
      <td>SA</td>
      <td>1603563487</td>
    </tr>
    <tr>
      <th>209</th>
      <td>Chabahar</td>
      <td>25.29</td>
      <td>60.64</td>
      <td>78.80</td>
      <td>54</td>
      <td>0</td>
      <td>7.02</td>
      <td>IR</td>
      <td>1603563488</td>
    </tr>
    <tr>
      <th>257</th>
      <td>Mizdah</td>
      <td>31.45</td>
      <td>12.98</td>
      <td>70.79</td>
      <td>24</td>
      <td>0</td>
      <td>7.25</td>
      <td>LY</td>
      <td>1603563490</td>
    </tr>
    <tr>
      <th>258</th>
      <td>Assiut</td>
      <td>27.18</td>
      <td>31.18</td>
      <td>77.00</td>
      <td>47</td>
      <td>0</td>
      <td>13.87</td>
      <td>EG</td>
      <td>1603563422</td>
    </tr>
    <tr>
      <th>306</th>
      <td>Jalu</td>
      <td>29.03</td>
      <td>21.55</td>
      <td>70.09</td>
      <td>45</td>
      <td>0</td>
      <td>6.55</td>
      <td>LY</td>
      <td>1603563494</td>
    </tr>
    <tr>
      <th>350</th>
      <td>Eyl</td>
      <td>7.98</td>
      <td>49.82</td>
      <td>81.34</td>
      <td>70</td>
      <td>0</td>
      <td>5.95</td>
      <td>SO</td>
      <td>1603563496</td>
    </tr>
    <tr>
      <th>355</th>
      <td>Abnūb</td>
      <td>27.27</td>
      <td>31.15</td>
      <td>77.00</td>
      <td>47</td>
      <td>0</td>
      <td>13.87</td>
      <td>EG</td>
      <td>1603563497</td>
    </tr>
    <tr>
      <th>425</th>
      <td>Mount Isa</td>
      <td>-20.73</td>
      <td>139.50</td>
      <td>73.40</td>
      <td>46</td>
      <td>0</td>
      <td>12.75</td>
      <td>AU</td>
      <td>1603563501</td>
    </tr>
    <tr>
      <th>499</th>
      <td>Diplo</td>
      <td>24.47</td>
      <td>69.58</td>
      <td>80.24</td>
      <td>50</td>
      <td>0</td>
      <td>8.32</td>
      <td>PK</td>
      <td>1603563505</td>
    </tr>
    <tr>
      <th>513</th>
      <td>Tharād</td>
      <td>24.40</td>
      <td>71.63</td>
      <td>79.39</td>
      <td>31</td>
      <td>0</td>
      <td>3.42</td>
      <td>IN</td>
      <td>1603563506</td>
    </tr>
    <tr>
      <th>514</th>
      <td>Dwārka</td>
      <td>22.24</td>
      <td>68.97</td>
      <td>82.92</td>
      <td>55</td>
      <td>0</td>
      <td>5.26</td>
      <td>IN</td>
      <td>1603563506</td>
    </tr>
    <tr>
      <th>549</th>
      <td>Nālūt</td>
      <td>30.33</td>
      <td>10.85</td>
      <td>77.50</td>
      <td>23</td>
      <td>0</td>
      <td>7.38</td>
      <td>LY</td>
      <td>1603563508</td>
    </tr>
    <tr>
      <th>551</th>
      <td>Bayjī</td>
      <td>34.93</td>
      <td>43.49</td>
      <td>74.55</td>
      <td>19</td>
      <td>0</td>
      <td>6.98</td>
      <td>IQ</td>
      <td>1603563508</td>
    </tr>
  </tbody>
</table>
</div>



### Hotel Map
* Store into variable named `hotel_df`.
* Add a "Hotel Name" column to the DataFrame.
* Set parameters to search for hotels with 5000 meters.
* Hit the Google Places API for each city's coordinates.
* Store the first Hotel result into the DataFrame.
* Plot markers on top of the heatmap.


```python
# Store into variable named hotel_df
hotel_df = ideal_city.loc[:,["City","Country","Lat","Long"]]

# Add a "Hotel Name" column to the DataFrame
hotel_df["Hotel Name"] = ""
hotel_df
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
      <th>City</th>
      <th>Country</th>
      <th>Lat</th>
      <th>Long</th>
      <th>Hotel Name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>101</th>
      <td>Talara</td>
      <td>PE</td>
      <td>-4.58</td>
      <td>-81.27</td>
      <td></td>
    </tr>
    <tr>
      <th>127</th>
      <td>Los Andes</td>
      <td>CL</td>
      <td>-32.83</td>
      <td>-70.60</td>
      <td></td>
    </tr>
    <tr>
      <th>166</th>
      <td>Kalakamati</td>
      <td>BW</td>
      <td>-20.59</td>
      <td>27.31</td>
      <td></td>
    </tr>
    <tr>
      <th>178</th>
      <td>Salalah</td>
      <td>OM</td>
      <td>17.02</td>
      <td>54.09</td>
      <td></td>
    </tr>
    <tr>
      <th>183</th>
      <td>Ovalle</td>
      <td>CL</td>
      <td>-30.60</td>
      <td>-71.20</td>
      <td></td>
    </tr>
    <tr>
      <th>184</th>
      <td>Saint-Philippe</td>
      <td>RE</td>
      <td>-21.36</td>
      <td>55.77</td>
      <td></td>
    </tr>
    <tr>
      <th>205</th>
      <td>Saint-Pierre</td>
      <td>RE</td>
      <td>-21.34</td>
      <td>55.48</td>
      <td></td>
    </tr>
    <tr>
      <th>207</th>
      <td>Najrān</td>
      <td>SA</td>
      <td>17.49</td>
      <td>44.13</td>
      <td></td>
    </tr>
    <tr>
      <th>209</th>
      <td>Chabahar</td>
      <td>IR</td>
      <td>25.29</td>
      <td>60.64</td>
      <td></td>
    </tr>
    <tr>
      <th>257</th>
      <td>Mizdah</td>
      <td>LY</td>
      <td>31.45</td>
      <td>12.98</td>
      <td></td>
    </tr>
    <tr>
      <th>258</th>
      <td>Assiut</td>
      <td>EG</td>
      <td>27.18</td>
      <td>31.18</td>
      <td></td>
    </tr>
    <tr>
      <th>306</th>
      <td>Jalu</td>
      <td>LY</td>
      <td>29.03</td>
      <td>21.55</td>
      <td></td>
    </tr>
    <tr>
      <th>350</th>
      <td>Eyl</td>
      <td>SO</td>
      <td>7.98</td>
      <td>49.82</td>
      <td></td>
    </tr>
    <tr>
      <th>355</th>
      <td>Abnūb</td>
      <td>EG</td>
      <td>27.27</td>
      <td>31.15</td>
      <td></td>
    </tr>
    <tr>
      <th>425</th>
      <td>Mount Isa</td>
      <td>AU</td>
      <td>-20.73</td>
      <td>139.50</td>
      <td></td>
    </tr>
    <tr>
      <th>499</th>
      <td>Diplo</td>
      <td>PK</td>
      <td>24.47</td>
      <td>69.58</td>
      <td></td>
    </tr>
    <tr>
      <th>513</th>
      <td>Tharād</td>
      <td>IN</td>
      <td>24.40</td>
      <td>71.63</td>
      <td></td>
    </tr>
    <tr>
      <th>514</th>
      <td>Dwārka</td>
      <td>IN</td>
      <td>22.24</td>
      <td>68.97</td>
      <td></td>
    </tr>
    <tr>
      <th>549</th>
      <td>Nālūt</td>
      <td>LY</td>
      <td>30.33</td>
      <td>10.85</td>
      <td></td>
    </tr>
    <tr>
      <th>551</th>
      <td>Bayjī</td>
      <td>IQ</td>
      <td>34.93</td>
      <td>43.49</td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>




```python
for index, row in hotel_df.iterrows():
    
    base_url = "https://maps.googleapis.com/maps/api/place/nearbysearch/json"
    
    # Get latitude, longitude, and city from DataFrame 'ideal_city'
    lat = row['Lat']
    long = row['Long'] 
    city = row['City']
    
    # Set location
    coords = f"{lat}, {long}"
    
    # Set parameters 
    params = {
    "location": coords,
    "keyword": 'hotel',
    "radius": 5000,
    "type": 'lodging',
    "key": g_key}
    
    # Set url and make API request
    print(f"Retrieving Results for Index {index}: {city} at {coords}.")
    response = requests.get(base_url, params=params).json()
    
    # Extract results
    results = response['results']
    
    # Add the hotel name to dataframe
    try:
        print(f"First hotel in {city}  is {results[0]['name']}.") 
        hotel_df.loc[index, 'Hotel Name'] = results[0]['name'] 
       
    # If there is no hotel available, show missing field    
    except (KeyError, IndexError):
        print("Missing field/result... skipping.")  
    print("------------")
    
# Print end of search once searching is completed
print("-------End of Search-------")
```

    Retrieving Results for Index 101: Talara at -4.58, -81.27.
    First hotel in Talara  is Casa Andina Standard Talara.
    ------------
    Retrieving Results for Index 127: Los Andes at -32.83, -70.6.
    First hotel in Los Andes  is Cardon.
    ------------
    Retrieving Results for Index 166: Kalakamati at -20.59, 27.31.
    Missing field/result... skipping.
    ------------
    Retrieving Results for Index 178: Salalah at 17.02, 54.09.
    First hotel in Salalah  is Atana Stay Salalah.
    ------------
    Retrieving Results for Index 183: Ovalle at -30.6, -71.2.
    First hotel in Ovalle  is Hotel Keo.
    ------------
    Retrieving Results for Index 184: Saint-Philippe at -21.36, 55.77.
    First hotel in Saint-Philippe  is Les Embruns Du Baril.
    ------------
    Retrieving Results for Index 205: Saint-Pierre at -21.34, 55.48.
    First hotel in Saint-Pierre  is Villa Delisle, Hotel & Spa.
    ------------
    Retrieving Results for Index 207: Najrān at 17.49, 44.13.
    Missing field/result... skipping.
    ------------
    Retrieving Results for Index 209: Chabahar at 25.29, 60.64.
    First hotel in Chabahar  is Lipar Hotel.
    ------------
    Retrieving Results for Index 257: Mizdah at 31.45, 12.98.
    First hotel in Mizdah  is فندق مزدة الكبير.
    ------------
    Retrieving Results for Index 258: Assiut at 27.18, 31.18.
    First hotel in Assiut  is Florence Hotel Assiut.
    ------------
    Retrieving Results for Index 306: Jalu at 29.03, 21.55.
    First hotel in Jalu  is Jalu Hotel.
    ------------
    Retrieving Results for Index 350: Eyl at 7.98, 49.82.
    First hotel in Eyl  is Amiin Hotel.
    ------------
    Retrieving Results for Index 355: Abnūb at 27.27, 31.15.
    Missing field/result... skipping.
    ------------
    Retrieving Results for Index 425: Mount Isa at -20.73, 139.5.
    First hotel in Mount Isa  is Isa Hotel.
    ------------
    Retrieving Results for Index 499: Diplo at 24.47, 69.58.
    Missing field/result... skipping.
    ------------
    Retrieving Results for Index 513: Tharād at 24.4, 71.63.
    First hotel in Tharād  is HOTEL DESERT INN THARAD.
    ------------
    Retrieving Results for Index 514: Dwārka at 22.24, 68.97.
    First hotel in Dwārka  is VITS Devbhumi, Dwarka.
    ------------
    Retrieving Results for Index 549: Nālūt at 30.33, 10.85.
    Missing field/result... skipping.
    ------------
    Retrieving Results for Index 551: Bayjī at 34.93, 43.49.
    Missing field/result... skipping.
    ------------
    -------End of Search-------
    


```python
# Replace missing field in Hotel Name with NaN and drop these
hotel_df[hotel_df == ""] = np.nan
hotel_df = hotel_df.dropna()

# Create New DataFrame
hotel_data = pd.DataFrame(hotel_df)
hotel_data
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
      <th>City</th>
      <th>Country</th>
      <th>Lat</th>
      <th>Long</th>
      <th>Hotel Name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>101</th>
      <td>Talara</td>
      <td>PE</td>
      <td>-4.58</td>
      <td>-81.27</td>
      <td>Casa Andina Standard Talara</td>
    </tr>
    <tr>
      <th>127</th>
      <td>Los Andes</td>
      <td>CL</td>
      <td>-32.83</td>
      <td>-70.60</td>
      <td>Cardon</td>
    </tr>
    <tr>
      <th>178</th>
      <td>Salalah</td>
      <td>OM</td>
      <td>17.02</td>
      <td>54.09</td>
      <td>Atana Stay Salalah</td>
    </tr>
    <tr>
      <th>183</th>
      <td>Ovalle</td>
      <td>CL</td>
      <td>-30.60</td>
      <td>-71.20</td>
      <td>Hotel Keo</td>
    </tr>
    <tr>
      <th>184</th>
      <td>Saint-Philippe</td>
      <td>RE</td>
      <td>-21.36</td>
      <td>55.77</td>
      <td>Les Embruns Du Baril</td>
    </tr>
    <tr>
      <th>205</th>
      <td>Saint-Pierre</td>
      <td>RE</td>
      <td>-21.34</td>
      <td>55.48</td>
      <td>Villa Delisle, Hotel &amp; Spa</td>
    </tr>
    <tr>
      <th>209</th>
      <td>Chabahar</td>
      <td>IR</td>
      <td>25.29</td>
      <td>60.64</td>
      <td>Lipar Hotel</td>
    </tr>
    <tr>
      <th>257</th>
      <td>Mizdah</td>
      <td>LY</td>
      <td>31.45</td>
      <td>12.98</td>
      <td>فندق مزدة الكبير</td>
    </tr>
    <tr>
      <th>258</th>
      <td>Assiut</td>
      <td>EG</td>
      <td>27.18</td>
      <td>31.18</td>
      <td>Florence Hotel Assiut</td>
    </tr>
    <tr>
      <th>306</th>
      <td>Jalu</td>
      <td>LY</td>
      <td>29.03</td>
      <td>21.55</td>
      <td>Jalu Hotel</td>
    </tr>
    <tr>
      <th>350</th>
      <td>Eyl</td>
      <td>SO</td>
      <td>7.98</td>
      <td>49.82</td>
      <td>Amiin Hotel</td>
    </tr>
    <tr>
      <th>425</th>
      <td>Mount Isa</td>
      <td>AU</td>
      <td>-20.73</td>
      <td>139.50</td>
      <td>Isa Hotel</td>
    </tr>
    <tr>
      <th>513</th>
      <td>Tharād</td>
      <td>IN</td>
      <td>24.40</td>
      <td>71.63</td>
      <td>HOTEL DESERT INN THARAD</td>
    </tr>
    <tr>
      <th>514</th>
      <td>Dwārka</td>
      <td>IN</td>
      <td>22.24</td>
      <td>68.97</td>
      <td>VITS Devbhumi, Dwarka</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Save hotel data to a csv file
hotel_data.to_csv("output_data/hotel_data.csv", index=False)
```


```python
# NOTE: Do not change any of the code in this cell

# Using the template add the hotel marks to the heatmap
info_box_template = """
<dl>
<dt>Name</dt><dd>{Hotel Name}</dd>
<dt>City</dt><dd>{City}</dd>
<dt>Country</dt><dd>{Country}</dd>
</dl>
"""
# Store the DataFrame Row
# NOTE: be sure to update with your DataFrame name
hotel_info = [info_box_template.format(**row) for index, row in hotel_data.iterrows()]
locations = hotel_data[["Lat", "Long"]]
```


```python
# Add marker layer on top of heat map
marker_layer = gmaps.marker_layer(locations, info_box_content=hotel_info)
fig.add_layer(marker_layer)

# Display figure
fig
```


    Figure(layout=FigureLayout(height='420px'))



```python

```
