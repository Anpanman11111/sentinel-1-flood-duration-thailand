# Sentinel-1 Flood Duration by Land Cover Class

The 2016-2017 southern Thailand flood was a large-scale disaster spanning from late December 2016 to early January 2017, affecting 13 provinces and millions of people [1]. This project focused on Mueang Nakhon Si Thammarat, which is the most populous district of southern Thailand. The high societal impact of this event makes it a useful case study, as quantifying where and how long inundation persists directly supports emergency responses, recovery, and long-term risk reduction.


## Aim
Primary aim: To investigate how long vegetated, urban, and agricultural/bare lands in Mueang Nakhon Si Thammarat District remained flooded for on average during the 2016-2017 southern Thailand flood.

Secondary aim: To use elevation and rainfall data, together with knowledge of local geography, to infer the potential drivers behind the observed flood durations.


## Data sources
- Sentinel-1 GRD SAR (C-band): Imported from EE data catalog (ID: COPERNICUS/S1_GRD)
- Administrative boundary: Imported from EE data catalog (ID: FAO/GAUL_SIMPLIFIED_500m/2015/level2)
- Flood reference: Imported from EE data catalog (ID: GLOBAL_FLOOD_DB/MODIS_EVENTS/V1)
- Land class: Imported from EE data catalog (ID: COPERNICUS/Landcover/100m/Proba-V-C3/Global)
- Elevation: Imported from EE data catalog (ID: NASA/NASADEM_HGT/001)
- Precipitation: Imported from EE data catalog (ID: ECMWF/ERA5_LAND/MONTHLY_AGGR)


## Project workflow
1. Filtered the Sentinel-1 ImageCollection to only include images within Mueang Nakhon Si Thammarat District, as well as the chosen dates before and after the flood (15 Dec 2016 - 15 Feb 2017). These dates were used to appropriately capture the conditions before, during, and after the flood. Additionally, only images with > 90% AOI coverage were retained to ensure reliability of subsequent analysis.
2. Trained a randomforest classifier on the Sentinel-1 data composite and flood reference data. Obtained the accuracy metrics.
3. Applied the trained randomforest classifier on each image remaining in the Sentinel-1 ImageCollection to classify non-flood vs flood pixels.
4. Calculated the flood duration of each pixel based on how many images it remained flooded for and the known sampling interval of Sentinel-1 data.
5. Validated the image containing the flood duration data by testing the following: The flood duration band exists in the image, the value range of the pixels is within the expected range, pixels containing non-zero values exist (to test whether flood was detected), and pixel values are multiples of intervals (to check data consistency with the Sentinel-1 revisit interval).
6. Overlaid the flood duration image on land class data and calculated the mean flood duration for each land class.
7. Investigated elevation data and rainfall data to infer the potential drivers behind the observed trend in mean flood duration.


## Results
- The Random Forest classifier achieved OA = 87.3%
- Flooding was the most persistent in the east and south of the district, mostly coinciding with the low-lying agricultural/bare areas
- Agricultural/bare lands remained flooded the longest on average (17.9 days), followed by vegetated lands (7.9 days), then urban areas (3.5 days)
  - Low soil organic matter percentage and soil compaction around rubber and oil palm plantations could have contributed to the long flood duration in agricultural/bare lands [2, 3, 4, 5]
  - The vegetation in the district consist of mangrove, peat swamp, and evergreen forests [6]. These forests can reduce the observed mean flooded duration by absorbing and storing the floodwater, such that it does not remain as open surface water detectable by SAR [7]. However, this could also lead to underestimation of flood duration for vegetated lands.
  - Though urban areas contain more impermeable surfaces than vegetation or agricultural/bare land, they remained flooded for the shortest duration. This could be because of the existing drainage systems and active flood clearance efforts.
- Precipitation peaked in Dec 2016 and remained high through Jan 2017, caused by the northeastern monsoon [8]. This aligns with the flood timeline and suggests that heavy precipitation likely contributed to the flooding.

Fuller interpretations are provided in the notebook `Sentinel_1_Flood_Duration.ipynb`.


## Limitations
- The effective temporal sampling for this project was 12 days, meaning that any changes occurring within that time interval were not captured, leading to inaccuracy in estimates of flood timing and duration.
- The limited time frame means that the mean flooded duration could be underestimated for specific land classes, as they could’ve remained flooded for longer than this project’s scope (ex. some pixels reached the maximum measurable duration of 60 days).
- All working datasets were sampled at 250 m for ease of computation. However, this does not match the native resolution of the datasets. Resampling causes mixed pixels and blurred boundaries, reducing class-specific interpretability and the accuracy of flood duration of each class.
- Land classes were grouped into only 3 classes for simplicity. This resulted in loss of within-class variability, as each sub-class could have different properties (ex. porosity). As a result, explanations of land cover controls on flood duration remained partly speculative.


## Tools used
- Google Earth Engine (Python API)
- xee / xarray: converting ImageCollections into arrays for time-series analysis
- pandas / numpy: calculations
- matplotlib: plotting outputs

Exact package requirements are provided in `requirements.txt`.


## References
[1] - Tebakari, T., Wongsa, S. and Hayashi, Y. (2018). Floods in Southern Thailand in December 2016 and January 2017. Journal of Disaster Research, 13(4), pp.793–803. doi:https://doi.org/10.20965/jdr.2018.p0793.

[2] - Limtong, P. (2012). Status and Priorities of Soil Management in Thailand Global Soil Partnership, Technical Workshop ‘on Managing Living Soils’. [online] Available at: https://www.fao.org/fileadmin/user_upload/GSP/docs/WS_managinglivingsoils/Limtong_Thailand.pdf.

[3] - Bot, A. and Benites, J. (2005). Importance of soil organic matter. Rome: Fao

[4] - Acosta, Z. (2025). Thailand Agriculture: A Comprehensive Guide to One of Asia’s Most Vital Industries. [online] Eosglobalexpansion.com. Available at: https://eosglobalexpansion.com/thailand-agriculture-comprehensive-guide/.

[5] - Guillaume, T., Holtkamp, A.M., Damris, M., Brümmer, B. and Kuzyakov, Y. (2016). Soil degradation in oil palm and rubber plantations under land resource scarcity. Agriculture, Ecosystems & Environment, 232, pp.110–118. doi: https://doi.org/10.1016/j.agee.2016.07.002.

[6] - ESCAP (2020). Nakhon Si Thammarat, Thailand. [online] Available at: https://www.unescap.org/sites/default/d8files/2020-08/NakhonSiThammarat.pdf.

[7] - Nature-Based Infrastructure (2024). Wetlands - Nature-Based Infrastructure Global Resource Centre. [online] Nature-Based Infrastructure Global Resource Centre. Available at: https://nbi.iisd.org/wetlands/.

[8] - THAI METEOROLOGICAL DEPARTMENT (2025). 3-month Climate Prediction of Thailand.













