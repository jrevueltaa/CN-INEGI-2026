[README.md](https://github.com/user-attachments/files/34593318/README.md)
# National Curve Number maps of Mexico (2007–2014) | CN-INEGI-2026

[![Repository](https://img.shields.io/badge/GitHub-CN--INEGI--2026-181717?logo=github)](https://github.com/jrevueltaa/CN-INEGI-2026)

## Overview

This repository provides national-scale raster datasets of the **NRCS/SCS Curve Number (CN)** and **Hydrologic Soil Group (HSG)** for Mexico, developed from official cartography of the **Instituto Nacional de Estadística y Geografía (INEGI)**. The datasets accompany the research manuscript:

> Revuelta-Acosta, J. D., Alcala-Perea, G., & Conde-Gutierrez, R. A. *Reproducing Mexico’s national TR-55 Curve Number map (2007–2014): A benchmark using INEGI cartography.* Manuscript.

The study reconstructs the national mapping rules reported by Hernández Jiménez (2014), using the USDA Technical Release 55 (TR-55) method, an updated INEGI soil dataset, and three land-use and vegetation series. The output maps share a **250 m equal-area processing grid**, facilitating comparison between 2007, 2011, and 2014.

**Study area:** continental and insular Mexico.  
**Spatial resolution:** 250 m (processing grid).  
**Raster format:** GeoTIFF (`.tif`).  
**Hydrologic setting:** TR-55 tabulated CN values for average antecedent runoff condition (AMC II).  
**Primary purpose:** national and regional hydrologic screening, spatial analysis, and comparison of runoff potential across INEGI land-use series.

## Files

| File | Description | Reference year |
| --- | --- | --- |
| [`CN_INEGI_SERIE_IV.tif`](CN_INEGI_SERIE_IV.tif) | National Curve Number raster derived from INEGI Land Use and Vegetation Serie IV | 2007 |
| [`CN_INEGI_SERIE_V.tif`](CN_INEGI_SERIE_V.tif) | National Curve Number raster derived from INEGI Land Use and Vegetation Serie V | 2011 |
| [`CN_INEGI_SERIE_VI.tif`](CN_INEGI_SERIE_VI.tif) | National Curve Number raster derived from INEGI Land Use and Vegetation Serie VI | 2014 |
| [`INEGI_HYDROLOGIC_SOIL_GROUP.tif`](INEGI_HYDROLOGIC_SOIL_GROUP.tif) | Hydrologic Soil Group raster derived from INEGI Edaphology Serie III, held constant for the three CN dates | Fixed soil input |

**CN values** are dimensionless TR-55 curve numbers. Higher CN values generally indicate greater direct-runoff potential for a given rainfall event, under the method's assumptions. Areas classified as water or wetland were assigned **CN = 98** in the analysis. **HSG categories** correspond to the NRCS groups **A, B, C, and D**, from generally higher to lower infiltration potential; consult the raster's actual encoded values before reclassifying or analyzing it.

> **Technical metadata to verify against the exported GeoTIFFs:** exact CRS/WKT or EPSG identifier, raster data types, pixel-value encoding for the HSG categories, NoData values, spatial extent, and whether compression is enabled. The manuscript documents an Albers equal-area conic processing projection on WGS84 (standard parallels 17.5° N and 29.5° N; central meridian 102° W), but the GeoTIFF CRS metadata should be checked independently before publishing a definitive CRS identifier here.

## Source datasets

All source cartography comes from [INEGI](https://www.inegi.org.mx/), at a nominal mapping scale of **1:250,000**:

| Source | Reference year | Role |
| --- | --- | --- |
| Edaphology Serie III, edition using WRB 2014 soil codes | 2017 (2024 edition, as reported in the manuscript) | Soil units, qualifiers, and texture classes for HSG derivation |
| Land Use and Vegetation Serie IV | 2007 | Vegetation, tree cover, and agriculture for CN mapping |
| Land Use and Vegetation Serie V | 2011 | Same categories, harmonized across series |
| Land Use and Vegetation Serie VI | 2014 | Same categories, with explicit harmonization of renamed classes |

INEGI source data remain attributable to INEGI. Please consult the original INEGI publications and applicable terms of use when redistributing or creating derivative products.

## Method summary

The processing methodology described in the manuscript consists of three main stages:

1. **Hydrologic soil groups.** INEGI soil units and their WRB 2014 qualifiers are translated into HSG A–D. Polygon HSG assignments account for the dominant and, where applicable, secondary and tertiary soil units. This HSG layer is held fixed across all dates.
2. **Land cover and hydrologic condition.** Land-use, vegetation, tree-cover, and agricultural attributes from INEGI Series IV, V, and VI are harmonized into 13 working cover classes associated with TR-55 classes. Hydrologic condition is assigned from the documented rules and attributes.
3. **Curve Number assignment.** Each 250 m cell receives a tabulated TR-55 CN according to its cover class, condition, and HSG. Water and wetland cells are assigned CN 98.

The analysis uses **AMC II**, and runoff calculations in the manuscript employ the conventional TR-55 initial abstraction ratio **λ = 0.2**. The potential maximum retention is:

\[
S = \frac{25400}{CN} - 254,
\]

where \(S\) is measured in millimetres. For rainfall depth \(P\) (mm), direct runoff depth \(Q\) (mm) is:

\[
Q = \begin{cases}
\dfrac{(P - 0.2 S)^2}{P + 0.8 S}, & P > 0.2 S,\\
0, & P \leq 0.2 S.
\end{cases}
\]

For the full classification rules, methodological choices, and sensitivity analysis, consult the accompanying manuscript and its supplementary tables (S1–S5).

## Use and interpretation

The three CN rasters use a common grid and a fixed soil classification, so their differences in the study are attributed to changes in INEGI land-use/cover classes and assigned hydrologic condition, **not to soil change**.

Important qualifications:

- **Cartographic interpretation is not necessarily physical change.** Differences between INEGI series can reflect changes in interpretation, legend terminology, or polygon boundaries; pixel-level changes should not automatically be treated as observed land-use change.
- **Pasture assumptions matter.** The baseline maps follow the original classification of induced and cultivated pasture as protected from grazing. The manuscript evaluates a sensitivity alternative that grades those pastures as fair; the alternative is **not represented by separate raster files in the current repository**.
- **Urban land is simplified.** The published mapping rules classify urban areas as TR-55 open space, rather than using local impervious-surface fractions.
- **These are not locally calibrated flood-hazard maps.** The CN products have not been validated against observed rainfall–runoff events and are not a substitute for site-specific hydrologic investigation or calibration.
- **Mapping scale and resolution differ.** The 250 m raster grid does not increase the inherent detail of INEGI's 1:250,000 source cartography.

## Opening the datasets

The GeoTIFFs can be inspected in GIS software such as **QGIS**, or with geospatial packages in R or Python. Before combining them with other datasets, verify that the file's embedded CRS, grid alignment, NoData value, and pixel encoding are correctly interpreted.

For example, in **R** using the `terra` package:

```r
library(terra)
cn_2014 <- rast("CN_INEGI_SERIE_VI.tif")
print(cn_2014)
crs(cn_2014)
res(cn_2014)
plot(cn_2014, main = "Mexico Curve Number — 2014")
```

This code reads and displays an existing output raster; it does **not** reproduce the full workflow that generated the maps.

## Availability and reproducibility

The **four GeoTIFF datasets** listed above are publicly available in this repository:

https://github.com/jrevueltaa/CN-INEGI-2026

The manuscript describes an R-based workflow and supporting lookup tables, but **the processing scripts and lookup tables are not currently included in this repository**. Consequently, these files support access to the resulting maps, but they do not by themselves enable full reproduction of the raster-generation workflow from INEGI source polygons.

**Archived data version / DOI:** To be added after publication of a versioned release in a persistent research repository (e.g., Zenodo).

## Citation

Until the associated manuscript is published and an archived dataset DOI is assigned, please cite the manuscript as an unpublished work and identify the repository URL and the version/date accessed. **Replace this provisional reference when final bibliographic details and a dataset DOI become available.**

> Revuelta-Acosta, J. D., Alcala-Perea, G., & Conde-Gutierrez, R. A. *Reproducing Mexico’s national TR-55 Curve Number map (2007–2014): A benchmark using INEGI cartography.* Manuscript. Data repository: https://github.com/jrevueltaa/CN-INEGI-2026

## Authors

- **Josept David Revuelta-Acosta** — Department of Civil Engineering, Universidad Veracruzana, Mexico.
- **Gerardo Alcala-Perea** — Centro de Investigación en Recursos Energéticos y Sustentables, Universidad Veracruzana, Mexico.
- **Robert Agustin Conde-Gutierrez** — Centro de Investigación en Recursos Energéticos y Sustentables, Universidad Veracruzana, Mexico.

**Corresponding author:** jrevuelta@uv.mx

## License and attribution

The repository currently includes an **MIT LICENSE** file. Because the shared content consists primarily of **geospatial datasets**, users should not assume that this software-oriented license resolves all rights attached to INEGI source material. Please check the appropriate licensing and attribution terms for both the authors' derived products and the original INEGI datasets before reuse or redistribution.

## Key methodological references

- Hernández Jiménez, F. (2014). *Obtención del Número de Curva (NC) para la República Mexicana mediante el uso de Sistemas de Información Geográfica, aplicando la metodología del TR-55*. BSc thesis, Universidad Nacional Autónoma de México.
- USDA (1986). *Urban Hydrology for Small Watersheds*. Technical Release 55, 2nd edition.
- USDA (2009). *National Engineering Handbook, Part 630 Hydrology*, Chapter 7: Hydrologic Soil Groups.
- INEGI (2009, 2014, 2017). Official soil and land-use/vegetation mapping documentation cited in the associated manuscript.
