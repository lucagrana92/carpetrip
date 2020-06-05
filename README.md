# carpetrip
capstone project for IBM's Professional Data Scientist certificate

## Introduction/Business Problem
<sub>*Have you ever bought some plane tickets to spend the weekend away but have little to no idea what are the main attractions of the city you will be visiting?  
Have you ever been on a work trip abroad and wanted to take advantage of the situation to tour the city in the few days you have available before going home?  
Your plane leaves tomorrow and you have no time to plan your trip?  
If yes this solution could help you get organized with helpful tips and machine learning - powered insights.*</sub>

In this repository you can find the backend of a chatbot that is aimed both at helping those who struggle with the frantic rhythms of today's life whenever they have to plan for a vacation and those who are part of the larger group of people who, when they first visit a place, tend to see only the best known sights and miss out on most of the other interesting venues. 

Furthermore, a widespread economic problem is addressed, according to which tourists usually don't spend much time thinking about the optimal solution in terms of accomodation, showing a preference for those hotels that have the lowest price for a room within their budget range.
This can be sometimes misleading, since a hotel that is very far from all the points of interest may be cheap but could also mean lots of dollars spent in taxi rides to get around the city.

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

		

	![scraping](https://dl.boxcloud.com/api/2.0/internal_files/674008224911/versions/715223565311/representations/jpg_paged_2048x2048/content/1.jpg?access_token=1!jP27rUKqPNnG7--7R9AFTXfo9saUmSdqI8vSKTYVOILuaOUvR13vu2hcIzoZWvDf9oD2up5vlTUdFBoXPCQk3IcnlEMqgNolWmIgiaXObJOa6cnwhNB8sgUPMQECdsE2W_TFmNLHrbDZjRlJ_87lSbnZUVCclaQ-GxFW0_v6Z6dVX2XTTwWKcSU0KNUqeF6nOkpLt9057rVxjyaLkgfZ1AEhota12vIAWozP3RODvxhWSuXOg-Rnw3CS7-qYnoT6iNPAMPpkTtZKiigw0qwkVSLAoQWafoQyAIddU3wLFYlXIrX8m-dlfWbTsPtFrO22XzsoT3cCQlXe7r3uWw5PgfM-9CJ6GmBMlElTQHAOJ7ubqKsatYT6IAhUBjBtq_8Qt7tnnnzv3BpAxpRexMT3qKzgxYtbuUc8YAoXWjZeOS2crgqglkKnjuLe0QbXOZ9NdVLZpWyDqoYPt1gawi0bIFO8VDHwrjkpPNwhSWX0IQmorok5lUehkQBIz_PS4mhB7NDsQlXtUKi5zg2AzQWemXnx6RjhnxO2O_Dn3Akg1Udh4cdJKemPHlgZDcd6ZvOEhQ..&box_client_name=box-content-preview&box_client_version=2.42.0)
- [TaxiFareFinder](https://https://www.taxifarefinder.com/) website: making an estimate of the cost of transports during a vacation can be hard without access to the proper API for the city of interest; to circumvent complications, *carpetrip* scrapes taxi fares from this useful online calculator 

	![enter image description here](https://dl.boxcloud.com/api/2.0/internal_files/674015418511/versions/715231307311/representations/jpg_paged_2048x2048/content/1.jpg?access_token=1!hvIMRPeQ_Y_3i8uxKYa3M8VN0DcXq6efrnRKFi5-b0OtkteAW_VFC38p9IX9ou22Ia83VJAheEj2WtVFo9kltzHYYXs_NEAOmnXb5nQ7bKbaq-LV63h0ADL1D3jo3AxbBzM0R_pxH8fKPjPlXUAB5Q-wnPmMojI9qBiKGattk3ZcodE8C9_cBLi4pytoY0g0HCnxY2Hsv3lSv-0hfRFDpMxfPYW2eblvkoxRqHkZFC_JULGCkrg4QuU14izOkl8hKbskKIkW17tuBB4UfXIai7um_p8gpUzHJZTPOrHIWYMA27EaKQBZbMkh2_d9DBI0hIdlYfFZKZtz7mFidi2eqe21STr25zDOFSDyrebA5ch44EryIfiNb_GPZHtaNRDo_MX4YnTwdY3J8g7eAoSB6NtTLYZDnKSnwhOpGDflFvYMMpcfRu-nbvolYXhSyJxw10kll3WQmMBRuwkleMtVD1GHSi3sq25JBMdLbox0gyjI9jwaK6l5at7XJnYxQ3x2DWquXqEJgCl9JtHLykWqfDzmnzlwMvw4FHAuHyphvEXMg0xKd9OaMo0PlcfgA672-Q..&box_client_name=box-content-preview&box_client_version=2.42.0)
- [Nominatim](https://nominatim.openstreetmap.org/): Nominatim is a search engine for [OpenStreetMap](https://www.openstreetmap.org/) data available for use in Python within the geopy.geocoders library; in the context of this solution, Nominatim is used to perform geocoding of  popular places' address

	![enter image description here](https://dl.boxcloud.com/api/2.0/internal_files/674005703981/versions/715221475181/representations/jpg_paged_2048x2048/content/1.jpg?access_token=1!2QxKIlXrszIhqSU_IKKdQzgafqXwXJMgJ263JYMGdli1B-gnIrOEbKrttURLTm2HY53nDkne2Dz8kqiwalhMO48PY43MtQERsNAu8maCBO6f3oh6YW7n9r0lQAQ7q4IC41VVUKLGxQJKAoCdxV_jB4uqpsP0_4ke95_vuHdA5jdFhTKyjF8_A97H4DAhJ-Oz14Rp4v4myjH3qpyA1RBoi8bUWuVSZ3XzqaHrBbZMYCapTaa99sAcgqMD8KwPMbtdXj4oAny36Dv-1zj0zl01ZlPg7RoEnkOHgH5EiTTTJj7nvvZZGa4tBgz6oW8hGU9YGwcXaIrKqZoqHbXtHcx59hsQX2j6TBxNYHv1m1seXaiwdF12Hj1yKTIi7rX3YOIM6RHqRlNhMX5aDTtX1Le9xS7VuRee_2-Z4_rNTFL2IFixgGHK6HuXHkjjMhL9SqlBWpZPpddIc3K0CBCmyKc4JiUKe4wrAD9fnGaExokoP7R2rGs9lBMtgAXPOkDm-lIRn-stzylmv9bymFUtscl2IU3SvnIw1i5BAqo7yMUjgVPMz-2N-TPjOQaszJif6I1A6w..&box_client_name=box-content-preview&box_client_version=2.42.0)
## Methodology

#### step 1
The first step to take in analyzing the sights of a city is defining its perimeter. In this case, the Nominatim class in the geopy.geocoders module comes in handy because, when the name of a geographical area such as *Manhattan, New York, NY* is passed as an argument to it, it returns a json with raw information pertinent to that location: as it's possible to see from the example, the JSON also contains the bounding box of the considered area, from which it is possible to define the scope of the search as the blue area in the second image below.

![nominatim](https://dl.boxcloud.com/api/2.0/internal_files/674572066379/versions/715841717579/representations/jpg_paged_2048x2048/content/1.jpg?access_token=1!cXXeFwsUWstO1BIqgSlVYphraL32GGnU_oaf7QGAtl1phS7HkvvGBoYFa5Kpy-Z8Bf0XLmBDEDSTXenpi7C36IVZLrV53IMQzBvHsOE9V8mAkf_nrirklWQGU6OP04WP3NUKHLUqkqnKbM8Nu3H-69gqUjV6ma0Nrydh8TqYzw4U2wSIcTzT--FtJyUQr8g5FHUVhf4JnTQfIHbEKTbjIXzvxi1yn6g2c32ZSj1ENmEuvuxM5RX6VEg4nMKIPGS-T37W3x434Hv02O0G6FpjOz0xca44XXBKM1ybCYku-jA4z_PSN6xhR-7e4UjT7EeCuyveObZuJfsnUnhaQGV31Y8boVRpGdoa9kMIkutF0TVAWC0B3ujJQmn9dxJUa4QLIay8qlyo3Veafx5MA8wKfdqmN2CyLb9hbOZq1LOK65jxTwi4RwjAvUC40vn7U_Gsa_xZ1i8mE1WwdQkuQun5hzol6_-If1BhxXaA4oxf4piX__u93EpgjagqWXuZFM-NIxVf-z2Gn2KoEtwwadkNynmEQxw1LPe6GlKfBhxb4bfQQ01PcRDCghCADECZMOAelA..&box_client_name=box-content-preview&box_client_version=2.42.0)

![nybox]()

#### step 2
As a second step, the chatbot proposes a list of places to visit based on the categories of attractions that the user finds interesting. This is done by parsing Foursquare's response json to a GET request to [this url](#first-foursquare-url) into a tabular form like this: 

![possible locations]()

Once presented with the list, the user can make a first selection of the venues he is going to visit.

#### step 3
In a third step, a dbscan is performed on the catalogue of venues to see if there are any **clusters** of attractions that are densely concentrated within different areas of the city and if our users is already visiting one of those areas.  
*If so, we want to highlight to him the possibility to see more sights by just walking around the neighborhood and spending more of his time in that part of town.*

Such a methodical approach helps reduce stress and increase satisfaction due to:
- less moving frantically from one part to another of the city during the vacation,
- less time (and money!) spent in taxi while you could be walking and enjoying the local atmosphere,
- more venues visited within the same lapse of time

In these terms, the parameters of the DBSCAN are chosen considering that: 
**a.** for walking from a place to the next one to be convenient there should always be another attraction no further than 1km away   &rarr;    *ε = 1000 meters*
**b.** there should be at least two attractions to have a cluster &rarr; *min_samples=2*

The result is the following, where a different color highlights a different cluster (-1 represents outliers) and venues that have already been selected by the user are represented with a larger mark:

![clusters view]()

As a result of this process we have defined the final list of sights that will be visited by the user.

#### step 4
Next step is to help the user find a hotel that minimizes the overall spending (taxi + accomodation). In order to achieve this goal, carpetrip finds the *center of gravity* (mathematically, the spot that minimizes the sum of distances to an ensemble of points) of the selected venues and query the Foursquare service at [this](#second-foursquare-url) url to find all the facilities within a radius of 1000 meters (blue area in the example).

![clusters view]()

In order to make comparisons, the price of a room in each hotel needs to be retrieved from Trip Advisor; however,  it's worth to point out that just for the city of New York the website contains over 900 listings spread over about 30 pages and that each time a page is loaded the website needs time to execute the underlying script to load information about room prices. 
The issue of automating the process of parsing through different pages of listing is easily solved if we consider the url's structure:
 ```https://www.tripadvisor.it/Hotels-g60763-oa{}-New_York_City_New_York-Hotels.html ```
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
![user venues]()

A few assumptions are made:

-  the airport is considered to be the starting and ending point of the trip
- each day the user leaves from the hotel to visit the farthest venue 
- the user cannot visit more than a predetermined number of venues per day (if the total number is 15 in 3 days  &rarr; 5 venues per day) 

The plan is then defined by a series of logical rules depending on two scenarios:

1. the user is visiting a point from the -1 cluster (outlier):
	carpetrip looks for the closest point
	- if that point is also an outlier that will be the next venue in the plan
	- if the closest point belongs to a cluster, the algorithm checks if the cluster fits in the maximum number of venues that can be visited in a day
		-  if not, it will break down the cluster
		- if so, the algrithm checks if there is enough time left in the current day to visit the cluster, if not it will direct the user there on another day
2. the user is visiting a place from one of the clusters:
	carpetrip looks for other points in the cluster
	
	- if there is such point, then it directs the user there
	- if the venues from that cluster are over, then it looks for the closest point and repeats the process described in case 1

The final artifact looks something like this:
![venues plan]()

#### step 6 
Once the plan is defined, it's possible to calculate the cost of moving between places, assuming this is done 
- by taxi for venues that are further apart than 1 km 
- by walk for venues that are closer.



