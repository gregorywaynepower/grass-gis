# Raster Analysis 

This section introduces fundamental raster operations in GRASS GIS. You'll learn how to perform map algebra, clipping rasters to a boundary, statistical summaries, LULC masking, raster algebra, and temporal aggregation.

---

### Clip Raster to a Boundary

```bash
# Set region to match vector boundary
g.region vector=IndiaBoundary align=chirps_pcp_a_2024 -p

# Create a mask using the boundary
r.mask vector=IndiaBoundary

# Clip the raster
r.mapcalc "chirps_pcp_a_2024_clipped = chirps_pcp_a_2024"

# Remove the mask after clipping
r.mask -r
```

### Get Raster Statistics (min, max, mean, median)
```bash
# Basic stats:
r.univar map=chirps_pcp_a_2024_clipped -g

# For median and advanced stats:
r.stats -aCn input=chirps_pcp_a_2024_clipped

# For raster category counts, area and values:
r.report map=chirps_pcp_a_2024_clipped units=h,c,p
```



### LULC Masking (e.g., Mask only Cropland Areas)
```bash
# Assuming you have an LULC raster (esa_lulc_2021) where value 40 = cropland:
r.mapcalc "cropland_mask = if(esa_lulc_2021 == 40, 1, null())"
r.mask raster=cropland_mask

# Apply it to another raster:
r.mapcalc "chirps_pcp_a_2024_cropland = chirps_pcp_a_2024"

# Then remove the mask:
r.mask -r

```


### Raster Calculation
```bash
r.mapcalc "output_raster = input_raster_a /input_raster_b"
```

### Temporal Raster Analysis
```bash
# Mean over years
r.series input=chirps_pcp_a_2021,chirps_pcp_a_2022,chirps_pcp_a_2023,chirps_pcp_a_2024 output=chirps_pcp_a_mean_2021_2024 method=average

# Max or Min over years
r.series input=hirps_pcp_a_2021,chirps_pcp_a_2022,chirps_pcp_a_2023,chirps_pcp_a_2024 output=chirps_pcp_a_max_2021_2024 method=maximum

# Aggregate Monthly to Annual Raster: Annual Sum
r.series input=$(g.list type=raster pattern="chirps_pcp_m_2023_*" separator=comma) output=chirps_pcp_a_2023_sum method=sum

# Aggregate Monthly to Annual Raster: Annual Mean
r.series input=$(g.list type=raster pattern="chirps_pcp_m_2023_*" separator=comma) output=chirps_pcp_a_2023_mean method=average


```


