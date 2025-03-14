# Faster Big Data Analysis

I am working on a project which requires the analysis of a ~4.5GB FileGDB. This GDB contains a feature class which needs to be joined to a CSV which is 3.5 GB.

These files sizes are not uncommon and do not even constitute big data to most, but it can present challenges to an analysist tasked with analysis.

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
GDAL is an open source command line tool which can be used to read, query, convert or even perform geoprocessing on geospatial files. I do recommend becoming familiar with this tool. 

This is the tool we will use to convert our layer nested inside a FileGDB into a Parquet file. 

A (mostly) failproof way to install GDAL for a Windows user is via OSGeo4W.
* Go to the official OSGeo4W website: https://www.osgeo.org/projects/osgeo4w/
* Download OSGeo4W Network Installer (osgeo4w-setup.exe).
* Run the Installer
* Choose "Advanced Install" (to get the latest stable version).
* Select "gdal" from the list of available packages.
* Finish the installation.

If you're a Python using Anaconda as your command line interface package and virtual environment handler, then you can download GDAL via:
```python
conda install -c conda-forge gdal
```
\* GDAL can have dependency issues so I recommend installing into a purpose built virtual environment via `conda create --name geo`

### 2. Converting to Parquet
If you've downloaded OSGeo4W then you can open this tool from by searching for it in your programs. If you've installed it via the conda forge method then you can open an Anaconda prompt and activate the virtual environment you've installed it into. 

Now, in the CLI `cd` to the file location of the gdb (optional, allows to avoid long paths reducing code readability).

From here we can run the following code to convert the multi-polygon feature layer to Parquet file format:
```python
ogr2ogr -f Parquet "CaseLands_03032025_1330.parquet" "nlsdb_03032025.gdb" CaseLands_03032025_1330
```

This converted the file in ~2 minutes and the resulting file is ~681 MB.

Here we see the first benefit of the Parquet file, smaller file size. If we extract the feature class to different file types we can see the various file sizes of each for comparison:
- Parquet
    - 681 MB
- FileGDB
    - 947 MB (140% the size of parquet)
- GeoJSON
    - 3.9 GB (Yikes!)
- KML
    - 4.5 GB (Ouch!)

## Loading the Data
Let's see what it takes to load the parquet file instead of the FileGDB using Geopandas.

```python
import geopandas as gpd

gdf = gpd.read_parquet(r"2025_03-03_NLSDB.gdb\CaseLands_03032025_1330.parquet")
```
Running the above code loaded the GeoDataFrame in 8.4s! Holy crap that is some time savings. This is a **21,750%** increase in performace. My RAM is only using 671 MB as well (compared to 9 GB using FileGDB).