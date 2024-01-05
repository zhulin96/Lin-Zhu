---
title: Unlocking the Physical Climate Risk using Climate Model Projections(1)
seo_title: Physical Climate Risk
summary: Climate Scenarios using the example of IPCC
description: Climate Scenarios using the example of IPCC
slug: Blog-1
author: Lin Zhu

draft: false
date: 2022-06-04
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
  - Open Source
series: 
  - Unlocking the Physical Climate Risk

toc: true
related: true
social_share: true
newsletter: true
disable_comments: false
---

Climate change with triggered physical risk is already presenting and impacting our life. Hazards such as wildfire, floods, and heat waves become more frequent and harder to be predicted. Estimating the acute hazards, their future frequency/severity, and finding out the mitigation methods should be set as the top priority. The climate model is one of the powerful tools in this assessment process, which can stimulate the climate change trend and indicate the rise of climate hazards.

However, it is always difficult for non-climate scientists to get understand the climate model products and make use of them at the beginning. Thus, I decided to write the Unlocking the Physical Climate Risk using Climate Model Projections series to provide basic tutorials on utilizing the existed climate model projections to analyze the physical Climate Risk.

The series is divided into four parts:
1. Climate Scenarios using the example of IPCC
2. Climate Model Projections using the example of CMIP
3. Access CMIP model projections
4. Use CMIP6 model projections to assess&visualize the flood risk

They will first help the readers to grasp the basic understanding of important terms such as climate scenarios, climate model projections, IPCC, and CMIP, and later provide the real use case for accessing, visualizing, and utilizing the climate model project datasets for the flood risk.

## What are Climate Scenarios

Scenarios in a simple way to understand are plausible narrative stories used to sketch the possible future. The goal of developing scenarios is not for predicting the future but rather to investigate which alternative futures could be under a wide range of pathways.

Climat scenario is a powerful device to help better understand climate change, chart climate response strategies, and support policy decision-making. A climate scenario represents plausible trajectories of climate driving forces such as greenhouse gas emissions. It is used to construct the ‘if-then’ statement in climate change analysis, if this scenario occurs/the world goes in this way then the future climate could be changed to a certain situation. Climate scenarios are the important inputs of climate models to project the future climate. Referring to the same climate scenario, the outcomes of the multiple climate models also become inter-comparable.


## IPCC climate scenarios

There is already a set of standard, cross-comparable scenarios developed for analyzing climate change. The most universal scenarios used by climate scientists are from the Intergovernmental Panel on Climate Change (IPCC) including Representative Concentration Pathways (RCPs) and Shared Socioeconomic Pathways (SSPs).

RCPs are emissions pathways of greenhouse gases in the atmosphere through the year 2100. The amount of emission will impact the amount of radiative forcing (the difference between solar radiation absorbed by the Earth and energy radiated back into space) and further influence the amount of warming. Therefore, RCP uses the radiative forcing measured in watts/meter squared for naming, such as RCP 2.6 (2.6 watts/m²). The table below gives the example of the RCP pathway and corresponding temperature rise by 2100.

In IPCC the Sixth Assessment Report (AR6), a new pathway called SSPs was established to use. Compared to RCPs, SSPs complement the lack of socioeconomic narratives and improve the exploration of the possible baseline of climate outcomes. These pathways assume no climate policies exceeding current measures or any measures to continuous climate change.

Specifically, SSPs look the socioeconomic trends in five different ways, which are

1) **SSP1**: The world takes “the green road” and shifts gradually in a sustainable way.

2) **SSP2**: The world follows the path in which social, economic, and technological trends do not shift markedly from historical patterns.

3) **SSP3**: The world takes “the rocky road” in which nationalism and security concerns enhance isolationism and slow down economic development.

4) **SSP4**: The world is led by the ever-increasing inequality. Environmental policies only focus on local issues around middle and high-income areas.

5) **SSP5**: The world has strong economic growth, health and education investment, but also unconstrained consumption of fossil fuels.

Combining these five socioeconomic pathways with different intensities of the additional radiative forcing (watts/m²), we can structure various possible scenarios under the assumption of non-climate policy.

For example, SSP1-26 which combines RCP2.6 and SSP1 assumes the greenhouse gas emission will strongly decline and the radiative forcing will reach 2.6 W/m² (with a global average temperature increase of around 2°C)by the year 2100, meanwhile, the climate measure being taken and the world is developing in a sustainable way.

There are four combinations SSP1–2.6, SSP2–4.5, SSP3–7.0, and SSP5–8.5 agreed upon as standard scenarios in IPCC AR6.

If you want to get more details on the SSPs, please check this article.

In the next article of the series, I will go on to talk about climate model projections using the example of CMIP and explain how IPCC uses CMIP.

## Reference

1. https://www.mckinsey.com/business-functions/sustainability/our-insights/climate-risk-and-response-physical-hazards-and-socioeconomic-impacts
2. https://sedac.ciesin.columbia.edu/ddc/ar5_scenario_process/scenario_background.html
3. https://www.dkrz.de/en/communication/climate-simulations/cmip6-en/the-ssp-scenarios