# Zonal Statistics

Zonal statistics summarize raster values (e.g., mean, sum, count) within vector zones such as administrative boundaries, watersheds, or land parcels. This guide explains how to perform zonal statistics in GRASS GIS using a GeoJSON boundary file and a raster map.

---

### Import Vector Boundary (GeoJSON)

Use `v.in.ogr` to import a GeoJSON file:

```bash
v.in.ogr input=/path/to/boundaries.geojson output=zones
```
> 🔍 Check if the projection of your GeoJSON matches the current GRASS location. If not, reproject the vector (see section below).

### Import Raster Map

Use r.import to bring in a GeoTIFF or similar raster file:

```bash
r.import input=/path/to/raster.tif output=my_raster

```


### Set Computational Region

Match the region to your raster map:

```bash
g.region raster=my_raster -p

# (Optional) Expand region to fully cover vector zones:
g.region vector=zones align=my_raster

```



### Perform Zonal Statistics with v.rast.stats

Use the vector map (zones) to compute stats from the raster (my_raster) for each polygon:

```bash
v.rast.stats map=zones raster=my_raster column_prefix=stats method=average,sum,count
```

- column_prefix: Adds columns like stats_mean, stats_sum, etc.
- method: Can be one or more of: average, sum, count, min, max, stddev

This updates the attribute table of the vector map with the calculated values.


### View Results

Display the updated attribute table:

```bash
v.db.select map=zones

```



### Optional: Export the Results to GeoJSON or Shapefile

```bash
# Export GeoJSON
v.out.ogr input=zones output=zones_stats.geojson format=GeoJSON

# or Export Shapefile
v.out.ogr input=zones output=zones_stats.shp format=ESRI_Shapefile

# or Export Attribute Table to CSV
v.db.select map=zones separator=comma file=zones_stats.csv

```


### (Optional) Reproject GeoJSON if Needed

If your GeoJSON CRS doesn't match the current GRASS location, you can either:

- Reproject using GDAL before importing, or
- Import it into a matching GRASS location, and use v.proj

```bash
# Run this inside your target location
v.proj location=source_location mapset=PERMANENT input=zones output=zones_reprojected

```




---

### Python Script: Annual CHIRPS PCP
This Python script calculates zonal statistics of annual CHIRPS precipitation data (2020–2024) over Indian states using GRASS GIS and export results as csv.

- **India States**  
  ➡️ [Download IndiaStates.geojson](https://amanchry.github.io/grass-gis/assets/IndiaStates.geojson)


```bash
import os
import subprocess
import grass.script as gs
from grass.pygrass.modules.shortcuts import general as g
from grass.pygrass.modules.shortcuts import raster as r
from grass.pygrass.modules.shortcuts import display as d
from grass.pygrass.modules.shortcuts import vector as v
from grass.pygrass.gis import *
import grass.script.setup as gsetup


# Main function
def main(gisdb, location, mapset): 

    geojson_file = 'IndiaStates.geojson'
    output_csv_path=f"IndiaStates_chirps_pcp_zonalstats.csv"
    start_yr = '2020'
    end_yr = '2024'

    os.environ['GISDBASE'] = gisdb
    os.environ['LOCATION_NAME'] = location

    # Check if mapset exists; if not, create it
    mapset_path = os.path.join(gisdb, location, mapset)
    if not os.path.exists(mapset_path):
        print(f"Mapset '{mapset}' does not exist. Creating new mapset...")
        # Create the new mapset
        gs.run_command('g.mapset', flags='c', mapset=mapset, location=location, dbase=GISDBASE)
    else:
        print(f"Mapset '{mapset}' already exists.")

    # Initialize GRASS session
    gsetup.init(gisdb, location, mapset)
    print(f"GRASS GIS session initialized in {gisdb}/{location}/{mapset}")


    vector_name = os.path.splitext(os.path.basename(geojson_file))[0]

    v.import_(input=geojson_file, output=vector_name, overwrite=True)

    g.mapsets(mapset="chirps_pcp", operation="add")

    gs.run_command('g.region', vector=vector_name, res=0.00292)


    for year in range(int(start_yr), int(end_yr) + 1):
        raster_name=f"chirps_pcp_resamp_a_{year}"
        gs.run_command('v.rast.stats', map=vector_name, raster=raster_name, 
            column_prefix=raster_name, 
            method='average', 
            overwrite=True)

    stats_output = gs.read_command(
        'v.db.select', map=vector_name, format="csv", overwrite=True
    )



    with open(output_csv_path, 'w') as f:
        f.write(stats_output)



    print(f"zonalstats exported successfully: {vector_name}")




if __name__ == '__main__':
    GISDBASE = "/Users/amanchaudhary/grassdata"
    LOCATION_NAME = "myproject"
    MAPSET = "zonalstats"                 

    # Call the main function
    main(GISDBASE, LOCATION_NAME, MAPSET)





```


   