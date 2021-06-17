[![GitHub license](https://img.shields.io/github/license/amanattar/A-Tale-of-Two-Cities)](https://github.com/Thomas-George-T/A-Tale-of-Two-Cities/blob/master/LICENSE.md)
![ViewCount](https://views.whatilearened.today/views/github/amanattar/A-Tale-of-Two-Cities.svg?cache=remove)

<h1 align="center"> A Tale of Two Cities </h1>

<h2 align="center">
Clustering the Neighbourhoods of Mumbai and London
  <br>
  <br>
Amanul Rahiman Shamshuddin Attar
  <br>
  <br>
</h2>

### Project Links:

1. **Code:** [Jupyter Notebook](https://github.com/amanattar/A-Tale-of-Two-Cities/blob/main/Tale_of_Two_Cities_Data_Science_Project.ipynb)
2. **Blog Post:** [Medium Article](https://amanattar.medium.com/a-tale-of-two-cities-mumbai-and-london-42da60e79785)
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

# 4. Methodology
We will be creating our model with the help of Python so we start off by importing all the required packages.

```python
import pandas as pd
import requests
import numpy as np
import matplotlib.cm as cm
import matplotlib.colors as colors
import folium
from sklearn.cluster import KMeans
```

Package breakdown:


*Pandas* : To collect and manipulate data in JSON and HTMl and then data analysis

*requests* : Handle http requests

*matplotlib*  : Detailing the generated maps

*folium* : Generating maps of London and Paris

*sklearn* : To import Kmeans which is the machine learning model that we are using.

The approach taken here is to explore each of the cities individually, plot the map to show the neighbourhoods being considered and then build our model by clustering all of the similar neighbourhoods together and finally plot the new map with the clustered neighbourhoods. We draw insights and then compare and discuss our findings.

## 4.1 Data Collection
In the data collection stage, we begin with collecting the required data for the cities of Mumbai and London. We need data that has the postal codes, neighbourhoods and boroughs specific to each of the cities.

To collect data for Mumbai, we scrape the List of neighbourhoods in Mumbai wikipedia page to take the 1st table using the following code:

```python
url_mumbai = "https://en.wikipedia.org/wiki/List_of_neighbourhoods_in_Mumbai"
wiki_mumbai_url = requests.get(url_mumbai)
wiki_mumbai_data = pd.read_html(wiki_mumbai_url.text)
wiki_mumbai_data = wiki_mumbai_data[0]
wiki_mumbai_data
```

The data look like this :
![Mumbai_Data.png](https://raw.githubusercontent.com/amanattar/A-Tale-of-Two-Cities/main/assets/Mumbai_Data.png)

To collect data for London, we scrape the List of areas of London wikipedia page to take the 2nd table using the following code:

```python
url_london = "https://en.wikipedia.org/wiki/List_of_areas_of_London"
wiki_london_url = requests.get(url_london)
wiki_london_data = pd.read_html(wiki_london_url.text)
wiki_london_data = wiki_london_data[1]
wiki_london_data
```

The data looks like this:
![London_Data.png](https://raw.githubusercontent.com/amanattar/A-Tale-of-Two-Cities/main/assets/London_Data.png)

## 4.2 Data Preprocessing

For Mumbai, We replace the spaces with underscores in the title.The *borough* column has numbers within square brackets that we remove using:
```python
wiki_mumbai_data.rename(columns=lambda x: x.strip().replace(" ", "_"), inplace=True)
```

For London, We replace the spaces with underscores in the title.The *borough* column has numbers within square brackets that we remove using:

```python
wiki_london_data.rename(columns=lambda x: x.strip().replace(" ", "_"), inplace=True)
wiki_london_data['borough'] = wiki_london_data['borough'].map(lambda x: x.rstrip(']').rstrip('0123456789').rstrip('['))
```

## 4.3 Feature Selection

For both of our datasets, we need only the borough, neighbourhood, postal codes and geolocations (latitude and longitude). So we end up selecting the columns that we need by:

```python
df2 = wiki_london_data.drop( [ wiki_london_data.columns[0], wiki_london_data.columns[4], wiki_london_data.columns[5] ], axis=1)
```

## 4.4 Feature Engineering

Both of our Datasets actually contain information related to all the cities in the country. We can narrow down and further process the data by selecting only the neighbourhoods pertaining to 'Mumbai' and 'London'

```python
df1.columns = ["borough", "town","latitude","longitude"]

df2 = df2[df2['town'].str.contains('LONDON')]
```

For Mumbai data set we get the latitude and longitude in the code so we dont have to work

![Mumbai_Final_Data.png](https://raw.githubusercontent.com/amanattar/A-Tale-of-Two-Cities/main/assets/Mumbai_Final_Data.png)

Looking over our London dataset, we can see that we don't have the geolocation data. We need to extrapolate the missing data for our neighbourhoods. We perform this by leveraging the `ArcGIS API`. With the Help of `ArcGIS API` we can get the latitude and longitude of our London neighbourhood data. 

```python
from arcgis.geocoding import geocode
from arcgis.gis import GIS
gis = GIS()
```

Defining London arcgis geocode function to return latitude and longitude

```python
def get_x_y_uk(address1):
   lat_coords = 0
   lng_coords = 0
   g = geocode(address='{}, London, England, GBR'.format(address1))[0]
   lng_coords = g['location']['x']
   lat_coords = g['location']['y']
   return str(lat_coords) +","+ str(lng_coords)
```

Passing postal codes of london to get the geographical co-ordinates

```python
coordinates_latlng_uk = geo_coordinates_uk.apply(lambda x: get_x_y_uk(x))
```

We proceed with Merging our source data with the geographical co-ordinates to make our dataset ready for the next stage

```python
london_merged = pd.concat([df1,lat_uk.astype(float), lng_uk.astype(float)], axis=1)
london_merged.columns= ['borough','town','post_code','latitude','longitude']
london_merged
```

![London_Final_Data.png](https://raw.githubusercontent.com/amanattar/A-Tale-of-Two-Cities/main/assets/London_Final_Data.png)

*Note: Both the datasets have been properly processed and formatted. Since the same steps are applied to both the datasets of Mumbai and London, we will be discussing the code for only the London dataset for simplicity.*

## 4.5 Visualizing the Neighbourhoods of Mumbai and London

Now that our datasets are ready, using the `Folium` package, we can visualize the maps of Mumbai and London with the neighbourhoods that we collected.

Neighbourhood map of Mumbai:

![Neighbourhood_of_Mumbai.png](https://raw.githubusercontent.com/amanattar/A-Tale-of-Two-Cities/main/assets/Neighbourhood_of_Mumbai.png)

Neighbourhood map of London :

![Neighbourhood_of_London.png](https://raw.githubusercontent.com/amanattar/A-Tale-of-Two-Cities/main/assets/Neighbourhood_of_London.png)

Now that we have visualized the neighbourhoods, we need to find out what each neighbourhood is like and what are the common venue and venue categories within a 100om radius. 

This is where `Foursquare` comes into play. With the help of `Foursquare` we define a function which collects information pertaining to each neighbourhood including that of the name of the neighbourhood, geo-coordinates, venue and venue categories.

```python
LIMIT=400

def getNearbyVenues(names, latitudes, longitudes, radius=1000):
    
    venues_list=[]
    for name, lat, lng in zip(names, latitudes, longitudes):
        print(name)
            
        # create the API request URL
        url = 'https://api.foursquare.com/v2/venues/explore?&client_id={}&client_secret={}&v={}&ll={},{}&radius={}&limit={}'.format(
            CLIENT_ID, 
            CLIENT_SECRET, 
            VERSION, 
            lat, 
            lng, 
            radius,
            LIMIT
            )
            
        # make the GET request
        results = requests.get(url).json()["response"]['groups'][0]['items']
        
        # return only relevant information for each nearby venue
        venues_list.append([(
            name, 
            lat, 
            lng, 
            v['venue']['name'], 
            v['venue']['categories'][0]['name']) for v in results])

    nearby_venues = pd.DataFrame([item for venue_list in venues_list for item in venue_list])
    nearby_venues.columns = ['Neighbourhood', 
                  'Neighbourhood Latitude', 
                  'Neighbourhood Longitude', 
                  'Venue', 
                  'Venue Category']
    
    return(nearby_venues)
```

Resulting data look like:

![Mumbai_Neighbourhood_List.png](https://raw.githubusercontent.com/amanattar/A-Tale-of-Two-Cities/main/assets/Mumbai_Neighbourhood_List.png)

## 4.6 One Hot Encoding

Since we are trying to find out what are the different kinds of venue categories present in each neighbourhood and then calculate the top 10 common venues to base our similarity on, we use the One Hot Encoding to work with our categorical datatype of the venue categories. This helps to convert the categorical data into numeric data.

We won't be using label encoding in this situation since label encoding might cause our machine learning model to have a bias or a sort of ranking which we are trying to avoid by using One Hot Encoding.

We perform one hot encoding and then calculate the mean of the grouped venue categories for each of the neighbourhoods.

```python
# One hot encoding
Mumbai_venue_cat = pd.get_dummies(venues_in_Mumbai[['Venue Category']], prefix="", prefix_sep="")

# Adding neighbourhood to the mix
Mumbai_venue_cat['Neighbourhood'] = venues_in_Mumbai['Neighbourhood']

# moving neghbourhood colun to the first column
fixed_columns = [Mumbai_venue_cat.columns[-1]] + list(Mumbai_venue_cat.columns[:-1])

# Grouping and calculating the mean
Mumbai_grouped = Mumbai_venue_cat.groupby('Neighbourhood').mean().reset_index()
```

![Mumbai_Grouped_Data.png](https://raw.githubusercontent.com/amanattar/A-Tale-of-Two-Cities/main/assets/Mumbai_Grouped_Data.png)

## 4.7 Top Venues in the Neighbourhoods

In our next step, We need to rank and label the top venue categories in our neighborhood.

Let's define a function to get the top venue categories in the neighbourhood

```python
def return_most_common_venues(row, num_top_venues):
    row_categories = row.iloc[1:]
    row_categories_sorted = row_categories.sort_values(ascending=False)
    
    return row_categories_sorted.index.values[0:num_top_venues]
```

There are many categories, we will consider top 10 categories to avoid data skew. 

Defining a function to label them accurately

```python
num_top_venues = 10

indicators = ['st', 'nd', 'rd']

# create columns according to number of top venues
columns = ['Neighbourhood']
for ind in np.arange(num_top_venues):
    try:
        columns.append('{}{} Most Common Venue'.format(ind+1, indicators[ind]))
    except:
        columns.append('{}th Most Common Venue'.format(ind+1))

```

Getting the top venue categories in the neighbourhoods of Mumbai

```python
# create a new dataframe for Mumbai
neighborhoods_venues_sorted_mumbai = pd.DataFrame(columns=columns)
neighborhoods_venues_sorted_mumbai['Neighbourhood'] = Mumbai_grouped['Neighbourhood']

for ind in np.arange(Mumbai_grouped.shape[0]):
    neighborhoods_venues_sorted_mumbai.iloc[ind, 1:] = return_most_common_venues(Mumbai_grouped.iloc[ind, :], num_top_venues)

neighborhoods_venues_sorted_mumbai.head()
```

![Mumbai_10_Common_Venue.png](https://raw.githubusercontent.com/amanattar/A-Tale-of-Two-Cities/main/assets/Mumbai_10_Common_Venue.png)

## 4.8 Model Building - KMeans

Moving on to the most exicitng part - **Model Building!** We will be using KMeans Clustering Machine learning algorithm to cluster similar neighbourhoods together. We will be going with the number of clusters as 5.

```python
# set number of clusters
k_num_clusters= 5

Mumbai_grouped_clustering = Mumbai_grouped.drop('Neighbourhood',1)

# run k-means clustering
kmeans_mumbai = KMeans(n_clusters=k_num_cluster, random_state=0).fit(Mumbai_grouped_clustering)
```

Our model has labelled each of the neighbourhoods, we add the label into our dataset.

```python
neighborhoods_venues_sorted_mumbai.insert(0,'Cluster Labels',kmeans_mumbai.labels_ +1)
```

We then join Mumbai_merged with our neighbourhood venues sorted to add latitude & longitude for each of the neighborhood to prepare it for visualization.

```python
mumbai_data = df1

mumbai_data = mumbai_data.join(neighborhoods_venues_sorted_mumbai.set_index('Neighbourhood'), on = 'borough')

mumbai_data.head()
```

![Mumbai_Merged_data.png](https://raw.githubusercontent.com/amanattar/A-Tale-of-Two-Cities/main/assets/Mumbai_Merged_data.png)

## 4.9 Visualizing the clustered Neighbourhoods

Our data is processed, missing data is collected and compiled. The Model is built. All that's remaining is to see the clustered neighbourhoods on the map. Again, we use `Folium` package to do so.

We drop all the NaN values to prevent data skew

```python
mumbai_data_nonan = mumbai_data.dropna(subset=['Cluster Labels'])
```

Map of clustered neighbourhoods of Mumbai:

![Mumbai_Cluster_Map.png](https://raw.githubusercontent.com/amanattar/A-Tale-of-Two-Cities/main/assets/Mumbai_Cluster_Map.png)

Map of clustered neighbourhoods of London :

![London_Cluster_Map.png](https://raw.githubusercontent.com/amanattar/A-Tale-of-Two-Cities/main/assets/London_Cluster_Map.png)

### 4.9.1 Examining our Clusters

We could examine our clusters by expanding on our code using the `Cluster Labels` column:

Cluster 1

```python
mumbai_data_nonan.loc[mumbai_data_nonan['Cluster Labels'] == 1, mumbai_data_nonan.columns[[1] + list(range(5, mumbai_data_nonan.shape[1]))]]
```

Cluster 2

```python
mumbai_data_nonan.loc[mumbai_data_nonan['Cluster Labels'] == 2, mumbai_data_nonan.columns[[1] + list(range(5, mumbai_data_nonan.shape[1]))]]
```

Cluster 3

```python
mumbai_data_nonan.loc[mumbai_data_nonan['Cluster Labels'] == 3, mumbai_data_nonan.columns[[1] + list(range(5, mumbai_data_nonan.shape[1]))]]
```

Cluster 4

```python
mumbai_data_nonan.loc[mumbai_data_nonan['Cluster Labels'] == 4, mumbai_data_nonan.columns[[1] + list(range(5, mumbai_data_nonan.shape[1]))]]
```

Cluster 5

```python
mumbai_data_nonan.loc[mumbai_data_nonan['Cluster Labels'] == 5, mumbai_data_nonan.columns[[1] + list(range(5, mumbai_data_nonan.shape[1]))]]
```

# 5. Results and Discussion


Mumbai is relatively big in size geographically. It has a wide variety of cusines and eateries including French, Thai, Cambodian, Asian, Chinese etc. There are a lot of hangout spots including many Restaurants, Bars and Clubs.Different means of public transport in Mumbai which includes buses, taxies, trains and rikshaws.For leisure and sight seeing, there are a lot of Plazas, Trails, Parks, Historic sites, clothing shops, Art galleries. 

Overall, Mumbai seems like the relaxing vacation spot with a mix of lakes, historic spots and a wide variety of cusines to try out.

The neighbourhoods of London are very mulitcultural. There are a lot of different cusines including Indian, Italian, Turkish and Chinese. London seems to take a step further in this direction by having a lot of Restaurants, bars, juice bars, coffee shops, Fish and Chips shop and Breakfast spots. It has a lot of shopping options too with that of the Flea markets, flower shops, fish markets, Fishing stores, clothing stores. The main modes of transport seem to be Buses and trains. For leisure, the neighbourhoods are set up to have lots of parks, golf courses, zoo, gyms and Historic sites.

Overall, the city of London offers a multicultural, diverse and certainly an entertaining experience.


# 6. Conclusion

The purpose of this project was to explore the cities of Mumbai and London and see how attractive it is to potential tourists and migrants. We explored both the cities based on their extrapolated the common venues present in each of the neighbourhoods finally concluding with clustering similar neighbourhoods together.

We could see that each of the neighbourhoods in both the cities have a wide variety of experiences to offer which is unique in it's own way. The cultural diversity is quite evident which also gives the feeling of a sense of inclusion.

Both LOndon and Mumbai seem to offer a vacation stay or a romantic gateaway with a lot of places to explore, beautiful landscapes and a wide variety of culture.Overall, it's upto the stakeholders to decide which experience they would prefer more and which would more to their liking.
