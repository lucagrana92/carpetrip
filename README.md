# carpetrip
capstone project for IBM's Professional Data Scientist certificate

## Introduction/Business Problem
<sub>*Have you ever bought some plane tickets to spend the weekend away but have little to no idea what are the main attractions of the city you will be visiting?  
Have you ever been on a work trip abroad and wanted to take advantage of the situation to tour the city in the few days you have available before going home?  
Your plane leaves tomorrow and you have no time to plan your trip?  
If yes this solution could help you get organized with helpful tips and machine learning - powered insights.*</sub>

In this repository you can find the backend of a chatbot that is aimed at helping those who struggle with the frantic rhythms of today's life whenever they have to plan for a vacation, or those who are part of the larger group of people who, when they visit a place for the first time, tend to visit only the best known sights and miss out on most of the other interesting venues. 

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
	- commercial activities within an area, for example by setting a point of coordinates *ll*, a *radius* and with the possibility to restrict results through the *category* parameter (e.g. hotels)
		
		```https://api.foursquare.com/v2/venues/search?&client_id={}&client_secret={}&v={}&ll={},{}&radius={}&categoryId={}&limit={}```
- [Trip Advisor](https://www.tripadvisor.it/) website: in order to obtain room prices for the recommended hotels, the *carpetrip* bot scrapes them from the html source code of this notorious travel agency's website

		

	![scraping](https://dl.boxcloud.com/api/2.0/internal_files/674008224911/versions/715223565311/representations/jpg_paged_2048x2048/content/1.jpg?access_token=1!jP27rUKqPNnG7--7R9AFTXfo9saUmSdqI8vSKTYVOILuaOUvR13vu2hcIzoZWvDf9oD2up5vlTUdFBoXPCQk3IcnlEMqgNolWmIgiaXObJOa6cnwhNB8sgUPMQECdsE2W_TFmNLHrbDZjRlJ_87lSbnZUVCclaQ-GxFW0_v6Z6dVX2XTTwWKcSU0KNUqeF6nOkpLt9057rVxjyaLkgfZ1AEhota12vIAWozP3RODvxhWSuXOg-Rnw3CS7-qYnoT6iNPAMPpkTtZKiigw0qwkVSLAoQWafoQyAIddU3wLFYlXIrX8m-dlfWbTsPtFrO22XzsoT3cCQlXe7r3uWw5PgfM-9CJ6GmBMlElTQHAOJ7ubqKsatYT6IAhUBjBtq_8Qt7tnnnzv3BpAxpRexMT3qKzgxYtbuUc8YAoXWjZeOS2crgqglkKnjuLe0QbXOZ9NdVLZpWyDqoYPt1gawi0bIFO8VDHwrjkpPNwhSWX0IQmorok5lUehkQBIz_PS4mhB7NDsQlXtUKi5zg2AzQWemXnx6RjhnxO2O_Dn3Akg1Udh4cdJKemPHlgZDcd6ZvOEhQ..&box_client_name=box-content-preview&box_client_version=2.42.0)
- [TaxiFareFinder](https://https://www.taxifarefinder.com/) website: making an estimate of the cost of transports during a vacation can be hard without access to the proper API for the city of interest; to circumvent complications, *carpetrip* scrapes taxi fares from this useful online calculator 

	![enter image description here](https://dl.boxcloud.com/api/2.0/internal_files/674015418511/versions/715231307311/representations/jpg_paged_2048x2048/content/1.jpg?access_token=1!hvIMRPeQ_Y_3i8uxKYa3M8VN0DcXq6efrnRKFi5-b0OtkteAW_VFC38p9IX9ou22Ia83VJAheEj2WtVFo9kltzHYYXs_NEAOmnXb5nQ7bKbaq-LV63h0ADL1D3jo3AxbBzM0R_pxH8fKPjPlXUAB5Q-wnPmMojI9qBiKGattk3ZcodE8C9_cBLi4pytoY0g0HCnxY2Hsv3lSv-0hfRFDpMxfPYW2eblvkoxRqHkZFC_JULGCkrg4QuU14izOkl8hKbskKIkW17tuBB4UfXIai7um_p8gpUzHJZTPOrHIWYMA27EaKQBZbMkh2_d9DBI0hIdlYfFZKZtz7mFidi2eqe21STr25zDOFSDyrebA5ch44EryIfiNb_GPZHtaNRDo_MX4YnTwdY3J8g7eAoSB6NtTLYZDnKSnwhOpGDflFvYMMpcfRu-nbvolYXhSyJxw10kll3WQmMBRuwkleMtVD1GHSi3sq25JBMdLbox0gyjI9jwaK6l5at7XJnYxQ3x2DWquXqEJgCl9JtHLykWqfDzmnzlwMvw4FHAuHyphvEXMg0xKd9OaMo0PlcfgA672-Q..&box_client_name=box-content-preview&box_client_version=2.42.0)
- [Nominatim](https://nominatim.openstreetmap.org/): Nominatim is a search engine for [OpenStreetMap](https://www.openstreetmap.org/) data available for use in Python within the geopy.geocoders library; in the context of this solution, Nominatim is used to perform geocoding of  popular places' adress

	![enter image description here](https://dl.boxcloud.com/api/2.0/internal_files/674005703981/versions/715221475181/representations/jpg_paged_2048x2048/content/1.jpg?access_token=1!2QxKIlXrszIhqSU_IKKdQzgafqXwXJMgJ263JYMGdli1B-gnIrOEbKrttURLTm2HY53nDkne2Dz8kqiwalhMO48PY43MtQERsNAu8maCBO6f3oh6YW7n9r0lQAQ7q4IC41VVUKLGxQJKAoCdxV_jB4uqpsP0_4ke95_vuHdA5jdFhTKyjF8_A97H4DAhJ-Oz14Rp4v4myjH3qpyA1RBoi8bUWuVSZ3XzqaHrBbZMYCapTaa99sAcgqMD8KwPMbtdXj4oAny36Dv-1zj0zl01ZlPg7RoEnkOHgH5EiTTTJj7nvvZZGa4tBgz6oW8hGU9YGwcXaIrKqZoqHbXtHcx59hsQX2j6TBxNYHv1m1seXaiwdF12Hj1yKTIi7rX3YOIM6RHqRlNhMX5aDTtX1Le9xS7VuRee_2-Z4_rNTFL2IFixgGHK6HuXHkjjMhL9SqlBWpZPpddIc3K0CBCmyKc4JiUKe4wrAD9fnGaExokoP7R2rGs9lBMtgAXPOkDm-lIRn-stzylmv9bymFUtscl2IU3SvnIw1i5BAqo7yMUjgVPMz-2N-TPjOQaszJif6I1A6w..&box_client_name=box-content-preview&box_client_version=2.42.0)
