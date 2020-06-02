# carpetrip
capstone project for IBM's Professional Data Scientist certificate

## Introduction/Business Problem
Have you ever bought some plane tickets to spend the weekend away but have little to no idea what are the main attractions of the city you will be visiting?
Have you ever been on a work trip abroad and wanted to take advantage of the situation to tour the city in the few days you have available before having to go home?
Your plane leaves tomorrow and you have no time to plan your trip?
If yes this solution could help you get organized with helpful tips and machine learning - powered insights.

In this repository you can find the backend of a chatbot that is aimed at helping those who struggle with the frantic rhythms of today's life whenever they have to plan for a vacation, or those who are part of the larger group of people who, when they visit a place for the first time, tend to visit only the best known sights and miss out on most of the other interesting venues. 
# carpetrip
capstone project for IBM's Professional Data Scientist certificate

## Introduction/Business Problem
Have you ever bought some plane tickets to spend the weekend away but have little to no idea what are the main attractions of the city you will be visiting?  
Have you ever been on a work trip abroad and wanted to take advantage of the situation to tour the city in the few days you have available before going home?  
Your plane leaves tomorrow and you have no time to plan your trip?  
If yes this solution could help you get organized with helpful tips and machine learning - powered insights.

In this repository you can find the backend of a chatbot that is aimed at helping those who struggle with the frantic rhythms of today's life whenever they have to plan for a vacation, or those who are part of the larger group of people who, when they visit a place for the first time, tend to visit only the best known sights and miss out on most of the other interesting venues. 

Furthermore, a widespread economic problem is addressed, according to which tourists usually don't spend much time thinking about the optimal solution in terms of accomodation, showing a preference for those hotels that have the lowest price for a room within their budget range.
This can be sometimes misleading, since a hotel that is very far from all the points of interest may be cheap but could also mean lots of dollars spent in taxi rides to get around the city.

In order to face these problems and to reach out to tourists in a more general way, so that they can fully take advantage of their time off, the bot will help the user:
1. identify the venues he will be visiting during his journey,
2. make a detailed plan (day by day) 
3. find the hotel that minimizes the total cost of travelling + overnight stay, thus allowing access to higher value accomodations while spending less in some cases.

## Data collection
In order to fullfil its purpose, the *carpetrip* bot will be using the following data sources:
- [Foursquare](https://it.foursquare.com/) API: via the *search* endpoint in the *venues* group, this source allows to retrieve information about popular sights in the city of interest and present them as a list within which to choose to the user; moreover, once an optimal area for accomodation is defined, this API allows to get hotels in that area by leveraging the same endpoint;
- [Trip Advisor](https://www.tripadvisor.it/) website: in order to obtain room prices for the recommended hotels, the *carpetrip* bot scrapes them from the html source code of this notorious travel agency's website by means of a webdriver 
- [TaxiFareFinder](https://https://www.taxifarefinder.com/) website: making an estimate of the cost of transports during a vacation can be hard without access to the proper API for the city of interest; to circumvent complications, *carpetrip* scrapes taxi fares from this useful online calculator 
- Nominatim: Nominatim is a search engine for [OpenStreetMap](https://www.openstreetmap.org/) data available for use in Python within the geopy.geocoders library; in the context of this solution, Nominatim is used to perform geocoding of  popular places' adress
Furthermore, a widespread economic problem is addressed, according to which tourists usually don't spend much time thinking about the optimal solution in terms of accomodation, showing a preference for those hotels that have the lowest price for a room within their budget range.
This can be sometimes misleading, since a hotel that is very far from all the venues we intend to visit may be cheap but could also mean lots of dollars spent in taxi rides to get around the city.

In order to face these problems and to reach out to tourists in a more general way, so that they can fully take advantage of their time off, the bot will help the user:
1. identify the venues he will be visiting during his journey,
2. make a detailed plan (day by day) 
3. find the hotel that minimizes the total cost of travelling + overnight stay, thus allowing access to higher value accomodations while spending less in some cases.
