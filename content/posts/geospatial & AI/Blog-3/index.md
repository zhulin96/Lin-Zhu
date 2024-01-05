---
title: Unlocking the Physical Climate Risk using Climate Model Projections(3)
seo_title: Physical Climate Risk
summary: Access CMIP6 model projection datasets
description: Access CMIP6 model projection datasets
slug: Blog-3
author: Lin Zhu

draft: false
date: 2022-06-18
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
  - Code
series: 
  - Unlocking the Physical Climate Risk


toc: true
related: true
social_share: true
newsletter: true
disable_comments: false
---

Through the previous two parts, we already got to know

1. what climate scenarios are and the definition of two IPCC scenarios?
2. what are the climate model projections and how does CMIP work?

In this part, I will start to introduce how to access the CMIP model projection datasets.

CMIP model projection datasets are written to netCDF files and stored through a distributed data archive developed and operated by the Earth System Grid Federation (ESGF). The data are hosted on a collection of four modeling/data centers around the world. Users can access the entire distributed data through any of the sites showed as below.

* USA, PCMDI/LLNL (California) — https://esgf-node.llnl.gov

* France, IPSL — https://esgf-node.ipsl.upmc.fr

* Germany, DKRZ — https://esgf-data.dkrz.de

* the UK, CEDA — https://esgf-index1.ceda.ac.uk

Meanwhile, technical users can also access the data via ESGF Search RESTful API and integrate the accessing data function into the program directly.

## CMIP Global Attributes

For filing CMIP outputs with standardized metadata, a series of global attributes are pre-defined. They help to identify the source of the data, the imposed experiment, and other related information which are necessary to interpret the dataset. Therefore, these global attributes also work as searching keywords when users access the dataset. Check this file to find out the detailed description of the global attributes. Many global attributes can only be filled by specific values from so-called Controlled Vocabularies (CVs). Controlled Vocabularies are lists containing specific values which can be assigned to the specific global attribute. Check here to find the detailed information on CVs.

A dataset haData Reference Syntax defined as **mip_era.activity_id.institution_id.source_id.experiment_id.member_id.table_id.variable_id.grid_label.version**

This is an example of a dataset name, each part of the name stands for a unique attribute in the metadata.

>CMIP6_AS-RCEC_TaiESM1_ssp585_r1i1p1f1_Amon_gn_20200901

Check the detailed description of each global attribute in the dataset name in the table below.

## Access CMIP via ESGP API

As we mentioned above, CMIP data can also be accessed through ESGP REST API using the searching constraints. The structure of the search service URL looks like this:

> http://<index-node>/esg-search/search?[keyword parameters as (name, value) pairs][facet parameters as (name,value) pairs]

The **index node** in the URL can come from any one of the four sites mentioned above. Python module Pyesgf already encapsulates the searching functions based on ESGF API to access the data conveniently. The code example below uses the Pyesgf module and connects to the site Germany, DKRZ on searching the same dataset mentioned above.

```python
from pyesgf.search import SearchConnection
conn = SearchConnection('https://esgf-data.dkrz.de/esg-search',
                        distrib=False)
#Input constraints for searching queries
ctx = conn.new_context(
        project = "CMIP6",
        table_id = "Amon",
        experiment_id = "ssp585",
        source_id = "TaiESM1",
        institution_id = "AS-RCEC",
        variant_label = "r1i1p1f1",
        variable_id = "sfcWind",
        grid_label = "gn",
        version = 20200901)
results = ctx.search()
for item in results:
    files = item.file_context().search()
    for file in files:
        download_url = file.download_url
        print(download_url)
```

It returns two URL links for downloading the same dataset (in netCDF format) which can be accessed via two data nodes http://esgf.rcec.sinica.edu.tw and http://esgf3.dkrz.de separately.

Python module xarray allows us to read the dataset and get the data structure directly via the link. The code below gives the example of how to use xarray to access, slice, and visualize the dataset.

```python
import xarray as xr
from matplotlib import pyplot as plt
ds = xr.open_dataset(files[0].opendap_url)
#get the value for near surface wind speed
da = ds['sfcWind']
#slice the data 
#time: 2015-01-16 12:00:00
#spatial range: lat [0:50] lon [0:50]
da = da.isel(time=slice(0, 1))
da = da.sel(lat=slice(0, 50), lon=slice(0, 50))
fig = plt.figure()
da.plot()
fig.show()
```
The file structure of *CMIP6_AS-RCEC_TaiESM1_ssp585_r1i1p1f1_Amon_gn_20200901* and the sliced image are shown in the graph below.

*In the next article, I will introduce the use case of combining multiple model projection datasets to evaluate and visualize the potential flood risk.*