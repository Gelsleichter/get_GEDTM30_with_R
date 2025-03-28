# Handling GEDTM30 Data with R Terra Package

This script demonstrates how to load, visualize, crop, and save spatial data from [GEDTM30](https://github.com/openlandmap/GEDTM30/tree/main) using the `R` `terra` package without downloading the entire file.

Available products:
- Ensemble Digital Terrain Model
- Standard Deviation EDTM
- Difference from Mean Elevation
- Geomorphons
- Hillshade
- LS Factor
- Maximal Curvature
- Minimal Curvature
- Negative Openness
- Positive Openness
- Profile Curvature
- Ring Curvature
- Shape Index
- Slope in Degree
- Specific Catchment Area
- Spherical Standard Deviation of the Normals
- Tangential Curvature
- Topographic Wetness Index

The product list links are [here](https://github.com/openlandmap/GEDTM30/blob/main/metadata/cog_list.csv).

## Code

```R
# Terra package to load and handle raster data
library(terra)

# Product link (for DTM - digital terrain model)
dtm_url <- "https://s3.opengeohub.org/global/edtm/legendtm_rf_30m_m_s_20000101_20231231_go_epsg.4326_v20250130.tif"

# Read as raster with vsicurl enable 
# vsicurl: allows on-the-fly reading without download of the entire file. Source: https://gdal.org/en/stable/user/virtual_file_systems.html#vsicurl-http-https-ftp-files-random-access 
dtm <- terra::rast(dtm_url, vsi= T) 
dtm

# Quick check with plot
terra::plot(dtm)

# Define the extent for area of interest
ext <- terra::ext(-48.9, -48.3, -27.9, -27.1) # xmin, xmax, ymin, ymax

# Convert extent to polygon
ext_poly <- terra::as.polygons(ext, crs= terra::crs(dtm))

# Plot the extent to see if it is correct
terra::plot(ext_poly, add=T, border="magenta")

# Crop 
dtm_cp <- terra::crop(dtm, ext)

# Plot the cropped raster
terra::plot(dtm_cp)

# Write the aoi raster to disk
writeRaster(dtm_cp, "aoi_dtm.tif", gdal=c("COMPRESS=NONE", "TFW=YES"), overwrite= T)

```

### Notes
- The vsi=TRUE option leverages GDAL's /vsicurl/ to read only the subset (extent area) data from the remote file.
- /vsicurl/ is a file system handler that allows on-the-fly random reading of files available through HTTP/FTP web protocols, without prior download of the entire file. It requires GDAL to be built against libcurl. See GDAL documentation [here](https://gdal.org/en/stable/user/virtual_file_systems.html#vsicurl-http-https-ftp-files-random-access)

### Load in QGIS
- Get the link of the layer you are interested in [GEDTM30 cog_list](https://github.com/openlandmap/GEDTM30/blob/main/metadata/cog_list.csv)
- In QGIS: Layer --> Add Layer --> Add Raster Layer --> Choose HTTP(s) protocol --> Paste the link in URI field. These instructions are in the page [GEDTM30](https://github.com/openlandmap/GEDTM30/tree/main), more specifically in the [gif](https://github.com/openlandmap/GEDTM30/blob/main/img/open_file_qgis_copy_link.gif). 
