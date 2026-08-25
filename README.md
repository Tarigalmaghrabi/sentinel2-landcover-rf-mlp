LAND COVER CLASSIFICATION FROM SENTINEL-2 IMAGERY
Random Forest vs. Multi-Layer Perceptron

Author: Tarig Ahmed
Course: Geoinformatics Project
Academic year: 2025/2026


1. PROJECT OVERVIEW
This project implements an end-to-end Sentinel-2 land-cover classification workflow in Python.
It compares Random Forest (RF) and Multi-Layer Perceptron (MLP) classifiers using raw
Sentinel-2 bands and the spectral indices NDVI, NDWI, and NDBI.

The workflow includes:
- Sentinel-2 search and download from Copernicus Data Space Ecosystem (CDSE)
- automatic preparation of the required bands
- interactive creation and editing of training polygons
- training-label quality control
- sample extraction and class balancing
- stratified 5-fold cross-validation
- systematic MLP configuration testing
- final 20% held-out evaluation
- full-scene classification and GeoTIFF export
- optional cartographic sieve filtering


2. SUBMITTED SENTINEL-2 SCENE
MGRS tile: T32TNR
Product: S2C_MSIL2A_20260709T101601_N0512_R065_T32TNR_20260709T153312.SAFE
Acquisition time: 2026-07-09 10:16:01 UTC
Product level: Sentinel-2 Level-2A
Reported cloud cover: 1.04%


3. REQUIRED OFFLINE DATA
For offline reproduction, place these six prepared rasters inside the project's data/ folder:

data/B02_10m.tif
data/B03_10m.tif
data/B04_10m.tif
data/B08_10m.tif
data/B11_10m.tif
data/B12_10m.tif

Also place:

training_polygons.gpkg

in the project root.

The submitted training dataset contains 160 polygons:
40 Urban, 40 Vegetation, 40 Water, and 40 Bare Soil.


4. RECOMMENDED FOLDER STRUCTURE

project_root/
    Land_Cover_Classification_RF_vs_MLP_FINAL_SUBMISSION_CLEAN.ipynb
    training_polygons.gpkg
    README_Land_Cover_Classification_USER_MANUAL.txt

    data/
        B02_10m.tif
        B03_10m.tif
        B04_10m.tif
        B08_10m.tif
        B11_10m.tif
        B12_10m.tif

    Results/
        training_polygon_quality.csv
        cross_validation_results.csv
        feature_engineering_comparison.csv
        feature_engineering_summary.csv
        final_model_comparison.csv
        rf_confusion_matrix.png
        mlp_confusion_matrix.png
        final_test_performance_rf_vs_mlp.png
        spectral_indices_f1_comparison.png
        landcover_classification_best_model.tif
        landcover_area_summary.csv
        landcover_map.png
        final_map_model_info.csv


5. USER-CONFIGURABLE OPTIONS

The notebook is designed to support both full interactive use and exact offline reproduction.

5.1 DATA SOURCE MODE — Step 1

DATA_SOURCE_MODE = "download"
    Use this option to search Copernicus Data Space Ecosystem, select a Sentinel-2 product,
    enter the user's own CDSE credentials, download the product, and prepare the bands.

DATA_SOURCE_MODE = "offline"
    Use this option when the six prepared GeoTIFF files are already available in data/.
    No CDSE login or SAFE download is required.

Recommended for reproducing the submitted experiment:
    DATA_SOURCE_MODE = "offline"


5.2 TRAINING-POLYGON MODE — Step 1 / Step 6

CREATE_NEW_TRAINING_POLYGONS = True
    Opens the interactive Step 6 interface.
    The user can create new polygons, inspect existing polygons, edit labels, delete polygons,
    run label-quality checks, and save the final training dataset.

CREATE_NEW_TRAINING_POLYGONS = False
    Uses the existing training_polygons.gpkg without opening the polygon-creation workflow.

Recommended for exact offline reproduction of the submitted result:
    CREATE_NEW_TRAINING_POLYGONS = False

Recommended for demonstrating or modifying the training dataset:
    CREATE_NEW_TRAINING_POLYGONS = True


5.3 SENTINEL-2 SEARCH / PRODUCT SELECTION — Steps 2–4

When download mode is used, the notebook allows the user to:
- select/confirm the study tile or area used by the workflow
- define the cloud-cover filtering condition when prompted
- inspect the returned Sentinel-2 products
- choose a product from the numbered list
- answer "y" to confirm the selected product
- answer "n" to return to the product list and choose another product

The submitted experiment uses tile T32TNR and the 9 July 2026 Level-2A product listed above.


5.4 STEP 6 INTERACTIVE TRAINING CONTROLS

Class
    Selects the class assigned to a newly drawn polygon:
    Urban, Vegetation, Water, or Bare Soil.

Show Sentinel-2 RGB
    Toggles the Sentinel-2 RGB layer for visual comparison.
    The high-resolution imagery is only a visual reference; training pixels and QC values are
    always derived from Sentinel-2.

Click a training polygon
    Opens the polygon editor.
    The class can be reviewed/changed or the individual polygon can be deleted.

Apply Class
    Applies the selected class to the clicked polygon.

Delete Polygon
    Removes the selected polygon.

Check Labels
    Runs the spectral label-quality-control procedure.
    Suspect polygons are review candidates only; the QC routine never changes a label
    automatically.

Suspect dropdown + Zoom to suspect
    Allows the user to select a flagged polygon and zoom directly to it for inspection.

Direct click on a highlighted suspect
    Opens the same polygon editor directly, so the user can review it without first using
    the dropdown.

Save + Check
    Saves the complete training dataset and updates the training-quality summary.
    It also produces Results/training_polygon_quality.csv.

Clear All
    Clears the working training-polygon collection. Use only when intentionally starting over.


5.5 TRAINING-QUALITY SETTINGS — Step 6

The submitted workflow checks both polygon count and Sentinel-2 pixel coverage.
The default training targets are:
- at least 20 polygons per class
- at least 2,000 Sentinel-2 pixels per class
- balanced spatial sampling across the scene

These values are quality-control thresholds, not model hyperparameters.


5.6 MODEL EXPERIMENTS — AUTOMATIC, NOT MANUALLY SELECTED

The RF/MLP comparison is intentionally automated.
The user does not manually choose the final classifier before evaluation.

The notebook:
- evaluates Random Forest using raw bands and raw bands + indices
- evaluates at least six MLP configurations
- compares 5-fold CV performance
- selects the best configuration programmatically
- retrains the selected models on the full development set
- evaluates both on the held-out 20% test set
- uses the better-performing model for the production map

random_state = 42 is used for reproducibility.


5.7 OPTIONAL SIEVE FILTER

The sieve-filter step is optional and is used only for cartographic display.

landcover_classification_best_model.tif
    Original scientific classification product.
    Used for accuracy reporting and area statistics.

RF_landcover_sieved_display.tif
    Optional display-only version with small isolated regions removed.
    It must not replace the original raster for quantitative evaluation.


6. QUICK START — EXACT OFFLINE REPRODUCTION

1. Put the notebook, README, and training_polygons.gpkg in the project root.
2. Put the six prepared band GeoTIFFs inside data/.
3. Set:
       DATA_SOURCE_MODE = "offline"
       CREATE_NEW_TRAINING_POLYGONS = False
4. Run the notebook from top to bottom.
5. Check that the final metrics and map are reproduced.


7. QUICK START — FULL INTERACTIVE WORKFLOW

1. Set:
       DATA_SOURCE_MODE = "download"
       CREATE_NEW_TRAINING_POLYGONS = True
2. Run the notebook from the beginning.
3. Search/select a Sentinel-2 product.
4. Enter personal CDSE credentials when requested.
5. Let Step 4 download and prepare the imagery.
6. In Step 6, draw/review polygons and run Check Labels.
7. Click Save + Check after the final corrections.
8. Continue running the remaining cells to train, evaluate, and classify the full scene.


8. SOFTWARE REQUIREMENTS
Python 3.x and Jupyter Notebook / JupyterLab.

Main packages:
numpy
pandas
rasterio
geopandas
scikit-learn
matplotlib
seaborn
shapely
geopy
mgrs
ipyleaflet
ipywidgets
Pillow
requests

Example installation:
pip install numpy pandas rasterio geopandas scikit-learn matplotlib seaborn shapely geopy mgrs ipyleaflet ipywidgets Pillow requests


9. REPRODUCIBILITY
The final balanced dataset contains 8,920 pixels (2,230 per class).
The 80/20 stratified split contains:
- 7,136 development samples
- 1,784 held-out samples

Five-fold StratifiedKFold cross-validation is used on the development set.


10. FINAL SUBMITTED RESULTS

Random Forest:
- 200 trees
- selected feature set: Raw bands + indices
- mean CV Macro F1: 0.996918 ± 0.000841
- held-out Accuracy: 0.997197
- held-out Macro F1: 0.997197
- Cohen's Kappa: 0.996263

Best MLP:
- MLP_4
- hidden layers: (100, 50)
- activation: ReLU
- learning rate: 0.0005
- selected feature set: Raw bands + indices
- mean CV Macro F1: 0.994957 ± 0.001031
- held-out Accuracy: 0.970852
- held-out Macro F1: 0.970882
- Cohen's Kappa: 0.961136

The production map is generated with Random Forest.


11. FINAL MAP AREA SUMMARY
Urban: 22.1403%
Vegetation: 68.5870%
Water: 2.7262%
Bare Soil: 6.5465%


12. VALIDATION NOTE
The final 20% hold-out split is stratified at pixel level and is not spatially independent.
This limitation is documented in the report. A future validation design could reserve complete
polygons or spatially separated areas for testing.


13. REPORT
The accompanying PDF report documents the methodology, RF and MLP theory, feature
engineering, cross-validation, held-out evaluation, full-scene classification, limitations,
and final results.

END OF USER MANUAL
