[![GitHub license](https://img.shields.io/github/license/amanattar/A-Tale-of-Two-Cities)](https://github.com/Thomas-George-T/A-Tale-of-Two-Cities/blob/master/LICENSE.md)
![ViewCount](https://views.whatilearened.today/views/github/amanattar/A-Tale-of-Two-Cities.svg?cache=remove)

<h1 align="center"> A Tale of Two Cities </h1>

<h2 align="center">
Clustering the Neighbourhoods of Mumbai and London
  <br>
  <br>
Thomas George Thomas
<br>
  <br>
30th January 2021
</h2>

### Project Links:

1. **Code:** [Jupyter Notebook](https://github.com/amanattar/A-Tale-of-Two-Cities/blob/main/Tale_of_Two_Cities_Data_Science_Project.ipynb)
2. **Blog Post:** [Medium Article](https://medium.com/@attar.aman29/a-tale-of-two-cities-a8ade7fb5213)
3. **Report:** [Report](https://github.com/amanattar/A-Tale-of-Two-Cities/blob/main/Tale_of_Two_Cities_Report.ipynb)

# 1. Introduction

Mumbai and London are the most popular cities in the world. These two cities have major history in past. It has changed over the years and we now take a look at how cities have grown.
Mumbai and London are quite popular tourist and vacation destination for people around the world. They are diverse and multicultural and offer a wide variety of experiences that is widely sought after. We try to group the neighborhoods of Mumbai and London respectively and draw insights to what they look like now.

# 2. Business Problem

The aim is to help tourists choose their destinations depending on the experiences that the neighborhoods have to offer and what they would want to have. This also helps people make decisions if they are thinking about migrating to Mumbai or London or even if they want to relocate neighborhoods within the city. Our findings will help stakeholders make informed decisions and address any concerns they have including the different kinds of cuisines, provision stores and what the city has to offer.


# 3. Data Description

We require geolocation data for both Mumbai and London. Postal codes in each city serve as a starting point. Using Postal codes, we use can find out the neighborhoods, boroughs, venues and their most popular venue categories.


## 3.1 Mumbai

To derive our solution, We scrape our data from 
https://en.wikipedia.org/wiki/List_of_neighbourhoods_in_Mumbai

This wikipedia page has information about all the neighbourhoods, we limit it South Mumbai.

*borough* : Name of Neighbourhood

*town* : Name of borough

*latitude* : Latitude for Neighbourhood

*longitude* : Longitude for Neighbourhood

## 3.2 London

To derive our solution, We scrape our data from https://en.wikipedia.org/wiki/List_of_areas_of_London

This wikipedia page has information about all the neighbourhoods, we limit it London.

*borough* : Name of Neighbourhood

*town* : Name of borough

*post_code* : Postal codes for London.

This wikipedia page lacks information about the geographical locations. To solve this problem we use ArcGIS API

### 3.3 ArcGIS API

ArcGIS Online enables you to connect people, locations, and data using interactive maps. Work with smart, data-driven styles and intuitive analysis tools that deliver location intelligence. Share your insights with the world or specific groups. 

More specifically, we use ArcGIS to get the geo locations of the neighbourhoods of London. The following columns are added to our initial dataset which prepares our data. 

*latitude* : Latitude for Neighbourhood

*longitude* : Longitude for Neighbourhood

## 3.4 Foursquare API Data

We will need data about different venues in different neighbourhoods of that specific borough. In order to gain that information we will use "Foursquare" locational information. Foursquare is a location data provider with information about all manner of venues and events within an area of interest. Such information includes venue names, locations, menus and even photos. As such, the foursquare location platform will be used as the sole data source since all the stated required information can be obtained through the API.

After finding the list of neighbourhoods, we then connect to the Foursquare API to gather information about venues inside each and every neighbourhood. For each neighbourhood, we have chosen the radius to be 1000 meters.

The data retrieved from Foursquare contained information of venues within a specified distance of the longitude and latitude of the postcodes. The information obtained per venue as follows:

*Neighbourhood* : Name of the Neighbourhood

*Neighbourhood Latitude* : Latitude of the Neighbourhood

*Neighbourhood Longitude* : Longitude of the Neighbourhood

*Venue* : Name of the Venue

*Venue Latitude* : Latitude of Venue

*Venue Longitude* : Longitude of Venue

*Venue Category* : Category of Venue


Based on all the information collected for both Mumbai and London, we have sufficient data to build our model. We cluster the neighbourhoods together based on similar venue categories. We then present our observations and findings. Using this data, our stakeholders can take the necessary decision.