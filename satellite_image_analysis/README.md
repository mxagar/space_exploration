# Satellite Image Analysis

This folder contains classification, clustering and dimensionality reduction examples applied to satellite images. The techniques are applied pixel-wise. These examples come originally from these sources:

- [syamkakarla98/Satellite_Imagery_Analysis](https://github.com/syamkakarla98/Satellite_Imagery_Analysis)
- [Satellite Imagery Analysis 101: Handling TIFF Files](https://syamkakarla.medium.com/satellite-imagery-analysis-101-handling-tiff-files-22e4d2da236d)

[RasterIO](https://rasterio.readthedocs.io/en/stable/) is used along with [EarthPy](https://earthpy.readthedocs.io/en/latest/index.html) to load an process the TIFF images separated in different bands.

Note that EarthPy has [example datasets](https://earthpy.readthedocs.io/en/latest/earthpy-data-subsets.html).

Mikel Sagardia, 2023.  
No guarantees.

## Notebooks

- [`00_ImageManipulation.ipynb`](./00_ImageManipulation.ipynb): basics of image manipulation are shown.
- [`01_Classification.ipynb`](./01_Classification.ipynb): pixels of images with 12 bands are classified given the ground truth labels (6 categories).
- [`02_Clustering_DimensionalityReduction.ipynb`](./02_Clustering_DimensionalityReduction.ipynb): 
- [`03_Band_Analysis.ipynb`](./03_Band_Analysis.ipynb): Sentinel-2 bands are combined to obtain relevant geological and vegetation features.

## Sentinel-2 Band Explanation

Sentinel-2 consists of 2 satellites (Sentinel 2A and 2B). Sentinel-2 carries the Multispectral Imager (MSI). This sensor delivers 13 spectral bands ranging from 10 to 60-meter pixel size.

| Band | Resolution | Central Wavelength | Description |
| --- | --- | --- | --- | --- | 
| B1 | 60 m | 443 nm| Ultra Blue (Coastal and Aerosol) |
| B2 | 10 m | 490 nm | Blue |
| B3 | 10 m | 560 nm | Green |
| B4 | 10 m | 665 nm | Red |
| B5 | 20 m | 705 nm | Visible and Near Infrared (VNIR) |
| B6 | 20 m | 740 nm | Visible and Near Infrared (VNIR) |
| B7 | 20 m | 783 nm | Visible and Near Infrared (VNIR) |
| B8 | 10 m | 842 nm | Visible and Near Infrared (VNIR) |
| B8a | 20 m | 865 nm | Visible and Near Infrared (VNIR) |
| B9 | 60 m | 940 nm | Short Wave Infrared (SWIR) |
| B10 | 60 m | 1375 nm | Short Wave Infrared (SWIR) |
| B11 | 20 m | 1610 nm | Short Wave Infrared (SWIR) |
| B12 | 20 m | 2190 nm | Short Wave Infrared (SWIR) |

Band combinations provide relevant information related to geological and vegetation information, as one can see in [Sentinel Playground](https://www.sentinel-hub.com/explore/sentinelplayground/).

Important band combinations:

- RGB colors: B4, B3, B2.
- Color infrared: B8, B4, B3.
    - Healthy dense vegetation appears very red.
    - Buildings, white.
- Short-Wave Infrared (SWIR): B12, B8A, B4.
    - Darker green: more dense vegetation.
    - Brown: bare soil.
- Agriculture: B11, B8, B2.
    - Dense vegetation: dark green.
- Geology: B12, B11, B2.
    - Lithology, geological formations.
- Bathymetric: B4, B3, B1.
    - Suspended sediments in water.
- Vegetation Index: (B8-B4)/(B8+B4).
    - High values: dense canopy.
    - Low values: urban, water.
- Moisture Index: (B8A-B11)/(B8A+B11).
    - Water stress in plants if low value.

Additional exemplary indices are obtained and visualized in [`03_Band_Analysis.ipynb`](./03_Band_Analysis.ipynb):

- Vegetation and Soil Indices
    - Normalized Difference Vegetation Index (NDVI)
    - Soil-Adjusted Vegetation Index (SAVI)
    - Visible Atmospherically Resistant Index (VARI)
    - Distribution of NDVI, SAVI, and VARI pixel values
- Water Indices
    - Modified Normalized Difference Water Index (MNDWI)
    - Normalized Difference Moisture Index (NDMI)
- Geology Indices
    - Clay Minerals
    - Ferrous Minerals


More information:

- [Sentinel 2 Bands and Combinations](https://gisgeography.com/sentinel-2-bands-combinations/)
- [How to Download Free Sentinel Satellite Data](https://gisgeography.com/how-to-download-sentinel-satellite-data/)
- [100 Earth Shattering Remote Sensing Applications & Uses](https://gisgeography.com/remote-sensing-applications/)
- [Spectral Signature Cheatsheet](https://gisgeography.com/spectral-signature/)
