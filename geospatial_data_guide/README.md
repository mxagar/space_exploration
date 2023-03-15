# Geospatial Data: A Guide

This document contains a guide on how to visualize and work with geospatial data, based on two courses from Datacamp:

- [Working with Geospatial Data in Python](https://app.datacamp.com/learn/courses/working-with-geospatial-data-in-python)
- [Visualizing Geospatial Data in Python](https://app.datacamp.com/learn/courses/visualizing-geospatial-data-in-python)

The datasets used during the courses are in these folders:

- [`../data/visualize_geodata`](../data/visualize_geodata)
- [`../data/work_geodata`](../data/work_geodata)

Additionally, 

- some exercises/tests tried on my own are located in [`lab`](./lab)
- and the slides (not committed) are in [`slides`](./slides).

Mikel Sagardia, 2023.  
No guarantees.

## Table of Contents

- [Geospatial Data: A Guide](#geospatial-data-a-guide)
  - [Table of Contents](#table-of-contents)
  - [1. Introduction to Geospatial Vector Data](#1-introduction-to-geospatial-vector-data)
  - [5. Building 2-Layer Maps](#5-building-2-layer-maps)
    - [5.1 Scatterplots: Longitude and Latitude](#51-scatterplots-longitude-and-latitude)
    - [5.2 Geometries and Shapefiles: Geopandas](#52-geometries-and-shapefiles-geopandas)
    - [5.3 Combining 2 Layers: Geometry Plots and Scatterplots](#53-combining-2-layers-geometry-plots-and-scatterplots)
  - [6. Creating and Joining GeoDataFrames](#6-creating-and-joining-geodataframes)
    - [6.1 GeoJSON and Plotting with Geopandas](#61-geojson-and-plotting-with-geopandas)
    - [6.2 Projections and Coordinate Reference Systems](#62-projections-and-coordinate-reference-systems)
    - [6.3 Spatial Joins](#63-spatial-joins)
  - [7. GeoSeries and Folium](#7-geoseries-and-folium)
  - [8. Creating a Choropleth Building Permit Density in Nashville](#8-creating-a-choropleth-building-permit-density-in-nashville)

## 1. Introduction to Geospatial Vector Data

We have mainly two types of geodata:

- Raster: grid with values, e.g., altitude or images (RGB, heat, anything).
- Vector: points, lines and polygons.

![Raster vs. vector](./pics/raster_vs_vector.jpg)

The course focuses on vector data, i.e.,

- Points: `(x,y)` (e.g., cities on a map).
- Lines; `[(x,y), (x,y), (x,y), ...]` (e.g., rivers on a map).
- Polygons (and multipolygons): they close and contain an area (e.g., countries on a map).

![World map: points, lines, polygons](./pics/world_point_line_polygons.jpg)

**IMPORTANT**: This section has an associated notebook where I try the code snippets summarized here:

[`lab/01_Intro_GeoPandas.ipynb`](./lab/01_Intro_GeoPandas.ipynb)

### 1.1 Plotting: Scatterplots with Background

We can plot points with regular (scatter) plots and we can add background maps with the package [`contextily`](https://contextily.readthedocs.io/en/latest/).

```python
# Import pandas and matplotlib
import pandas as pd
import matplotlib.pyplot as plt
import contextily

# Read the restaurants csv file
restaurants = pd.read_csv("paris_restaurants.csv")

# Inspect the first rows of restaurants
print(restaurants.head())
#                                               type           x          y
# 0                             Restaurant européen  259641.692  6.252e+06
# 1                Restaurant traditionnel français  259572.340  6.252e+06

# Make a plot of all points: scatterplot
fig, ax = plt.subplots()
ax.plot(restaurants.x, restaurants.y, 'o')
plt.show()

# To add a background map: contextily
# !pip install contextily
# It automatically downloads the tile of the required region
# But the X & Y or Long & Lat need to be correct
fig, ax = plt.subplots()
ax.plot(restaurants.x, restaurants.y, 'o', markersize=1)
contextily.add_basemap(ax)
plt.show()
```

### 1.2 GeoPandas Basics

GeoPandas is an extension to Pandas which can contain geodata. It has always a `geometry` column, which contains often the polygons of given regions/areas. The rest of the columns are the attributes of the elements that represent those polygons.

Note that:

- The dataframes become of type `GeoDataFrame`. They have special properties and methods, like `plot()`, which nicely plots the geometries.
- The `geometry` column is of type `GeoSeries`, equivalent to the Pandas `Series`, but for geometrical data; it has several special attributes/properties, like `area`.

```python
import geopandas as gpd

# Read the Paris districts dataset: GeoPackage file
districts = gpd.read_file('paris_districts.gpkg')

# Inspect the first rows
print(districts.head())

# Make a quick visualization of the districts
districts.plot()

type(districts) # geopandas.geodataframe.GeoDataFrame
type(districts.geometry) # geopandas.geoseries.GeoSeries

# Area attribute
districts.geometry.area

# Convert CSV to GeoPandas
df = pd.read_csv("paris_restaurants.csv")
restaurants = gpd.GeoDataFrame(df, geometry=gpd.points_from_xy(df.x, df.y))

# Calculate the number of restaurants of each type
type_counts = restaurants.groupby('type').size()

# Take a subset of the African restaurants
african_restaurants = restaurants[restaurants['type'] == 'African restaurant']
```

### 1.3 Visualizing Spatial Data

If we use `.plot()` on a `GeoDataFrame`, the `geometry` column will be plotted, i.e., the polygons contained in it. We can further control the plots:

- If we pass the `color` attribute, we can specify the color for all geometries.
- `column`: we specify the values of which other column should be plotted in the displayed polygons.
- `cmap`: [color maps](https://matplotlib.org/stable/tutorials/colors/colormaps.html).
- We can overlay other plot son the plotted polygons (e.g., scatterplots or points) by using the `ax` option.
- We can add a legend and control it passing a dictionary via `legend_kwds`.
- We can use any pandas and matplotlib functions, basically.

```python
# Add a population density column
# Watch out: area is in m2, we want to pass it to km2 -> /10^6
districts['population_density'] = (districts.population / districts.geometry.area) * 10**6

# Make a plot of the districts colored by the population density
districts.plot(column = 'population_density', cmap = 'Reds', legend=True, figsize=(10, 5))

# Take a subset of the African restaurants
african_restaurants = restaurants[restaurants['type'] == 'African restaurant']

# Make a multi-layered plot
fig, ax = plt.subplots(figsize=(10, 10))
restaurants.plot(ax=ax, color='grey')
african_restaurants.plot(ax=ax, color='red')
# Remove the box, ticks and labels
ax.set_axis_off()
plt.show()
```

## 2. Spatial Relationships

**IMPORTANT**: This section has an associated notebook where I try the code snippets summarized here:

[`lab/02_Spatial_Relationships.ipynb`](./lab/02_Spatial_Relationships.ipynb)



## 3. Projecting and Transforming Geometries


## 4. Case Study: Artisanal Mining Sites


---

## 5. Building 2-Layer Maps

In this section scatterplots and shapely geometry plots are combined to build 2-layer map visualizations.

### 5.1 Scatterplots: Longitude and Latitude

Geospatial data comes often in longitude (=x) and latitude (y). These values can be plotted with a scatterplot; example of a scatterplot

```python
# Import matplotlib.pyplot
import matplotlib.pyplot as plt

# Scatterplot 3
plt.scatter(df.father_height, df.son_height,  c = 'yellow', edgecolor = 'darkblue')
plt.grid()
plt.xlabel('father height (inches)')
plt.ylabel('son height (inches)')
plt.title('Son Height as a Function of Father Height')

# Show your plot
plt.show()
```

Longitude and latitude, if they have been processed, often come in a tuple; we usually extract the values and create one columns for each value:

```python
# print the first few rows of df 
print(df.head())

# extract latitude to a new column: lat
df['lat'] = [loc[0] for loc in df.Location]

# extract longitude to a new column: lng
df['lng'] = [loc[1] for loc in df.Location]

# print the first few rows of df again
print(df.head())
```

### 5.2 Geometries and Shapefiles: Geopandas

We have two important packages to deal with geometries:

- [Shapely](https://shapely.readthedocs.io/en/stable/): Python package for manipulation and analysis of planar geometric objects.
- [GeoPandas](https://geopandas.org/en/stable/): extends Pandas to be able to manipulate Shapely geomatries.

A shapefile contains these components/files:

- `my_map.shp`: geometry
- `my_map.dbf`: attributes of geometry
- `my_map.shx`: links the attributes to the geometry

The geometries can have these elements:

- Points.
- Lines: segments consisting of joined Points (they can be curvy).
- Polygon: closed Lines which contain an area.



```python
# Import geopandas
import geopandas as gpd
import matplotlib.pyplot as plt

# Read in the services district shapefile and look at the first few rows.
df = gpd.read_file(shapefile_path)
print(df.head())
#    area_sq_mi  objectid                       name                                           geometry
# 0       198.0       0.0    Urban Services District  POLYGON ((-86.68681 36.28671, -86.68706 36.285...
# 1       327.0       4.0  General Services District  MULTIPOLYGON (((-86.56776 36.03424, -86.56783 ...

# Print the contents of the service districts geometry in the first row
print(df.loc[0, 'geometry'])
# POLYGON ((-86.68680500011935 36.28670500013504, -86.68706099969657 ...

# Plot the Service Districts without any additional arguments
df.plot()
plt.show()

# Plot the Service Districts, color them according to name, and show a legend
df.plot(column = 'name', legend = True)
plt.show()
```

![Geopandas Plot](./pics/geopandas_plot.jpg)

### 5.3 Combining 2 Layers: Geometry Plots and Scatterplots

Here, we have two dataframes: `service_district` with geometries, `chickens` with longitude and latitude coordinates. We can overlay the plots.

```python
# Import geopandas
import geopandas as gpd
import matplotlib.pyplot as plt

# Plot the service district shapefile
service_district.plot(column="name", legend=True)

# Add the chicken locations
plt.scatter(x=chickens.lng, y=chickens.lat, c="black", edgecolor = 'white')

# Add labels and title
plt.title('Nashville Chicken Permits')
plt.xlabel("longitude")
plt.ylabel("latitude")

# Add grid lines and show the plot
plt.grid()
plt.show()
```

![Two-Layer Plot](./pics/two_layer_plot.jpg)

## 6. Creating and Joining GeoDataFrames

Besides Shapely geometry files, we have GeoJSON files, which can be read by Geopandas, too. They have these advantages:

- They integrate everything in a JSON file.
- They are easier to handle, since JSON is a human readable/friendlier format.

### 6.1 GeoJSON and Plotting with Geopandas

GeoJSON files con contain the same structures as Shapely files. They can represent maps as

- Vector data: points, lines, polygons, multi-polygons.
- Raster data: pixel graphics, grids.

Geopandas requires some packages to deal with these structures; we have these options:

- Fiona: support for OGR, which translates vector data.
- GDAL & OGR: which translate raster data (GDAL) and vector data (OGR).

We'll deal with vector data.

An important aspect when plotting are color maps, `cmap`, because they help understand the polygons in one way or another: [Choosing Colormaps in Matplotlib](https://matplotlib.org/stable/tutorials/colors/colormaps.html). Color maps can be:

- Qualitative: miscellaneous colors, no ordering, for categorical data.
- Sequential: numerical information, with ordering/degree.
- Diverging
- Cyclic

```python
import geopandas as gpd
import matplotlib.pyplot as plt

# Read in the neighborhoods geojson file
neighborhoods = gpd.read_file(neighborhoods_path)

# Print the first few rows of neighborhoods
print(neighborhoods.head())
#                     name                                           geometry
# 0  Historic Buena Vista  MULTIPOLYGON (((-86.79511 36.17576, -86.79403 ...
# 1        Charlotte Park  MULTIPOLYGON (((-86.87460 36.15758, -86.87317 ...

# Plot the neighborhoods, color according to name and use the Dark2 colormap
neighborhoods.plot(column = 'name', cmap = 'Dark2')

# Show the plot.
plt.show()
```

![GeoJSON Plot](./pics/geojson_plot.jpg)

We can add a legend and manipulated its shape:

```python
import geopandas as gpd
import matplotlib.pyplot as plt

# Optional legend manipulation
leg_kwds={'title':'District Number',
          'loc': 'upper left',
          'bbox_to_anchor':(1, 1.03),
          'ncol':3}

council_dists.plot(column='district',
                   cmap='Set3',
                   legend=True,
                   legend_kwds=leg_kwds) # optional

plt.title('Council Districts')

plt.show()
```

### 6.2 Projections and Coordinate Reference Systems

To create a geo-dataframe, we need:

- A `geometry` column with geometry data.
- Coordinate Reference System (CRS) data. The most common system is `EPSG:3857`, which has units in meters and is used in Google maps and most other apps.

In addition to that we need to account for the type of **projection**, e.g., Mercator or derivates.

In the following code snippets, geo-dataframes are constructed:

```python
from shapely.geometry import Point
import geopandas as gpd

# schools
# School Name                 Latitude     Longitude
# A. Z. Kelley Elementary     36.021       -86.658
# Alex Green Elementary       36.252       -86.832
# ...

schools['geometry'] = schools.apply(lambda x: Point((x.Longitude, x.Latitude)), axis = 1)
# geometry
# POINT (-86.658 36.021)
# POINT (-86.832 36.252)
# ...

# Construct a geo-dataframe
# schools_geo seems to be the same as schools, but it's another data structure
schools_crs = {'init':  'epsg:4326'}
schools_geo = gpd.GeoDataFrame(schools,
                               crs = schools_crs,
                               geometry = schools.geometry)
schools_geo.head(1)

# We can convert geometry from decimal degrees to meters if we have a geo-dataframe
# and thus, we can measure distances!
# Here, we convert the crs to epsg:3857
schools_geo.geometry = schools_geo.geometry.to_crs(epsg = 3857)

# We can get the CRS as follows
print(schools_geo.crs)

```

### 6.3 Spatial Joins

Spatial joins make possible many geospatial analyses; for instance, if we have two geometry maps of a city, one with neighborhoods/districts and the other with public school points and their regions, we can ask: do they match? which districts have several schools or which schools contain several districts?

Spatial joins are done with `gpd.sjoin()`, which requires an operation, `op`:

- `'intersects'`: all points/data which intersect both dataframes are returned.
- `'contains'`: data contained in the first/left dataframe
- `'within'`: data contained in the second/right dataframe
- If we don't pass an operation, both datasets are shown.

Since in one geometry column can be district polygons and the other schools points, a join can filter points (schools) within polygons (districts). Thus, we're performing geometry operations.

![Intersect](./pics/intersect.jpg)

![Contains](./pics/contains.jpg)

```python
import geopandas as gpd

# Spatially join art_geo and neighborhoods 
neighborhood_art = gpd.sjoin(art_geo, neighborhoods, op = 'intersects')

# Print the shape property of art_intersect_neighborhoods
print(neighborhood_art.shape)
# (40, 13)

# Get name and title from neighborhood_art and group by name
neighborhood_art_grouped = neighborhood_art[['name', 'title']].groupby('name')

# Aggregate the grouped data and count the artworks within each polygon
print(neighborhood_art_grouped.agg('count').sort_values(by = 'title', ascending = False))
#                           title
# name                           
# Urban Residents              22
# Lockeland Springs             3
# ...

# Create urban_art from neighborhood_art where the neighborhood name is Urban Residents
urban_art = neighborhood_art.loc[neighborhood_art.name == "Urban Residents"]

# Get just the Urban Residents neighborhood polygon and save it as urban_polygon
urban_polygon = neighborhoods.loc[neighborhoods.name == "Urban Residents"]

# Plot the urban_polygon as ax 
ax = urban_polygon.plot(color = 'lightgreen')

# Add a plot of the urban_art and show it
urban_art.plot(ax = ax, column = 'type', legend = True);
plt.show()
```

![Two layer plot](./pics/2_layer_plot.jpg)
