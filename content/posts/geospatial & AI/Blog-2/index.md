---
title: Unlocking the Physical Climate Risk using Climate Model Projections(2)
seo_title: Physical Climate Risk
summary: Climate Model Projections using the example of CMIP
description: Climate Model Projections using the example of CMIP
slug: Blog-2
author: Lin Zhu

draft: false
date: 2022-06-11
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
  - Climate model
series: 
  - Unlocking the Physical Climate Risk


toc: true
related: true
social_share: true
newsletter: true
disable_comments: false
---

In the previous part, we got to know the concept of climate scenarios. They are one of the important inputs of climate models by indicating the potential pathways for the concentration of greenhouse gases in the atmosphere to project the future climate. The most important climate scenarios are the RCPs and SSPs developed by IPCC.

This section will start to introduce the **Climate Model Projections**, the output of the climate model under the conditional assumption of climate scenarios, to estimate physical climate impact in the future.



## Climate Model and Model projections

A climate model is a computer simulation of the Earth’s climate system, including the atmosphere, ocean, land, and ice.

> At their most basic level, climate models use equations to represent the processes .and interactions that drive the Earth’s climate. These cover the atmosphere, oceans, land and ice-covered regions of the planet. — carbonbrief

Due to the complexity of the climate system and the lack of computing power, now it is not possible to include each climate process in the climate model. But through the past decades especially by leveraging the power of supercomputers, the climate model has been improved significantly. the graph below illustrates that more and more climate processes are involved in the global climate models from the mid-1970s to the fourth Assessment Report (AR4) of the Intergovernmental Panel on Climate Change (IPCC).

Scientists can input different climate scenarios which indicate the potential pathways for the concentration of greenhouse gases into climate models and derive the estimate of potential physical climate impacts such as temperature rise, and precipitation in the future. The estimated results from the climate models are called Climate Model Projections. They help scientists to understand how the climate will change in the future and the impact of human activities on climate change.

Because projections of future climate change are conditional on assumptions of climate forcing, they are affected by shortcomings of climate models and inevitably also subject to internal variability when considering specific periods. Projections among climate models can vary due to differences in their underlying representation of earth system processes. Thus, the use of a multi-model ensemble approach has been demonstrated in recent scientific literature to likely provide better-projected climate change information.

## CMIP model projections

There are different modeling groups working on developing and updating various climate models around the world constantly. For supporting the scientists to share and intercompare the models and provide better climate projections, Coupled Model Intercomparison Projects (CMIP) coordinates the multiple climate models from different modeling groups across the world and make them publicly accessible in a standardized format. Specifically, CMIP develops standards for simulation, data formats, evaluation algorithms, and others.

In addition to observational data, the CMIP climate model projections became one of the main data resources for the IPCC Assessment Report. The update of CMIP also aligns with the schedule of IPCC AR. The 2013 IPCC Fifth Assessment Report (IPCC AR5) featured the climate models from CMIP5, and the IPCC sixth Assessment Report (IPCC AR6) published in 2021 featured the climate model CMIP6. Click here, to check the selected CMIP6 data used as input datasets in IPCC AR6.

## How IPCC uses CMIP model projections

Let’s take an example of the IPCC6 data products as the map shows below to explain how CMIP projections were used in the report. The Map is called **CMIP6-Mean temperature(T) change deg C-Long Term (2081–2100) SSP5–8.5(rel. to 1850–1900)-Annual(34 models)** which represents the ensemble annual mean changes (relative to relative changes of the ensemble mean value) of global temperature between the period 2081–2100 and the period 1850–1900. Meanwhile, The future temperature projections are under the assumption of the scenario SSP5–8.5.

The graph below illustrates how the map above was produced in detail. There are 34 CMIP6 climate models with future and historical projections forming two datasets (*dataset1 is for future projections and dataset2 is for historical projections*)separately and used in the ensemble process. According to the selected comparison time frame between the history and the future, a subset of dataset1 located in the period from 2081 to 2100 is collected and the subset of dataset2 is selected between 1850 and 1900. All collected data should be transformed into the same spatial resolution using the reference grid for further comparison. After the data from each subset is re-gridded into the same spatial representation, they will be aggregated into one final raster data by calculating the annual mean temperature of each grid for the period 2081–2100 and 1850–1900. Calculating the difference between the aggregated raster product for the future and historical annual mean temperature is the last step of the whole ensemble process.

Such a process generates the product by integrating multi-climate models and dismisses the simulation bias of each model, which makes the final output to be more reliable.


*In the next article, I will introduce the method of how to access the climate model projections and their data format.*
