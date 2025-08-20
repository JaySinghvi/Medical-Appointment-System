# Advanced Clustering Analysis for Medical Risk Stratification

## Overview
This project implements advanced unsupervised learning techniques to uncover hidden patterns in medical data related to diabetes risk assessment. Using both **K-Means clustering** and **Gaussian Mixture Models (GMM)** via the Mclust package, the analysis successfully identifies distinct patient groups corresponding to Normal, Prediabetic, and Diabetic conditions with high statistical significance.

## Key Results
- **Optimal Clusters**: 3 clusters identified by both methods
- **K-Means Purity**: Cluster 1: 76%, Cluster 2: 100%, Cluster 3: 70%
- **GMM Purity**: Cluster 1: 89%, Cluster 2: 72%, Cluster 3: 96%
- **Statistical Significance**: Fisher's exact test and Chi-square tests confirm strong correlation between clusters and medical conditions
- **Best Method**: Gaussian Mixture Models demonstrated superior cluster purity for medical risk stratification

## Project Structure
```
├── README.md                    # Project documentation
├── Lab-Clustering.Rmd          # R Markdown analysis file
├── Lab 8.Rproj                 # RStudio project file
├── glucose_data.csv            # Medical dataset
└── output/
    ├── clustering_report.pdf    # Generated analysis report
    ├── bic_plots.png           # Model comparison visualizations
    └── contour_plots.png       # Dimensionality reduction plots
```

## Dataset Information

### Medical Variables
The dataset contains measurements from patients across different diabetes risk categories:

| Variable | Description | Type |
|----------|-------------|------|
| **test** | Diagnosis category (Normal/Prediabetic/Diabetic) | Factor |
| **glucose** | Blood glucose level measurement | Numeric |
| **insulin** | Blood insulin level measurement | Numeric |
| **sspg** | Steady-state plasma glucose (derived from insulin) | Numeric |

### Class Distribution
| Condition | Frequency | Percentage |
|-----------|-----------|------------|
| Normal | 76 | 52.4% |
| Prediabetic | 36 | 24.8% |
| Diabetic | 33 | 22.8% |

**Total Sample Size**: 145 patients

## Methodology

### 1. Data Preprocessing
```r
# Load and prepare data
g.data <- read.csv("glucose_data.csv")
g.data$test <- factor(g.data$test)

# Standardize features for clustering
g.data_scaled <- scale(g.data[-1])  # Exclude categorical variable
```

### 2. K-Means Clustering Analysis

#### Optimal Cluster Determination
```r
# Use NbClust for optimal k selection
nc <- NbClust(g.data_scaled, 
              min.nc = 2, 
              max.nc = 10, 
              method = "kmeans")
```

**NbClust Results:**
- **Total Indices Used**: 26 clustering validity indices
- **Optimal k**: 3 clusters
- **Consensus**: 7 out of 26 indices agreed on k=3

#### K-Means Performance
| Cluster | Size | Dominant Condition | Purity |
|---------|------|-------------------|---------|
| 1 | 17 | Diabetic | 76% |
| 2 | 22 | Prediabetic | 100% |
| 3 | 106 | Normal | 70% |

### 3. Model-Based Clustering (Gaussian Mixture Models)

#### Mclust Implementation
```r
# Gaussian Mixture Model clustering
model.clus <- Mclust(g.data_scaled)
summary(model.clus)
```

#### GMM Performance
| Cluster | Size | Dominant Condition | Purity |
|---------|------|-------------------|---------|
| 1 | 89 | Normal | 89% |
| 2 | 32 | Prediabetic | 72% |
| 3 | 24 | Diabetic | 96% |

### 4. Statistical Validation

#### Chi-Square Test Results
Both clustering methods showed statistically significant associations:
- **K-Means**: χ² test significant (p < 0.05)
- **GMM**: χ² test significant (p < 0.05)

#### Fisher's Exact Test
Applied to handle low frequency cells in contingency tables:
- **Null Hypothesis**: No relationship between clustering and medical condition
- **Result**: Rejected null hypothesis (p < 0.05)
- **Conclusion**: Strong correlation between cluster membership and diabetes risk levels

## Model Comparison & Evaluation

### BIC Model Selection
The analysis evaluated 14 different Gaussian mixture models:
```r
plot(model.clus, data = g.data_scaled, what = "BIC")
```

**Key Findings:**
- **Best Model**: Ellipsoidal, equal volume and shape (EEE)
- **BIC Score**: Optimal at 3 components
- **Model Selection**: Automatic selection based on Bayesian Information Criterion

### Dimensionality Reduction Visualization
```r
# Generate contour plots for cluster visualization
model.dr <- MclustDR(model.clus)
plot(model.dr, what = "contour")
```

## Results & Clinical Insights

### Cluster Purity Comparison
| Method | Average Purity | Best Cluster Purity | Worst Cluster Purity |
|--------|----------------|---------------------|---------------------|
| **K-Means** | 82% | 100% | 70% |
| **GMM** | 86% | 96% | 72% |

### Medical Risk Stratification
The clustering successfully identified three distinct patient populations:

1. **High-Risk Diabetic Cluster**
   - Characterized by elevated glucose and insulin levels
   - Clear separation from other risk groups
   - 96% purity in GMM analysis

2. **Intermediate-Risk Prediabetic Cluster**
   - Moderate glucose and insulin values
   - Transitional metabolic profile
   - 72-100% purity depending on method

3. **Low-Risk Normal Cluster**
   - Healthy glucose and insulin ranges
   - Largest patient population
   - 70-89% purity across methods

## Technical Implementation

### Required Libraries
```r
library(NbClust)    # Optimal cluster number determination
library(mclust)     # Gaussian Mixture Models
library(knitr)      # Report generation
```

### Key Functions & Analysis
- `NbClust()` - Consensus clustering for optimal k
- `Mclust()` - Gaussian Mixture Model fitting
- `fisher.test()` - Statistical significance testing
- `MclustDR()` - Dimensionality reduction for visualization

### Reproducibility
```r
RNGversion("4.1.2")
set.seed(1234)
```

## Usage Instructions

### Prerequisites
```r
install.packages(c("NbClust", "mclust", "knitr"))
```

### Running the Analysis
1. **Open RStudio Project**:
   ```r
   # Open Lab 8.Rproj in RStudio
   ```

2. **Execute Analysis**:
   ```r
   # Knit the Lab-Clustering.Rmd file
   rmarkdown::render("Lab-Clustering.Rmd")
   ```

3. **Interactive Analysis**:
   ```r
   # Load data and run clustering
   source("clustering_analysis.R")
   ```

## Model Validation Results

### Statistical Tests Summary
| Test | K-Means Result | GMM Result | Interpretation |
|------|----------------|------------|----------------|
| **Chi-Square** | Significant | Significant | Strong association |
| **Fisher's Exact** | p < 0.05 | p < 0.05 | Reject null hypothesis |
| **Cluster Quality** | Good | Excellent | GMM superior |

### Cross-Validation Insights
- Both methods consistently identified 3 clusters
- GMM showed higher overall cluster purity
- Strong correlation between cluster membership and medical diagnosis
- Robust results across different clustering approaches

## Clinical Applications

### Diagnostic Support
- **Risk Stratification**: Automatic patient categorization by diabetes risk
- **Early Detection**: Identification of prediabetic patients
- **Treatment Planning**: Cluster-specific intervention strategies

### Research Implications
- **Patient Segmentation**: Personalized medicine approaches
- **Biomarker Discovery**: Metabolic pattern identification
- **Population Health**: Risk distribution analysis

## Key Insights & Recommendations

### Model Performance
1. **GMM Superiority**: Higher average cluster purity (86% vs 82%)
2. **Robust Results**: Both methods agree on 3-cluster structure
3. **Statistical Validity**: Strong correlation with medical conditions confirmed

### Clinical Recommendations
- **Primary Screening**: Use GMM for initial patient stratification
- **Risk Assessment**: Leverage cluster membership for diabetes prevention
- **Monitoring**: Track patient transitions between risk clusters

### Future Enhancements
- [ ] Include additional biomarkers (HbA1c, BMI, age)
- [ ] Longitudinal clustering for disease progression modeling
- [ ] Integration with electronic health records
- [ ] Real-time risk assessment dashboard development
