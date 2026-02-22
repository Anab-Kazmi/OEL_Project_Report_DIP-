# OEL_Project_Report_DIP-
# DIP Phase 2 Project: Melanoma Detection & Lesion Segmentation

## SUBMITTED BY: Anab Kazmi
## ROLL NO: 2023-SE-06

## 📋 Project Overview

This project implements an automated **skin lesion segmentation and melanoma detection** pipeline using digital image processing and machine learning techniques. It leverages the **PH2 Dataset** to:

1. **Segment lesions** from dermoscopic images using image processing
2. **Extract discriminative features** from segmented lesions
3. **Classify lesions** as melanoma or non-melanoma using machine learning

The complete pipeline integrates image enhancement, morphological operations, lesion segmentation, and random forest classification.

---

## 🎯 Project Goals

- ✅ Develop robust lesion segmentation algorithm
- ✅ Extract meaningful features from segmented regions
- ✅ Train classifiers for melanoma detection
- ✅ Evaluate model performance using standard metrics
- ✅ Generate diagnostic masks for visual validation

---

## 📂 Project Structure

```
DIP_PH2_Project/
├── DIP_PH2_Project.ipynb          # Main Jupyter notebook
├── README.md                       # This file
│
└── Data/ (stored in Google Drive)
    └── PH_2_PROJECT/
        └── PH2Dataset/
            ├── PH2 Dataset images/    # Original dermoscopic images
            ├── generated_masks/       # Generated segmentation masks
            ├── Masks/                 # Ground truth masks
            └── PH2_dataset.txt        # Label annotations
```

---

## 📊 Dataset Information

**Dataset Name:** PH2 (Public Database of Melanomas)

### Dataset Structure
```
PH2 Dataset images/
├── IMD001/
│   ├── IMD001_Dermoscopic_Image/    # Original image
│   └── IMD001_lesion/               # Ground truth mask
├── IMD002/
│   ├── IMD002_Dermoscopic_Image/
│   └── IMD002_lesion/
└── ... (more samples)
```

### Label Information
The `PH2_dataset.txt` file contains:
- **Image ID:** Unique identifier (e.g., IMD001)
- **Histological Diagnosis:** Ground truth classification
- **Clinical Diagnosis:** Encoded as integers
  - `0` = Common Nevus (benign)
  - `1` = Atypical Nevus
  - `2` = Melanoma

### Binary Classification
- **Class 0:** Common Nevus (negative)
- **Class 1:** Atypical Nevus or Melanoma (positive)

---

## 🔧 Installation & Setup

### Prerequisites
- Python 3.7+ (or access to Google Colab)
- Google Drive account (for cloud execution)
- 2+ GB free space on Google Drive

### Required Libraries

```bash
pip install opencv-python numpy pandas scikit-learn tqdm matplotlib plotly
```

### For Local Execution

```bash
# Clone or download the project
cd DIP_PH2_Project

# Create virtual environment (optional)
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### For Google Colab Execution

The notebook is **optimized for Google Colab** and includes:
1. Automatic Google Drive mounting
2. Path configuration for Google Drive folders
3. All necessary imports

Simply upload the notebook to Colab and run cells sequentially.

---

## 🚀 Quick Start Guide

### Step 1: Setup Google Drive Paths

```python
BASE_PATH = "/content/drive/MyDrive/PH_2_PROJECT/PH2Dataset/PH2 Dataset images"
SAVE_PATH = "/content/drive/MyDrive/PH_2_PROJECT/PH2Dataset/generated_masks"
LABEL_TXT = "/content/drive/MyDrive/PH_2_PROJECT/PH2Dataset/PH2_dataset.txt"
```

### Step 2: Mount Google Drive

```python
from google.colab import drive
drive.mount('/content/drive')
```

### Step 3: Run the Pipeline

Execute cells in order:
1. **Import Libraries** - Load all required packages
2. **Image Enhancement** - Apply CLAHE for contrast improvement
3. **Hair Removal** - Remove artifacts using morphological operations
4. **Lesion Segmentation** - Extract binary masks
5. **Feature Extraction** - Compute statistical features
6. **Classification** - Train and evaluate Random Forest model

---

## 🔬 Pipeline Components

### 1️⃣ Image Enhancement

**Function:** `enhance_image(img)`

**Process:**
- Convert BGR → LAB color space
- Apply CLAHE (Contrast Limited Adaptive Histogram Equalization)
  - Clip Limit: 2.0
  - Tile Grid Size: 8×8
- Convert LAB → BGR
- Returns contrast-enhanced image

**Purpose:** Improve lesion visibility and reduce uneven illumination

---

### 2️⃣ Hair Removal

**Function:** `remove_hairs(img)`

**Process:**
- Convert image to grayscale
- Apply Black-hat morphological operation
  - Kernel: 17×17 rectangle
  - Extracts small dark objects (hair)
- Threshold extracted hair regions
- Inpaint using Telea algorithm
- Returns hair-free image

**Purpose:** Remove dermoscopic artifacts that interfere with segmentation

---

### 3️⃣ Lesion Segmentation

**Function:** `segment_lesion(img)`

**Process:**
1. **Grayscale Conversion** - Convert to single channel
2. **Gaussian Blur** - Reduce noise (kernel: 5×5)
3. **Otsu Thresholding** - Automatic binary threshold
4. **Morphological Closing** - Connect lesion boundaries
   - Kernel: 7×7 ellipse
5. **Flood Fill** - Fill internal holes
6. **Returns:** Binary mask with lesion highlighted

**Output:** White pixels = lesion, Black pixels = background

---

### 4️⃣ Feature Extraction

**Function:** `extract_features(img, mask)`

**Extracted Features (6 total):**
- **Blue Channel:** Mean, Std Dev
- **Green Channel:** Mean, Std Dev
- **Red Channel:** Mean, Std Dev

**Normalization:** Values scaled to [0, 1] range

**Purpose:** Capture color distribution within lesion region

---

### 5️⃣ Classification Model

**Algorithm:** Random Forest Classifier

**Hyperparameters:**
- Number of Trees: 50
- Max Depth: 5
- Min Samples Split: 5
- Random State: 42

**Train/Test Split:**
- Test Size: 20%
- Stratified Split: Yes (maintains class balance)

---

## 📊 Expected Results

### Segmentation Evaluation Metrics

```
Confusion Matrix:
[[TN, FP],
 [FN, TP]]

Accuracy   = (TP + TN) / (TP + TN + FP + FN)
Sensitivity = TP / (TP + FN)           # True Positive Rate
Specificity = TN / (TN + FP)           # True Negative Rate
```

### Classification Results

The notebook outputs:
- **Accuracy Score**
- **Precision, Recall, F1-Score** (per class)
- **Classification Report**

---

## 📝 Notebook Cell Breakdown

| Cell | Description |
|------|-------------|
| 1-2 | Import libraries & mount Google Drive |
| 3-4 | Set base paths for dataset |
| 5-6 | Load labels from PH2_dataset.txt |
| 7-9 | Image enhancement function |
| 10-12 | Hair removal function |
| 13-15 | Lesion segmentation function |
| 16-18 | Main mask generation loop |
| 19-20 | Feature extraction function |
| 21-25 | Load images, masks, extract features |
| 26-27 | Train/test split |
| 28-30 | Train Random Forest classifier |
| 31-32 | Evaluate model performance |
| 33+ | Data quality checks |

---

## 🛠️ Troubleshooting

### Issue: "Path does not exist" error

**Solution:**
- Verify Google Drive folder structure matches `BASE_PATH`
- Ensure PH2 Dataset is uploaded to Google Drive
- Check folder permissions

```python
import os
path = "/content/drive/MyDrive/PH_2_PROJECT/PH2Dataset"
print("Exists:", os.path.exists(path))
print("Contents:", os.listdir(path) if os.path.exists(path) else "Not found")
```

---

### Issue: "All labels are 1" in dataset

**Solution:**
- This indicates inconsistent label parsing
- Verify `PH2_dataset.txt` format matches parsing logic
- Inspect label dictionary:

```python
print("Label distribution:", np.unique(y, return_counts=True))
print("Sample labels:", list(label_dict.items())[:20])
```

---

### Issue: "Missing masks" or "Missing images"

**Solution:**
- Run the mask generation cell completely before classification
- Verify all folders have required subdirectories:
  - `{folder_id}_Dermoscopic_Image/`
  - `{folder_id}_lesion/`

```python
print("Missing masks:", missing_masks)
print("Missing images:", missing_images)
```

---

## 📈 Performance Optimization

### For Better Results:

1. **Tune Morphological Kernels**
   - Adjust closing kernel size for larger/smaller lesions
   - Experiment with ellipse vs. rectangle shapes

2. **Enhance Feature Set**
   - Add texture features (LBP, GLCM)
   - Include shape features (area, perimeter, circularity)
   - Add color space features (HSV, CIELAB)

3. **Improve Classification**
   - Use ensemble methods (SVM, Gradient Boosting)
   - Apply hyperparameter tuning (GridSearchCV)
   - Implement cross-validation for robust evaluation

4. **Data Augmentation**
   - Rotation, scaling, flipping
   - Brightness/contrast adjustments
   - Helps prevent overfitting with small datasets

---

## 📚 Key Libraries

| Library | Purpose |
|---------|---------|
| **OpenCV (cv2)** | Image processing, morphological operations |
| **NumPy** | Numerical computations, array operations |
| **Pandas** | Data loading and manipulation |
| **Scikit-learn** | Machine learning, model evaluation |
| **Matplotlib** | Visualization, plotting results |
| **TQDM** | Progress bars for loops |

---

## 🔍 Advanced Usage

### Custom Image Processing Pipeline

```python
def custom_pipeline(img_path, mask_path):
    # Load
    img = cv2.imread(img_path)
    gt_mask = cv2.imread(mask_path, 0)
    
    # Process
    img = enhance_image(img)
    img = remove_hairs(img)
    pred_mask = segment_lesion(img)
    
    # Extract features
    features = extract_features(img, pred_mask)
    
    return pred_mask, features
```

### Batch Processing

```python
for folder in os.listdir(BASE_PATH):
    img_path = f"{folder_path}/{folder}_Dermoscopic_Image"
    mask, features = custom_pipeline(img_path, mask_path)
    # Process results
```

---

## 🤝 Contributing

To improve the project:
1. Experiment with different preprocessing techniques
2. Test alternative segmentation algorithms
3. Implement additional feature descriptors
4. Optimize hyperparameters

---

## 📄 License

This project uses the **PH2 Dataset** for educational purposes.

**Citation:**
```
Mendonça, T., Ferreira, P. M., Marques, J. S., Marcal, A. R., & Rozeira, J. (2013).
PH²-A dermoscopic image database for research and benchmarking.
In 35th Annual International Conference of the IEEE EMBS (pp. 5437-5440). IEEE.
```

---

## 📞 Support & Questions

For issues or questions:
1. Check the **Troubleshooting** section
2. Verify dataset structure and paths
3. Review cell outputs for error messages
4. Check compatibility of OpenCV and other library versions

---

## ✅ Checklist for Execution

- [ ] Google Drive account ready
- [ ] PH2 Dataset uploaded to Google Drive
- [ ] Notebook uploaded to Google Colab
- [ ] All required libraries installed
- [ ] Paths configured correctly in notebook
- [ ] Google Drive mounted successfully
- [ ] Dataset paths verified to exist
- [ ] Ran cells sequentially without skipping
- [ ] Generated masks saved successfully
- [ ] Model trained and evaluated

---

