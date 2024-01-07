---
title: Unlocking the Physical Climate Risk using Climate Model Projections (4)
seo_title: Physical Climate Risk
summary: Using model projections to assess the flood risk
description: Using model projections to assess the flood risk
slug: Blog-4
author: Lin Zhu

draft: false
date: 2022-07-03
lastmod: 
expiryDate: 
publishDate: 

feature_image: 
feature_image_alt: 

categories:
  - Geospatial
  - Tutorial
tags:
  - Climate Change
series: 
  - Unlocking the Physical Climate Risk


toc: true
related: true
social_share: true
newsletter: true
disable_comments: false
---
Through the previous three parts, we already got to know

1. what climate scenarios are and the definition of two IPCC scenarios?
2. what are the climate model projections and how does CMIP work?
3. How to access the climate model projection datasets?
In this part, I will start to use the projection datasets (*precipitation, landform class, population, and land use*)to calculate the potential flood risk score using a simple equation. Coming out of reliable flood risk scores should be far more complex than the model used in this article. But this article will provide good guidance on processing climate model projection datasets and bring insights on embracing the climate model results into physical climate risk assessment.

## Data

Disasters are the outcome of the interaction of hazard and vulnerability which will trigger certain losses in the end. There will be no disaster if the hazard hits an area that has no population/assets, or if a vulnerable area does not experience any hazard events. Thus, the **flood risk (R)** score should be generated through the combination of **hazard(H)** and **vulnerability(V)**.

This article will use the **future precipitation projection** and **landform class** for the hazard module and future **population projection** and **land use projection** for the vulnerability module. Meanwhile, the selected projection datasets were produced with the assumption of scenario **SSP5** (fossil-fueled development) in investigating the potential flood risk in 2100 for the worst case.

### 1. IPCC Ensembled maximum 1-day Precipitation Model Projection
As we discussed in the last section, to avoid bias from a single climate model, IPCC ensembles multiple climate model projections to get the final product. Thus, this article will take the **maximum 1-day precipitation — long term (2081–2100) — SSP5–8.5** from IPCC product sets to represent the precipitation situation in 2100 under the assumption of the SSP5 scenario globally.

Click here to check the ensembled model product to download it directly via the download button and check the data information below.

### 2. The Majority Landform Class dataset

The form of the land to a certain extent indicates its capacity for accumulating water and the chance to be inundated when a flood occurs. The graph below illustrates the 10 most common landform elements. For example, pits or valleys are more prone to flooding. *The data Majority of geomorphological landforms* produced by Amatulli, G., Domisch, S., Tuanmu, MN. et al. provides a global view of the majority landform type in each 1km cell. This article will use this data as topographical input in the hazard module.

### 3. Population Projection Grids 

The future population is a critical driver of vulnerability to hazards. This article will take the global population projection created by Jones, B., and B. C. O’Neill. They used a parameterized downscaling model to produce the population projections that are quantitatively consistent with national population and urbanization projections for the SSPs and qualitatively consistent with assumptions in the SSP narratives regarding spatial development patterns.

Click here to install the dataset and check the data information below.

### 4. Land Use Projection Grids

Another socio-economic indication that is the key determinant of vulnerability to hazards is the degree of urbanization. To integrate the future potential urbanization into vulnerability assessment, this article will also use the land use projection produced by Gao, J., and M. Pesares. This data displays the 1-km fraction of urban land at the end of the 21st under the assumption of SSP-5 globally.

Click here to install the data and check the data information below.

## Process
### 1. Workflow
The final risk score is calculated by combining of datasets mentioned above and assigning the weight for each. There are several steps ahead in pre-processing the datasets to make sure they are represented in a standardized format and can be combined in the end. The first one is re-gridding, which makes sure all datasets are stored in the same grid system. The other is reclassification. It helps to simplify the values or reassign the values based on the standardized criteria.

Check the detailed workflow in the diagram below:

### 2. Re-Gridding

Raster datasets gathered from different data sources do not always have the same extent and resolution. Therefore, one of the most important data processing steps, before we start to calculate the risk score, is re-gridding the multiple rasters into the same reference grids. Afterward, images with the same resolution and extent can be further calculated pixel by pixel. This article uses the population projection data grids(population-ssp5-total-2100)as reference grids and re-grid the precipitation data in the reference grids. The resolution will also be changed from 10km to 1km.

Check the re-grid function below:
```python
from osgeo import gdal
import numpy as np
from scipy.interpolate import griddata
from scipy.interpolate import interp2d
from dask.base import tokenize
import dask.array as da
from xarray import DataArray

def interpolate_slice(rgt, slice_rows, slice_cols, interpolator):
    """Interpolate the given slice of the larger array."""
    fine_cols = np.array([rgt[0] + ix*rgt[1] + rgt[1]/2.0 for ix in range(slice_cols.start, slice_cols.stop)])
    fine_rows = np.array([-rgt[3] - iy*rgt[5] - rgt[5]/2.0 for iy in range(slice_rows.start, slice_rows.stop)])
    return interpolator(fine_cols, fine_rows)


def interpolate_xarray(xpoints, ypoints, values, x, y, rgt, kind='linear',
                       blocksize=500):
    """Interpolate, generating a dask array."""
    vchunks = range(0, y, blocksize)
    hchunks = range(0, x, blocksize)

    token = tokenize(blocksize, xpoints, ypoints, values, kind, (y,x))
    name = 'interpolate-' + token

    interpolator = interp2d(xpoints, ypoints, values, kind=kind)

    dskx = {(name, i, j): (interpolate_slice,
                           rgt,
                           slice(vcs, min(vcs + blocksize, y)),
                           slice(hcs, min(hcs + blocksize, x)),
                           interpolator)
            for j, hcs in enumerate(hchunks)
            for i, vcs in enumerate(vchunks)
            
            }
    res = da.Array(dskx, name, shape=list((y,x)),
                   chunks=(blocksize, blocksize),
                   dtype=values.dtype)
    return DataArray(res, dims=['lon', 'lat'] )


if __name__ == "__main__":
    # open reference file and get resolution
    data_path = "D:/Climate_Change/Data/Flood_Risk"
    referenceFile = data_path+"popdynamics-1-km-downscaled-pop-base-year-projection-ssp-2000-2100-rev01-proj-ssp5-geotiff\SSP5_1km\ssp5_total_2100.tif"
    sourceFile = data_path+"/CMIP6 - Maximum 1-day precipitation (RX1day)  mm - Long Term (2081-2100) SSP5-8.5  - Annual (33 models).nc"

    # Read source raster
    source = gdal.Open(sourceFile)
    nx, ny = source.RasterXSize, source.RasterYSize
    gt = source.GetGeoTransform()
    band_array = source.GetRasterBand(1).ReadAsArray()
    xpoints = np.array([gt[0] + ix*gt[1] + gt[1]/2.0 for ix in range(nx)])
    ypoints = np.array([-gt[3] - iy*gt[5] - gt[5]/2.0 for iy in range(ny)])
    # Read reference raster
    reference = gdal.Open(referenceFile)
    rgt = reference.GetGeoTransform()
    rnx, rny = reference.RasterXSize, reference.RasterYSize
    
    # regridding the raster file by 2d linear interpolation
    result = interpolate_xarray(xpoints, ypoints, band_array, rnx, rny, rgt, kind='linear',blocksize=500)
    # store the result as netcdf
    result = result.assign_coords({"lat": (rgt[0]+result.lat*rgt[1] + rgt[1]/2.0), "lon": (rgt[3]+result.lon*rgt[5]+rgt[5]/2.0)})
    output_file = data_path + "/Percipitation_Maximum-1-day_1km.nc"
    variable_name = list(result.keys())[0]
    result = result.rename({variable_name: 'percip'})
    result.to_netcdf(output_file, encoding = {"percip": {"dtype": "float32"}})
```
### 3. Re-Classification

There are several reasons to do the reclassification. One of them is grouping a certain range of values into one class, which can help to simplify the raster information. Another one is assigning values of preference, priority, sensitivity, or similar criteria to a raster, a typical use case is helping to rescale the value of multiple rasters into a common scale for calculating weighted and combined datasets in the end. Also, by reclassification, No Data cells can be replaced by specific values.

This article will use the reclassification method to group the continuous values of precipitation and population projections into predefined classes. Check the re-classification code (taking the example of reclassifying precipitation) below which makes use of reclassify function from module xrspatial :

```python
import xarray as xr
from osgeo import gdal
import dask.array as da
import numpy as np
from xrspatial.classify import reclassify

source = "D:\Climate_Change\Data\Flood_Risk\popdynamics-1-km-downscaled-pop-base-year-projection-ssp-2000-2100-rev01-proj-ssp5-geotiff\SSP5_1km\ssp5_total_2100.tif"
output = "D:\Climate_Change\Data\Flood_Risk\popdynamics-1-km-downscaled-pop-base-year-projection-ssp-2000-2100-rev01-proj-ssp5-geotiff\SSP5_1km\ssp5_total_2100_reclassified.nc"

bins=[1,25,100, 250, 1000, 5000,100000, np.inf]
new_values=[0,1,2,3,4,5,6,7]
source_array = xr.open_rasterio(source).sel(band=1)
source_numpy_array = source_array.to_numpy()
data_da = da.from_array(source_numpy_array, chunks=(1000, 1000))
agg_da = xr.DataArray(data_da)
source_reclassify = reclassify(agg_da, bins=bins, new_values=new_values).compute()
data_xr = xr.DataArray(source_reclassify, 
                        coords={'y': source_array.y.to_numpy(),'x': source_array.x.to_numpy()}, 
                        dims=["y", "x"])

data_xr.to_netcdf(output)
```

### 4. Risk Score Calculation

Till here, we collected and prepared all datasets to be ready for calculating the risk score further. Get a review of the processed dataset in the table below:

The risk score will be calculated by assigning the same weight for each factor shown in the equation below and normalized into the ranger from 0 to 1.

> R = land_use*0.25 + (pop/7)*0.25 +(percip/11)*0.25 + ((10-landform+1)/10)*0.25
Check the code for the final risk score calculation:
```python
import xarray as xr

land_file = "D:\Climate_Change\Data\ssp5_2100.tif"
pop_file = "D:\Climate_Change\Data\Flood_Risk\popdynamics-1-km-downscaled-pop-base-year-projection-ssp-2000-2100-rev01-proj-ssp5-geotiff\SSP5_1km\ssp5_total_2100_reclassified.nc"
percip_file = "D:\Climate_Change\Data\Flood_Risk\percipitation1km_max_daily_reclassified.nc"
landform_file = "D:\Climate_Change\Data\geom_1KMmaj_GMTEDmd.tif"
pop = xr.open_dataset(pop_file, chunks=1000)
percip = xr.open_dataset(percip_file, chunks=1000)
land_use = xr.open_rasterio(land_file,chunks=1000).sel(band=1)
landform = xr.rasterio(landform_file, chunks=1000).sel(band=1)
score =  land_use*0.25 + (pop/7)*0.25 + (percip/11)*0.25 + ((10-landform+1)/10)*0.25
y = pop.y.to_numpy()
x = pop.x.to_numpy()
result = xr.DataArray(score, 
                        coords={'y': y,'x': x}, 
                        dims=["y", "x"])

output = "D:\Climate_Change\Data\Flood_Risk\result.nc"
result.to_netcdf(output)
```
The resulting data is shown in the graph below:

### Conclusion
1. The factors triggering flood are not only limited by the four elements used in the article. More datasets upon availability can be added to either hazard or vulnerability module to generate more comprehensive models.
2. This article utilizes the climate model projections and top-down approach to analyze the potential flood risk from a global view, which can only provide a general indication of the potential risk. For getting more accurate assessment results, it is also necessary to split the analysis by regions and take more detailed local datasets into consideration.

### Reference
1. Amatulli, G., Domisch, S., Tuanmu, MN. et al. A suite of global, cross-scale topographic variables for environmental and biodiversity modeling. Sci Data 5, 180040 (2018). https://doi.org/10.1038/sdata.2018.40
2. Jones, B., and B. C. O’Neill. 2016. Spatially Explicit Global Population Scenarios Consistent with the Shared Socioeconomic Pathways. Environmental Research Letters, 11 (2016): 084003. https://doi.org/10.1088/1748-9326/11/8/084003.
3. Gao, J. and M. Pesaresi. 2021. Downscaling SSP-consistent Global Spatial Urban Land Projections from 1/8-degree to 1-km Resolution 2000–2100. Scientific Data 8(1): 281. https://doi.org/10.1038/s41597-021-01052-0.