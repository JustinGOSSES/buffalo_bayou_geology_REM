# Buffalo Bayou Geology REM

Python processing for creating Relative Elevation Model (REM) imagery to support the blog post: [Why Buffalo Bayou Does Not Drain to the Sea](https://justingosses.com/blog/why-buffalo-bayou-does-not-drain-to-the-sea)

A screenshot of the blog post can be found at [./Screenshot_2025-12-06HoustonHasTopographyLookingAtWhyBuffaloBayouDoesNotDrainToTheSeaDirectly.png](./Screenshot_2025-12-06HoustonHasTopographyLookingAtWhyBuffaloBayouDoesNotDrainToTheSeaDirectly.png) or at bottom of README.

## Overview

This repository contains the data processing pipeline used to create relative elevation model (REM) visualizations of the Buffalo Bayou area near Houston, Texas. REMs are useful for revealing subtle topographic features related to rivers and geology that are difficult to see in standard elevation maps.

The resulting maps show:
1. **Relative Elevation Model (REM)** - Elevation calculated relative to river centerlines, making floodplains and terraces visible
2. **Fault overlays** - Texas faults from geological surveys
3. **River centerlines** - Buffalo Bayou and Brazos River
4. **Highway overlays** - Major highways from OpenStreetMap for geographic reference

## Repository Structure

```
buffalo_bayou_geology_REM/
├── README.md
├── DEM_download_description.md    # Details on DEM data source and download
├── LICENSE
├── .gitattributes                 # Git LFS tracking for large files
├── data/
│   ├── input_rem_data/            # Input DEM files from Open Topography
│   │   ├── buffaloBayouRegionalV1_USGS10m.tif
│   │   ├── metadata-rt1716147037733.txt
│   │   └── NHDPlus12/             # NHDPlus hydrography data
│   ├── output_rem_data/           # Generated REM outputs
│   │   ├── v3/                    # Earlier version outputs
│   │   └── v8/                    # Latest version outputs
│   │       ├── output_USGS10m_NoDataChangedToZero_REM.tif
│   │       ├── output_USGS10m_NoDataChangedToZero_hillshade-color.tif
│   │       ├── output_USGS10m_NoDataChangedToZero_hillshade-color.png
│   │       ├── output_USGS10m_NoDataChangedToZero_hillshade-color.kmz
│   │       └── tiffPlusOverlaysPlot_vA.png
│   ├── reprojected_data/          # Combined and reprojected shapefiles
│   │   └── combined_BrazosBuffaloBayou.shp  # River centerlines
│   ├── rivers_NHD/                # NHD river flowline data
│   └── faults/
│       └── texas_faults_fromDor/  # Texas fault shapefiles
├── notebooks/
│   ├── ETL_v1clean.ipynb          # Initial REM processing notebook
│   └── ETL_v2.ipynb               # Extended regional REM processing
└── src/
    └── main.py                    # Script version using riverrem package
```

## How to Reproduce

### Prerequisites

1. **Python environment** with the following packages:
   ```bash
   pip install geopandas matplotlib rasterio riverrem osmnx pillow numpy shapely
   ```

2. **Git LFS** - Large files (`.tif`, `.shp`, `.kmz`, etc.) are tracked with Git LFS:
   ```bash
   git lfs install
   git lfs pull
   ```

### Step 1: Obtain the DEM Data

The Digital Elevation Model comes from [Open Topography](https://opentopography.org/) using USGS 1/3 arc-second data (USGS10m).

- **Bounding box**: Xmin=-96.207303, Ymin=29.324906, Xmax=-94.634402, Ymax=30.083256
- **Original job**: https://portal.opentopography.org/rasterOutput?jobId=rt1716147037733
- **Alternative**: Download from [Google Drive](https://drive.google.com/drive/folders/1rsUcOLENkj3Mep1P0wf--2rS9FM__Kza?usp=drive_link)

See `DEM_download_description.md` for full details.

### Step 2: Prepare River Centerlines

The notebooks extract and combine river centerlines for:
- **Buffalo Bayou** - from NHD (National Hydrography Dataset) flowlines
- **Brazos River** - converted from river points to lines

These are combined into `data/reprojected_data/combined_BrazosBuffaloBayou.shp`.

### Step 3: Generate the REM

Using the [RiverREM](https://github.com/OpenTopography/RiverREM) Python package:

```python
from riverrem.REMMaker import REMMaker

rem_maker = REMMaker(
    dem='data/input_rem_data/buffaloBayouRegionalV1_USGS10m.tif',
    out_dir='data/output_rem_data/v8/',
    centerline_shp='data/reprojected_data/combined_BrazosBuffaloBayou.shp'
)
rem_maker.make_rem()
rem_maker.make_rem_viz(cmap='mako_r')
```

### Step 4: Add Overlays

The notebooks demonstrate how to overlay:
- **Faults** from `data/faults/texas_faults_fromDor/texas_faults.shp`
- **Highways** fetched from OpenStreetMap using `osmnx`
- **River centerlines** for reference

## Output Files

The main outputs in `data/output_rem_data/v8/` include:

| File | Description |
|------|-------------|
| `*_REM.tif` | Raw REM raster (elevation relative to rivers) |
| `*_hillshade-color.tif` | Colorized REM with hillshade |
| `*_hillshade-color.png` | PNG export for viewing |
| `*_hillshade-color.kmz` | Google Earth compatible overlay |
| `tiffPlusOverlaysPlot_vA.png` | Final composite with faults & highways |

## Data Sources

- **DEM**: USGS 1/3 arc-second via [Open Topography](https://opentopography.org/)
- **Rivers**: [NHDPlus](https://www.usgs.gov/national-hydrography/nhdplus-high-resolution) from USGS
- **Faults**: Texas fault database
- **Highways**: [OpenStreetMap](https://www.openstreetmap.org/) via osmnx

## Citation

```
United States Geological Survey (2021). United States Geological Survey 3D 
Elevation Program 1/3 arc-second Digital Elevation Model. Distributed by 
OpenTopography. https://doi.org/10.5069/G98K778D. Accessed: 2024-05-25
```

## License

See [LICENSE](LICENSE) file.

## Blog post images methodology

The resulting kmz file that contains the tiff data was uploaded into [Google Earth Pro application](https://support.google.com/earth/answer/21955?hl=en) on a desktop.

This allows for visualization of the data in a way that allows both for overlay of roads and other google provided data, zooming in and out, and creation of [elevation profiles along transects](https://support.google.com/earth/answer/148134?hl=en). 
All of these were necessary for the blog post images.

## Blog post

The blog post that used this data
[Why Buffalo Bayou Does Not Drain to the Sea](https://justingosses.com/blog/why-buffalo-bayou-does-not-drain-to-the-sea) is replicated below in screenshot:

![./Screenshot_2025-12-06HoustonHasTopographyLookingAtWhyBuffaloBayouDoesNotDrainToTheSeaDirectly.png](./Screenshot_2025-12-06HoustonHasTopographyLookingAtWhyBuffaloBayouDoesNotDrainToTheSeaDirectly.png)