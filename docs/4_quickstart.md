# GRASS GIS Quickstart
When launching GRASS GIS for the first time, you will open a default project "world_latlog_wgs84" where you can find a map layer called "country_boundaries" showing a world map in the WGS84 coordinate system.


![GRASS Location Structure](../assets/images/grass_start.png)

---

## Interface Overview

The GRASS GUI has several panels and tools:

- **Layer Manager**: Controls loaded map layers.
- **Map Display**: Shows raster/vector data.
- **Data Catalog**: Displays the GRASS data hierarchy.
- **Console**: Run GRASS commands directly.
- **Modules Search Bar**: Search for specific tools and commands.

---

## Getting Started with GRASS GIS

This section explains how to set up your working environment in GRASS GIS and import your geospatial data using command line


### 1. Create a GRASS Database Directory

Open a terminal and create a directory that will act as the GRASS GIS database:

```bash
# Linux, Mac, *BSD, ...:
mkdir -p ~/grassdata

# Windows
mkdir D:\grassdata
```
>  This directory will store all Locations and Mapsets.


This is the root directory that will contain all your GRASS locations and mapsets.


### 2. Launch GRASS GIS
Open GRASS GIS normally **without a full path** so you can create your first Location and Mapset using the GUI.

Search for GRASS GIS in the Start Menu and open it normally.

In the startup screen:

- Set GIS Database to: ~/grassdata or D:\grassdata
- Click on New next to Location
- Choose one of the following:
    - Create with EPSG Code (e.g., 4326 for WGS84)
    - Import an existing raster/vector to define projection
- Once the Location is created, you will be prompted to create your first Mapset (e.g., chirps_pcp)

### 3. Create a New Location using command line
Use grass with the -c flag to create a new Location based on a coordinate reference system (CRS).
```bash

# Linux, Mac, *BSD, ...:
grass -c EPSG:4326 ~/grassdata/myproject

# Windows
grass -c EPSG:4326:3 D:\grassdata\myproject

```
This command:

- Creates a new Location named "myproject"
- Uses EPSG:4326 (WGS 84 coordinate system)
- Initializes the default PERMANENT mapset


### 4. Create and Start a New Mapset
Once your Location (e.g., `myproject`) is created, you can add a new **Mapset** to organize your analysis.
```bash
# create a new mapset 
g.mapset -c mapset=chirps_pcp location=myproject
```

> Notes:
-  Here, we are creating a mapset named chirps_pcp, which stands for CHIRPS Precipitation Data. We'll use this mapset for importing and analyzing precipitation datasets in upcoming sections.
Download ***CHIRPS Annual Precipitation*** Data (2020–2024):  [https://data.chc.ucsb.edu/products/CHIRPS/v3.0/annual/global/tifs/](https://data.chc.ucsb.edu/products/CHIRPS/v3.0/annual/global/tifs/)

### 5. Launch GRASS into the new mapset:
```bash
# Mac/Linux
grass --text ~/grassdata/myproject/chirps_pcp

# Windows
grass --text D:\grassdata\myproject\chirps_pcp
```


> Notes:
- The new mapset must be inside an existing location.


### Additional Mapset Operations
List all available mapsets
```bash
g.mapsets -l
```

Switch to a different mapset within the same location
```bash
g.mapset mapset=ind_annual_data  
```


### Sample Data
To follow along with the upcoming tutorials, please download the following sample datasets:

- **India Boundary**  
  ➡️ [Download IndiaBoundary.geojson](https://amanchry.github.io/grass-gis/assets/IndiaBoundary.geojson)

- **India States**  
  ➡️ [Download IndiaStates.geojson](https://amanchry.github.io/grass-gis/assets/IndiaStates.geojson)



In this section, we:

- Set up the GRASS GIS database structure
- Created a Location and Mapset
- Prepared to work with CHIRPS precipitation data

👉 In the next section, we’ll learn how to import raster and vector data into your mapset and begin your analysis!