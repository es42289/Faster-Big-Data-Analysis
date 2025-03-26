# Faster Big Data Analysis

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

These tools include:
- Dask
- PySpark
- parquet file format