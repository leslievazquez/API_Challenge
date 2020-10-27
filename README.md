<p align="center">
  <img width="1980" height="300" src="https://github.com/leslievazquez/API_Challenge/blob/main/VacationPy/output_data/heatmap.png">
</p>

<h1 align ="center"><span>Vacation Planning:<br/>What's The Weather Like?</span></h1>

## Background
If you ask two people what their ideal weather conditions are you may receive similar responses. However, how similar would their responses be if you added more variables to the question such as humidity %, max temperature (F), cloudiness %, and wind speed? 

Did you know that you can use data to plan the ideal vacation based on your preference of weather conditions? 

The following project uses python scripts, APIs, and JSON traversals to answer a fundamental question: “What’s the weather like as we approach or distance ourselves from the equator?” You may know the answer, but what if you were asked to prove it? 

This project contains two parts: 1) WeatherPy and 2) VacationPy. 

### Part 1: WeatherPy 
For the first part of this project, a python script was used to visualize the weather of 500+ cities across the world. A script was created to generate random combinations of latitudes and longitudes to ensure that the cities were not repeated. Using the coordinates, a list of cities was pulled from OpenWeatherMap API using a series of successive API calls. The list of cities was saved into a csv file named `cities.csv`. 

This part of the project contains several scatterplots to analyze if the distance from the equator has an effect on max temperature (F), humidity %, cloudiness %, and wind speed (mph). Then a python function was created to generate linear regression on the relationship of the 4 variables previously mentioned with latitude. However, for the linear regression models, the data was separated into Northern Hemisphere (=> 0 degrees latitude) and Southern Hemisphere (< 0 degrees latitude). The scatterplots and linear regression models were saved individually as PNG images. 

### Part 2: VacationPy 
The second part of the project used the cities csv file created in part one to plan future vacations based on a set of criteria of ideal weather. For this part of the project, Google Places API and gmaps was used. 

-	*It is important to note that Google Places API has a limit. Any API usage beyond the $300 credit is charged to the user’s personal account. Google allows one to set quotas and limits to the daily requests to be sure you can't be charged. Check out Google Maps Platform Billing and Manage your cost of use for more information.*

For this part of the project a Google humidity heatmap was created to display the humidity for every city that was saved in `cities.csv`. To display the map in your environment, you have run `jupyter nbextension enable --py gmaps`.

The 500+ list of cities was narrowed down based on the following criteria: 
-	A max temperature lower than 80 degrees but higher than 70.
-	Wind speed less than 10 mph.
-	Zero cloudiness.

Once the ideal vacation spots were identified, the first hotel for each of these cities located within 5000 meters of their coordinates was found using Google Places API. 

The last part of this project displays a humidity heatmap with each hotel pin containing the Hotel Name, City, and Country. 

<p align="center">
  <img width="1980" height="300" src="https://github.com/leslievazquez/API_Challenge/blob/main/VacationPy/output_data/marker_display.png">
</p>

