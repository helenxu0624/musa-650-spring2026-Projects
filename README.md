## Projects
### 1. EuroStats / EuroSAT Classification

The `EuroStats/` folder contains notebooks for land-cover classification using the EuroSAT dataset.  
This project explores both baseline machine-learning and deep-learning approaches for remote sensing image classification.

---

### 2. Final Project: Flood Extent Mapping in Nairobi

The `FinalProjects/` folder contains the full workflow for mapping flood extent in Nairobi using satellite imagery and machine-learning methods.

The project compares three levels of flood-mapping approaches:

- **Threshold-based methods**  
  Sentinel-1 VV/VH before–after change detection and Sentinel-2 optical indices.

- **Traditional machine learning**  
  Random Forest models using Sentinel-1 and Sentinel-2 features.

- **Deep learning**  
  A U-Net-based flood segmentation model trained on Sen1Floods11 and applied to the Nairobi study area.

The final workflow includes preprocessing, model training, model comparison, validation, and report-ready visualizations.

---

## Tools and Libraries

Main tools used in this repository include:

- Python  
- Jupyter Notebook  
- NumPy / Pandas  
- GeoPandas  
- Rasterio  
- Xarray / rioxarray  
- scikit-learn  
- PyTorch  
- Matplotlib  
- STAC / Earth Search satellite imagery workflows  

---

## Notes

Large datasets and model outputs may not be fully included in this repository.  
Please refer to the folder-level README files for more detailed setup instructions and project-specific documentation.

