# carpetrip
capstone project for IBM's Professional Data Scientist certificate

## Introduction/Business Problem
<sub>*Have you ever bought some plane tickets to spend the weekend away but have little to no idea what are the main attractions of the city you will be visiting?  
Have you ever been on a work trip abroad and wanted to take advantage of the situation to tour the city in the few days you have available before going home?  
Your plane leaves tomorrow and you have no time to plan your trip?  
If yes this solution could help you get organized with helpful tips and machine learning - powered insights.*</sub>

In this repository you can find the backend of a chatbot that is aimed both at helping those who struggle with the frantic rhythms of today's life whenever they have to plan for a vacation and those who are part of the larger group of people who, when they first visit a place, tend to see only the best known sights and miss out on most of the other interesting venues. 

Furthermore, a widespread economic problem is addressed, according to which tourists usually don't spend much time thinking about the optimal solution in terms of accomodation, showing a preference for those hotels that have the lowest price for a room within their budget range.
This can be sometimes misleading, since a hotel that is very far from all the points of interest (POIs) may be cheap but could also mean lots of dollars spent in taxi rides to get around the city.

In order to face these problems and to reach out to tourists in a more general way, so that they can fully take advantage of their time off, the bot will help the user:
1. identify the venues he will be visiting during his journey,
2. make a detailed plan (day by day) 
3. find the hotel that minimizes the total cost of travelling + overnight stay, thus allowing access to higher value accomodations while spending less in some cases.

## Data collection
In order to fullfil its purpose,  *carpetrip*  will be using data from the sources listed below. Please note that this repository contains a proof of concept in the form of a jupyter notebook, where the features of the chatbot are presented by helping a hypothetical user plan a trip to New York and examples on how the data sources will be used are referred to that use case.
- [Foursquare](https://it.foursquare.com/) API: via the *search* endpoint in the *venues* group, this source allows to retrieve information such as:
	- popular sights in a city, for example by specifying a bounding box (through the bottom left corner, *sw*, and top right corner, *ne*) and a list of categories of interest (*categoryId*)

		```https://api.foursquare.com/v2/venues/search?&client_id={}&client_secret={}&v={}&sw={}&ne={}&intent={}&categoryId={}&limit={}```  
	
	<h4 id="first-foursquare-url"></h4> 
	
	- commercial activities within an area, for example by setting a point of coordinates *ll*, a *radius* and with the possibility to restrict results through the *category* parameter (e.g. hotels)


		```https://api.foursquare.com/v2/venues/search?&client_id={}&client_secret={}&v={}&ll={},{}&radius={}&categoryId={}&limit={}```
	<h4 id="second-foursquare-url"></h4>	
- [Trip Advisor](https://www.tripadvisor.it/) website: in order to obtain room prices for the recommended hotels, the *carpetrip* bot scrapes them from the html source code of this notorious travel agency's website

		

	![scraping](https://drive.google.com/uc?export=download&id=12JMbSOtVTIoHTjL3pNIzBSSGfF7yvoYR)
- [TaxiFareFinder](https://https://www.taxifarefinder.com/) website: making an estimate of the cost of transports during a vacation can be hard without access to the proper API for the city of interest; to circumvent complications, *carpetrip* scrapes taxi fares from this useful online calculator 

	![enter image description here](https://drive.google.com/uc?export=download&id=1Bfk8sQOPtptgt5RDANqfOxAftp68OIT3)
- [Nominatim](https://nominatim.openstreetmap.org/): Nominatim is a search engine for [OpenStreetMap](https://www.openstreetmap.org/) data available for use in Python within the geopy.geocoders library; in the context of this solution, Nominatim is used to perform geocoding of  popular places' address

	![enter image description here](https://drive.google.com/uc?export=download&id=1Rp0cpbLA-2XMmWaJ52in7jvZahaP4EQ4)
## Methodology

#### step 1
The first step to take in analyzing the sights of a city is defining its perimeter. In this case, the Nominatim class in the geopy.geocoders module comes in handy because, when the name of a geographical area such as *Manhattan, New York, NY* is passed as an argument to it, it returns a json with raw information pertinent to that location: as it's possible to see from the example, the JSON also contains the bounding box of the considered area, from which it is possible to define the scope of the search as the blue area in the second image below.

![nominatim](https://drive.google.com/uc?export=download&id=1d0cKb_IAANuAr4-dJO1ALKuj9QnmTu-P)

![nybox](https://drive.google.com/uc?export=download&id=1hFXVSiM-UdJcRdF3O8zh_6yigETx8kaT)

#### step 2
As a second step, the chatbot proposes a list of places to visit based on the categories of attractions that the user finds interesting. This is done by parsing Foursquare's response json to a GET request to [this url](#first-foursquare-url) into a tabular form like this: 

![possible locations](https://drive.google.com/uc?export=download&id=1TSjXpwKByvdoh1xRqdAPoMJnk0oFwHNW)

Once presented with the list, the user can make a first selection of the venues he is going to visit.

#### step 3
In a third step, a dbscan is performed on the catalogue of venues to see if there are any **clusters** of attractions that are densely concentrated within different areas of the city and if our users is already visiting one of those areas.  
*If so, we want to highlight to him the possibility to see more sights by just walking around the neighborhood and spending more of his time in that part of town.*

In these terms, the parameters of the DBSCAN are chosen considering that: 
**a.** for walking from a place to the next one to be convenient there should always be another attraction no further than 1km away   &rarr;    *ε = 1000 meters*
**b.** there should be at least two attractions to have a cluster &rarr; *min_samples=2*

The result is the following, where a different color highlights a different cluster (-1, in purple, represents the outliers) and venues that have already been selected by the user are represented with a larger mark:

![clusters view](https://drive.google.com/uc?export=download&id=1chdKeMoDLILVgVh6B8k9E5IwHO0Cvc-j)

As a result of this process we have defined the final list of sights that will be visited by the user.

#### step 4
Next step is to help the user find a hotel that minimizes the overall spending (taxi + accomodation). In order to achieve this goal, *carpetrip* finds the *center of gravity* (mathematically, the spot that minimizes the sum of distances to an ensemble of points) of the selected venues and query the Foursquare service at [this](#second-foursquare-url) url to find all the facilities within a radius of 1000 meters (blue area in the example).

![clusters view](https://drive.google.com/uc?export=download&id=1pBDsdMF-Q0zUgSzwxAPA3l5MIT7v7CaL)

In order to make comparisons, the price of a room in each hotel needs to be retrieved from Trip Advisor; however,  it's worth to point out that just for the city of New York the website contains over 900 listings spread over about 30 pages and that each time a page is loaded the website needs time to execute the underlying script to load information about room prices. 
The issue of automating the process of parsing through different pages of listing is easily solved if we consider the url's structure:
 ```
 https://www.tripadvisor.it/Hotels-g60763-oa{}-New_York_City_New_York-Hotels.html
```
that allows to switch page by simply inserting a multiple of 30 (the number of listings per page) in place of the '{}' sign. The problem of the execution of the script through which Trip Advisor finds the best prices can instead be solved by means of a webdriver object (from the *selenium* package) which emulates the browser.
A further issue that needs to be addressed is *data cleaning*, in facts the html class containing the price of a room also contains additional text such as:
```
Listing: Sponsorizzato
The Ludlow New York City
Price: 312 €
```
or even more than one price. For this reason, it is necessary to use a regex in the form of (\d{2,5}\s*€\s*)?(\d{2,5}) to retrieve only the second number in the item (usually the cheapest alternative). In the process, facilities whose data turn out to be incomplete will be discarded.

#### step 5

This step is about planning the trips to the selected venues and organizing the vacation of our user. 

![user venues](https://drive.google.com/uc?export=download&id=14x_08mZ4uR38rGcY-qV_j5OUZG6mR1it)

A few assumptions are made:

-  the airport is considered to be the starting and ending point of the trip
- each day the user leaves from the hotel to visit the farthest venue 
- the user cannot visit more than a predetermined number of venues per day (if the total number is 15 in 3 days  &rarr; 5 venues per day) 

The plan is then defined by a series of logical rules depending on two scenarios:

1. the user is visiting a point from the -1 cluster (outlier):
	*carpetrip* looks for the closest point
	- if that point is also an outlier that will be the next venue in the plan
	- if the closest point belongs to a cluster, the algorithm checks if the cluster fits in the maximum number of venues that can be visited in a day
		-  if not, it will break down the cluster
		- if so, the algrithm checks if there is enough time left in the current day to visit the cluster, if not it will direct the user there on another day
2. the user is visiting a place from one of the clusters:
	*carpetrip* looks for other points in the cluster
	
	- if there is such point, then it directs the user there
	- if the venues from that cluster are over, then it looks for the closest point and repeats the process described in case 1

The final artifact looks something like this:

![venues plan](https://drive.google.com/uc?export=download&id=1rqXdUPWX841pTmyiSy-Hig25LiaScAb3)

#### step 6 
Once the plan is defined, it's possible to calculate the cost of moving between places, assuming this is done 
- by taxi for venues that are further apart than 1 km 
- by walk for venues that are closer.

Taxi fares are obtained by making a GET request to the following url:
```
https://www.taxifarefinder.com/main.php?city={}&from={}&to={}&fromCoord={},{}&toCoord={},{}
```
with the starting and end points' name and their coordinates.

Also in this case, some data cleaning is necessary:
- before submitting the request, since names of places containing parentheses and other special characters prevent the engine from recognizing the location of interest
- when information is scraped from the source code, since the data  can contain trailing white spaces, €, etc

Furthermore, a webdriver object from *selenium* is used to allow execution of javascript code that calculates the fares.



#### step 7

As a final step, the total cost of a hotel is computed by summing up the fares for that facility with the price of the rooms times the number of nights (assumed to be equal to 2).

![total cost](https://drive.google.com/uc?export=download&id=11-3SNbQCXjOMfsKPGPYal2UxbWq07hRj)

Let's suppose our user declared a budget of maximum 200 € per night for a room: at this point the bot will consider the total cost of the farthest hotel from the optimal area with room price ≤ 200 € as a baseline and recommend to the user all those hotels that have a lower total cost than that one (even those whose cost per night is actually ≥ 200 €).

![best choice](https://drive.google.com/uc?export=download&id=1QMGule-jkq599MRdDSs59Tk3FcHFyOu6)

## Results
As it's possible to see from this documentation, the *carpetrip* bot aims at making the life of a tourist easier under multiple points of view: from learning about the points of interest within a city, to vacation planning and cost minimization. 
Some of these aspects are highly qualitative as they have an impact on user satisfaction and efficiency and thus should be measured through a feedback analysis process.
The latter aspect, cost, can however be measured in a more quantitative way.

Let's suppose our user was looking to book a room at the *Solita Soho Hotel* after making his own consideration on price and quality (to be noted, it has a 4/5 rating).

![Solita soho](https://drive.google.com/uc?export=download&id=1j44k7ubz0W6sNoXXMhP886y9AXC0g55G)

By running the algorithm described above with this choice of accomodation we obtain the following results:

![soho_rides](https://drive.google.com/uc?export=download&id=1TFUL1mNEGaHctxed8LDH5Eohd8ZHwwTH)

![soho_cost](https://drive.google.com/uc?export=download&id=1TZs-c2vLia_xhSzlxipcP60xg1AFxBGa)

Let's compare the total cost with that of hotels in the optimal area defined in the use case in the notebook:

![best choice](https://drive.google.com/uc?export=download&id=1QMGule-jkq599MRdDSs59Tk3FcHFyOu6)

We notice immediately, that although being out of the price range, the Park Hyatt hotel has a lower total cost and a higher rating on Trip Advisor than the Solita Soho Hotel, thus proving the thesis that **choosing a facility from the optimal area helps minimizing distance travelled between POIs, which in turn minimizes cost and allows the user to access higher value rooms for less money**.

## Discussion
A critic that could be made to the results discussed in the previous section may be that they are good but not outstanding, even though a solution with lower overall cost and better ratings was presented to the user as a result of using *carpetrip*. 

At first sight this may be a point to work on, however please bear in mind that for the use case presented in this repository,  the scope was limited to only a part of New York. In truth, what we can draw out from the example is that scaling up the proof of concept to a larger area than Manhattan - such as the entire city of New York or larger metropolitan areas like for example Paris, Rome and London - should increase benefits dramatically.
Indeed, this could be proved by considering the Hilton Garden Inn, i.e. the farthest hotel from the venues' *center of gravity* in Manhattan, which is only 6 km away from the optimal area we have considered. Not very far after all.

## Conclusion

In the end, it was demonstrated how *carpetrip* could be a gamechanger in the last-minute tourism market and could help users reduce stress related to planning vacations and increase their satisfaction thanks to:
- less moving frantically from one part to another of the city during the vacation,
- less time (and money!) spent in taxi while you could be walking and enjoying the local atmosphere,
- more venues visited within the same lapse of time


 


