# yash-m2m: Multi-Modal Analysis of Clear Cell Renal Cell Carcinoma (ccRCC) Subtypes

This repository contains code and data for analyzing multi-modal features (genomic mutations, tumor mutation burden, whole slide image features) to classify clear cell renal cell carcinoma (ccRCC) subtypes, specifically distinguishing between ccA-only, ccB-only, shared ccA/B, and non-ccA/B cases.

## Project Overview

The project focuses on:
- **Gene Selection and Classification**: Using functional gene mutations and TMB to predict ccRCC subtypes
- **Stratified Analysis**: Evaluating model performance across different patient strata
- **Correlation Analysis**: Investigating relationships between genomic and imaging features
- **Whole Slide Image (WSI) Feature Analysis**: Leveraging deep learning-extracted features from histology images
- **Multi-Modal Integration**: Combining genomic, clinical, and imaging data for improved classification

## Data Description

The dataset includes:
- **Clinical Data**: Patient demographics, tumor stage, vital status
- **Genomic Data**: Functional mutations in cancer-related genes (e.g., VHL, PBRM1, SETD2)
- **Tumor Mutation Burden (TMB)**: Quantitative measure of mutation load
- **WSI Features**: Pre-extracted features from whole slide images using deep learning models
- **Gene Expression Data**: RNA-seq data for select genes
- **Imaging Data**: CT, MRI, and WSI patient files

Data is organized in the `Data/` directory with subfolders for different modalities.

## Repository Structure

```
yash-m2m/
├── Code/                          # Jupyter notebooks for analysis
│   ├── analysis.ipynb            # Main gene selection and classification
│   ├── analysis_MYC.ipynb        # MYC-related analysis
│   ├── analysis_stratified.ipynb # Stratified model evaluation
│   ├── analysis_WSI.ipynb        # WSI feature analysis
│   ├── analysis_WSI_MYC.ipynb    # Combined WSI and MYC analysis
│   ├── correlation.ipynb         # Feature correlation analysis
│   └── data_processing.ipynb     # Data preprocessing scripts
├── Data/                          # Dataset files
│   ├── clinical+genomic_split.csv
│   ├── full_data_table.csv
│   ├── Features/
│   │   ├── WSI Features/         # .npz files with WSI features
│   │   └── Gene expressions/     # Gene expression data
│   └── Patient files/            # CT/MRI/WSI patient metadata
└── Last_internship/               # Previous internship work
    └── onco-select/               # Oncology selection analysis
```

## Installation and Setup

### Using Docker (Recommended)

A multi-stage Dockerfile is provided for containerized execution:

```dockerfile
# Install dependencies into a local folder
COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt

# --- Stage 2: Final Runtime ---
FROM python:3.11-slim

WORKDIR /app

# Copy only the installed packages from the builder stage
COPY --from=builder /root/.local /root/.local
COPY . .

# Ensure the local bin is in the PATH
ENV PATH=/root/.local/bin:$PATH
# Optimization for ML: prevent heavy logging and pyc files
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    && rm -rf /var/lib/apt/lists/*

CMD ["tail", "-f", "/dev/null"]
```

To build and run:
```bash
docker build -t yash-m2m .
docker run -it --rm -v $(pwd):/app yash-m2m
```

### Local Setup

1. **Prerequisites**:
   - Python 3.11+
   - Git

2. **Install Dependencies**:
   ```bash
   pip install pandas numpy matplotlib scikit-learn jupyter
   ```
   *Note: A `requirements.txt` file is referenced in the Dockerfile but not present in the repository. The above packages are inferred from the analysis notebooks.*

3. **Clone the Repository**:
   ```bash
   git clone https://gitlab.m2m.bio/m2m-bio/apm-oncoselect/yash-m2m.git
   cd yash-m2m
   ```

## Usage

1. **Data Preparation**: Ensure data files are in the `Data/` directory
2. **Run Analysis Notebooks**: Use Jupyter to execute the notebooks in `Code/`
   ```bash
   jupyter notebook
   ```
3. **Key Analyses**:
   - `analysis.ipynb`: Gene-based classification pipeline
   - `analysis_WSI.ipynb`: WSI feature analysis
   - `correlation.ipynb`: Feature correlation studies

## Key Findings

- Genomic mutations (particularly in chromatin remodeling genes) are predictive of ccRCC subtypes
- WSI features provide complementary information to genomic data
- Multi-modal approaches improve classification accuracy over single-modality models

## Environment Details

- **Python Version**: 3.11
- **Base Image**: python:3.11-slim
- **Key Libraries**: pandas, numpy, scikit-learn, matplotlib
- **ML Optimizations**: PYTHONDONTWRITEBYTECODE=1, PYTHONUNBUFFERED=1 for performance

