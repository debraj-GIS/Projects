# West Bengal Climate Data Analysis (2000–2025)

A geospatial climate-data project for West Bengal, India, using Google Earth Engine (ERA5-Land) and Python. The project collects annual climate variables at 3,000 randomly generated sample points and analyses long-term variability, trends, anomalies, and relationships among climatic variables.

## Project Overview

This repository contains two Jupyter notebooks:

1. **`Climate_Data_Collection_(2000_2025).ipynb`** — prepares the West Bengal area of interest (AOI), generates sample points, processes ERA5-Land daily data into annual climate indicators, samples the annual images, and exports the results to Google Drive.
2. **`Data_Analysis(1).ipynb`** — loads the exported CSV and performs exploratory statistical analysis and visualisation using NumPy, pandas, Matplotlib, and SciPy.

**Study period:** 2000–2025  
**Study area:** West Bengal, India  
**Sampling design:** 3,000 random points generated with seed 42  
**Climate data source:** Google Earth Engine dataset `ECMWF/ERA5_LAND/DAILY_AGGR`

## Objectives

- Collect annual climate information across West Bengal using a consistent set of sample points.
- Examine annual minimum, maximum, and mean temperature.
- Analyse annual precipitation, rainfall anomalies, and wet/dry years relative to the long-term average.
- Investigate relative humidity variability and differences among sample locations.
- Compare actual and potential evaporation and calculate an aridity index.
- Analyse annual wind speed, wind direction, prevailing direction, and wind-rose patterns.
- Explore relationships between climate variables using Pearson correlation.
- Visualise annual variation, moving averages, linear trends, and statistical relationships.

## Repository Structure

```text
West-Bengal-Climate-Data-Analysis/
│
├── Climate_Data_Collection_(2000_2025).ipynb
├── Data_Analysis(1).ipynb
├── WB_Annual_Climate_2000_2025.csv
├── README.md
└── Documentation/
    └── Project-Report.docx
```

The CSV and documentation paths above describe the recommended repository layout. Add the exported CSV and report to the repository only when you are permitted to redistribute them.

## Data Source

The collection notebook uses the Google Earth Engine ImageCollection:

`ECMWF/ERA5_LAND/DAILY_AGGR`

The collection is filtered to the West Bengal AOI and the period from 1 January 2000 through 31 December 2025. The notebook uses the district-boundary shapefile `WEST BENGAL_DISTRICTS.shp` as the AOI input.

ERA5-Land is a reanalysis dataset. The extracted values are model/reanalysis grid estimates sampled at point locations; they are not direct observations from weather stations.

## Climate Variables

| Output field | Description | Unit |
|---|---|---|
| `point_id` | Identifier for each random sample point | ID |
| `longitude` | Sample point longitude | Degrees |
| `latitude` | Sample point latitude | Degrees |
| `year` | Calendar year | Year |
| `annual_min_temp_C` | Annual minimum of daily minimum 2 m temperature | °C |
| `annual_max_temp_C` | Annual maximum of daily maximum 2 m temperature | °C |
| `annual_mean_temp_C` | Annual mean of daily mean 2 m temperature | °C |
| `annual_precipitation_mm` | Annual accumulated total precipitation | mm |
| `annual_relative_humidity_pct` | Annual mean relative humidity estimated from temperature and dew point | % |
| `annual_wind_speed_m_s` | Annual mean 10 m wind speed calculated from u/v components | m/s |
| `annual_wind_direction_deg` | Annual mean of daily wind-direction angles as calculated in the notebook | Degrees |
| `annual_evaporation_mm` | Annual accumulated evaporation after sign conversion | mm |
| `annual_potential_evaporation_mm` | Annual accumulated potential evaporation after sign conversion | mm |

## Methodology

### 1. AOI preparation and sampling

- Read the West Bengal district boundary shapefile with GeoPandas.
- Convert the boundary to an Earth Engine feature collection using geemap.
- Generate 3,000 random points within the AOI using `ee.FeatureCollection.randomPoints`.
- Use random seed `42` and attach a point ID, longitude, and latitude to each point.

### 2. Climate data processing in Google Earth Engine

The collection notebook processes daily ERA5-Land data and creates annual images for each year from 2000 to 2025.

- Temperature: convert Kelvin to Celsius by subtracting 273.15.
- Precipitation: convert metres to millimetres and sum daily values for each year.
- Relative humidity: estimate from air temperature and dew-point temperature using a saturation-vapour-pressure relationship, then constrain the result to 0–100%.
- Wind speed: calculate from 10 m u- and v-component wind values.
- Wind direction: derive direction in degrees from the u- and v-components.
- Evaporation and potential evaporation: convert the source accumulations to millimetres using the sign conversion in the notebook.
- Annual statistics: calculate the annual minimum, maximum, mean, or sum according to the variable.

The annual climate images are sampled at the same random points. The resulting table is exported to Google Drive as `WB_Annual_Climate_2000_2025.csv` in the `GEE_Exports` folder.

### 3. Statistical analysis in Python

The analysis notebook uses the exported CSV to perform:

- Dataset inspection and missing-value checks.
- Year-wise climate summaries.
- Long-term means and standard deviations.
- Hottest, coolest, wettest, driest, and high/low humidity year identification.
- Year-to-year absolute and percentage changes.
- Climate anomalies relative to the full-period average.
- Five-year moving averages.
- Linear trend fitting and trend visualisation.
- Comparisons between selected time periods.
- Pearson correlation coefficients and a climate-variable correlation matrix.
- Location-level relative humidity ranking.
- Aridity index calculation using precipitation and potential evaporation.
- Wind speed, prevailing wind direction, and wind-rose analysis.

## Python Libraries

- `earthengine-api` — access to Google Earth Engine.
- `geemap` — Earth Engine and geospatial mapping workflows.
- `geopandas` — reading and handling the AOI shapefile.
- `numpy` — numerical calculations.
- `pandas` — data processing and tabular analysis.
- `matplotlib` — charts and visualisation.
- `scipy` — statistical calculations, including Pearson correlation.

## How to Run

### A. Collect climate data

1. Open `Climate_Data_Collection_(2000_2025).ipynb` in Google Colab or a compatible Jupyter environment.
2. Upload the West Bengal district-boundary shapefile and ensure its component files (`.shp`, `.shx`, `.dbf`, `.prj`, etc.) are available together.
3. Update `shp_path` in the notebook to point to the shapefile.
4. Install/import the required libraries and authenticate with Google Earth Engine.
5. Initialise Earth Engine with an authorised Google Cloud project. The notebook currently uses `ee-debrajkolya`; replace it with your own project ID if needed.
6. Run the notebook cells and start the table export task.
7. Check the Earth Engine task status and download the completed CSV from Google Drive.

### B. Analyse the exported data

1. Open `Data_Analysis(1).ipynb`.
2. Place the exported CSV in your Google Drive or working directory.
3. Update `file_path` in the notebook to the CSV's actual location.
4. Run the notebook cells in order to reproduce the tables, statistics, and plots.

## Interpretation and Limitations

- The 3,000 random points are a sampling of the ERA5-Land grid over the AOI. Their arithmetic mean is a sample-point summary and should not automatically be described as an area-weighted West Bengal average.
- The random-point seed makes the point generation reproducible for the same AOI and sampling settings.
- The wet/dry-year labels in the analysis are based on whether annual precipitation is above or below the selected long-term mean. They are not official drought or flood classifications.
- A linear trend describes the fitted direction and rate over the selected period; it does not by itself establish statistical significance or causation. Interpret it together with R² and the underlying time series.
- Pearson correlation measures linear association, not causation.
- Wind direction is circular data. A simple arithmetic mean of degrees can be misleading when directions cross 0°/360°. For robust annual mean direction, use a circular-mean calculation based on sine and cosine components. The wind-rose should use direction and wind-speed observations with a clearly defined temporal basis.
- Relative humidity is derived from temperature and dew point using the formula implemented in the notebook; it is an estimate, not a directly selected ERA5-Land humidity band.
- Confirm ERA5-Land variable definitions, accumulation conventions, units, and sign conventions against the dataset documentation before using results in formal research.
- Results depend on the AOI boundary, random sample points, source dataset version, and processing choices.

## Reproducibility

The sampling seed is set to `42`, and the study period is explicitly defined as 2000–2025. To reproduce the analysis, retain the same AOI boundary, Earth Engine dataset, sampling settings, and processing logic. Record any changes to these settings when updating the project.

## License

This repository's code may be released under the **MIT License** if you are the author and have the rights to license it. This does not automatically relicense ERA5-Land data, boundary files, third-party libraries, or other external materials. Check and retain the applicable data-source and boundary-data terms, and include any required attribution.

## Author

**Debraj Kolya**  
GIS | Remote Sensing | Geospatial Data Analysis | Python

---

*This project is intended for educational, exploratory, and portfolio purposes. It demonstrates a workflow for climate-data extraction, processing, statistical analysis, and visualisation using Google Earth Engine and Python.*
