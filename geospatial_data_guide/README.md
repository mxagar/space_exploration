# Geospatial Data: A Guide

This document contains a guide on how to visualize and work with geospatial data, based on two courses from Datacamp:

- [Visualizing Geospatial Data in Python](https://app.datacamp.com/learn/courses/visualizing-geospatial-data-in-python)
- [Working with Geospatial Data in Python](https://app.datacamp.com/learn/courses/working-with-geospatial-data-in-python)

Mikel Sagardia, 2023.  
No guarantees.

## Table of Contents

- [Geospatial Data: A Guide](#geospatial-data-a-guide)
  - [Table of Contents](#table-of-contents)
  - [1. Building 2-Layer Maps](#1-building-2-layer-maps)
    - [1.1 Scatterplots: Longitude and Latitude](#11-scatterplots-longitude-and-latitude)
    - [1.2 Geometries and Shapefiles](#12-geometries-and-shapefiles)
    - [1.3 Combining 2 Layers: Geometry Plots and Scatterplots](#13-combining-2-layers-geometry-plots-and-scatterplots)
  - [2. Creating and joining GeoDataFrames](#2-creating-and-joining-geodataframes)


## 1. Building 2-Layer Maps

In this section scatterplots and shapely geometry plots are combined to build 2-layer map visualizations.

### 1.1 Scatterplots: Longitude and Latitude

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

### 1.2 Geometries and Shapefiles

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

### 1.3 Combining 2 Layers: Geometry Plots and Scatterplots

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

## 2. Creating and joining GeoDataFrames

