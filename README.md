# AI for Sustainability – SRIP 2026

## Land Use Classification of the Delhi Airshed Using Satellite Imagery

### Objective

Develop an end-to-end geospatial AI pipeline to analyze land use patterns in the Delhi Airshed using Sentinel-2 satellite imagery and ESA WorldCover data.

---

# Dataset

### Provided Data

* **Delhi NCR boundary:** `delhi_ncr_region.geojson`
* **Delhi Airshed boundary:** `delhi_airshed.geojson`
* **Satellite imagery:** Sentinel-2 RGB patches (128×128 pixels, 10 m resolution)
* **Land cover labels:** ESA WorldCover 2021 (`worldcover_bbox_delhi_ncr_2021.tif`)

### Dataset Statistics

* Total satellite images: **9216**
* Images inside Delhi NCR after filtering: **8015**
* Image resolution: **128 × 128 pixels**
* Spatial resolution: **10 meters per pixel**

---

# Methodology

## 1. Spatial Reasoning and Data Filtering

Steps performed:

* Loaded Delhi NCR region shapefile using **GeoPandas**
* Converted CRS to **EPSG:32644** for spatial grid creation
* Generated **60 km × 60 km grid cells**
* Extracted coordinates from image filenames
* Filtered satellite images whose center coordinates lie inside the Delhi NCR region

Output:

* Grid overlay visualization of Delhi NCR
  <img width="3000" height="3000" alt="delhi_ncr_grid" src="https://github.com/user-attachments/assets/1bf533a7-e378-4606-9451-6e0aaeb0b9a5" />

* Total images before filtering: **9216**
* Images inside region: **8015**

---

## 2. Label Construction and Dataset Preparation

### Land Cover Patch Extraction

For each satellite image:

* Extracted image center coordinates from filename
* Located corresponding pixel location in **ESA WorldCover raster**
* Extracted **128 × 128 land cover patch**

### Label Assignment

* Flattened raster patch
* Computed **dominant land cover class using mode**
* Assigned dominant class as image label

### ESA Class Mapping

ESA codes mapped to simplified categories:

| ESA Code | Category           |
| -------- | ------------------ |
| 10       | Tree cover         |
| 20       | Shrubland          |
| 30       | Grassland          |
| 40       | Cropland           |
| 50       | Built-up           |
| 80       | Water              |
| 90       | Herbaceous wetland |

### Dataset Creation

Final dataset structure:

```
image                label
28.2266_77.5234.png  Cropland
28.4266_77.2822.png  Cropland
28.5355_77.3910.png  Built-up
```

Total labeled samples: **8015**

### Train-Test Split

* Training set: **4809 samples (60%)**
* Test set: **3206 samples (40%)**
* Stratified sampling used to maintain class balance

---

## 3. Model Training

### Model Architecture

* **ResNet18 (pretrained on ImageNet)**
* Final fully connected layer modified for **7 land use classes**

### Training Configuration

* Framework: **PyTorch**
* Optimizer: **Adam**
* Loss function: **CrossEntropyLoss**
* Epochs: **15**
* Batch size: **32**
* Image size: **128 × 128**

---

# Evaluation

### Metrics

* **Accuracy**
* **F1 Score**
* **Confusion Matrix**

### Interpretation

* High accuracy observed for **Cropland** and **Built-up** classes due to distinct spatial patterns.
* Some confusion between **Tree cover** and **Shrubland**, which share similar vegetation textures in RGB imagery.

---

# Pipeline

```
Satellite Images
       ↓
Coordinate Extraction
       ↓
Spatial Filtering (Delhi NCR)
       ↓
Land Cover Patch Extraction
       ↓
Dominant Class Labeling
       ↓
Dataset Creation
       ↓
Train/Test Split
       ↓
CNN Training (ResNet18)
       ↓
Evaluation (Accuracy, F1, Confusion Matrix)
```

---

# Results

Outputs generated:

* Delhi NCR grid visualization
* Class distribution plots
  <img width="3000" height="1500" alt="class_distribution" src="https://github.com/user-attachments/assets/5c591796-6d75-4735-b376-37604f0f195d" />

* CNN training results
  <img width="2400" height="1500" alt="training_loss" src="https://github.com/user-attachments/assets/8424757a-1c6f-4410-b5dc-f6b218a1045a" />

* Confusion matrix visualization
  <img width="2400" height="1800" alt="confusion_matrix" src="https://github.com/user-attachments/assets/db2bf1ef-7af1-49b9-8d42-fd574acb2e08" />

---

# Work 

The code was implemented by me with full understanding of the pipeline steps, including geospatial processing, raster operations, and deep learning model training.
