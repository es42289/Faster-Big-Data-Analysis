# Faster Big Data Analysis

I am working on a project which requires the analysis of a ~4.5GB FileGDB.
Working with the file in ArcGIS Pro or with a standard Python approach is cumbersome.

ArcGIS Pro is an amazing tool but there are many reasons why one would want to code a solution rather than rely on a GUI.
- Maybe you don't have a license
- Maybe you are forced to use a VM which is slow
- Maybe you are developing an Open Source solution
- Maybe you are using file formats not compatible with ArcGIS Pro

For Python developers there are some packages which are used widely for geoprocessing:
- geopandas
- fiona
- shapely

These packages have an active community, rich documentation and tutorials galore to support developers. 

In my expereience I have found that when a dataset reaches a certain size; these tools get bogged down. 
Analysis can become so slow that it can stop you in your tracks or even become impossible due to RAM limitations.

There are some modern solutions to these problems which can GREATLY speed up the read, analysis and write time of results.

The tools I am going to present are tools I've used successfully in the past to great success. I will present time and memory statistics to show the massive increase in performance achieved from these alternative methods.

## Standard Methodology
Below shows a methodology that works great for loading smaller datasets. The FileGDB is called into a GeoDataFrame and a layer is specified.
```python
import geopandas as gpd
gdf = gpd.read_file(r"nlsdb_03032025.gdb", layer = 'CaseLands_03032025_1330')
gdf.head()
```
I ran this code on my laptop which has:
- 32GB RAM
- Intel Core i7-12700H (14 Cores)

There are **(1,756,089) polygons** with **(33) attributes** in the resultant GeoDataFrame.

The process took **29m 36.6s** and is occupying **9GB** of my RAM. This is slow and stopped me in my tracks for half an hour just waiting for the load...not to mention leaving me little remaining memory.

## Enhanced Methodology
Parquet files originated from the Apache project which developed and supports the most popular distributed computing framework which includes **Hadoop** and **Spark**.

This file format GREATLY improves read and write times for dataframes. Let's try it out and see the performance enhancements.

### 1. Downloading GDAL
GDAL is an open source command line tool which can be used to read, query, 


