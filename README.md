# Stress Granule Partitioning & RNA Co-localization 🧬

> **Automated Stress Granule Partitioning & smFISH RNA Co-localization Pipeline**

`SG-Quant` is an open-source Python image analysis pipeline designed for fluorescence microscopy data. It automates the segmentation of stress granules (SGs), defines cellular and nuclear boundaries, and quantifies single-molecule FISH (smFISH) RNA transcript partitioning and co-localization.

---

## 📌 What is this Pipeline About?

During cellular stress, cells assemble membrane-less organelles called **stress granules (SGs)**. Quantifying which specific RNA transcripts sequester into these granules—and to what extent—is critical for understanding translational repression and RNA dynamics.

This pipeline performs an end-to-end quantitative analysis on 3-channel fluorescence microscopy images:

1. **Cell & Nuclear Segmentation:** Delineates nuclei (DAPI) and cytoplasmic boundaries using seeded watershed transformation.
2. **Stress Granule Segmentation:** Isolates stress granules (e.g., G3BP1 marker) using background subtraction, Otsu thresholding, and morphological filtering.
3. **Partitioning Ratio Calculation:** Measures mean fluorescence intensity within stress granules relative to the surrounding cytoplasm (`Mean SG Intensity / Mean Cytoplasm Intensity`).
4. **smFISH Spot Detection & Co-localization:** Detects individual RNA transcripts using Laplacian of Gaussian (LoG) spot detection and measures spatial co-localization (% of RNA spots inside granules vs. cytoplasm).

---

## 🚀 How to Execute the Pipeline

### Method 1: Running in Google Colab (Quickest)

1. **Open Google Colab** and upload your notebook (`.ipynb`).
2. **Install core dependencies** in the first cell:
   ```bash
   !pip install scikit-image scipy pandas matplotlib tifffile

```

3. **Generate or Load Image Data:**
* To test with sample data, run the synthetic dataset generation script in the notebook to create `sample_microscopy.tif`.
* To use your own data, upload a multi-channel `.tif` file using the file panel on the left sidebar.


4. **Run All Cells:** Press `Ctrl + F9` (or `Cmd + F9` on Mac) to execute the pipeline and display visual overlays and summary tables.

---

### Method 2: Running Locally via Python Script

#### 1. Clone the Repository & Install Dependencies

```bash
git clone [https://github.com/your-username/SG-Quant.git](https://github.com/your-username/SG-Quant.git)
cd SG-Quant
pip install -r requirements.txt

```

#### 2. Execute Analysis Script

Place your 3-channel `.tif` file inside the `data/` folder and run your main script:

```python
import matplotlib.pyplot as plt
from skimage.io import imread
from src.pipeline import (
    segment_stress_granules,
    segment_cells,
    analyze_granule_metrics,
    analyze_rna_localization
)

# 1. Load multi-channel TIFF stack [Channels, Y, X]
image_stack = imread('data/sample_microscopy.tif')
dapi_channel = image_stack[0]  # Channel 0: Nuclei
sg_channel   = image_stack[1]  # Channel 1: Stress Granules
rna_channel  = image_stack[2]  # Channel 2: smFISH RNA

# 2. Run Segmentation & Analysis
labeled_sg, num_sgs = segment_stress_granules(sg_channel)
nuclei, cytoplasm = segment_cells(dapi_channel, sg_channel)
sg_dataframe = analyze_granule_metrics(sg_channel, labeled_sg, cytoplasm)
rna_summary, detected_spots = analyze_rna_localization(rna_channel, labeled_sg, threshold=0.001)

# 3. Print Results
print(f"Total Granules Detected: {num_sgs}")
print("\n=== GRANULE METRICS ===")
print(sg_dataframe)

print("\n=== RNA LOCALIZATION SUMMARY ===")
print(rna_summary)

```

---

## 📊 Summary Output Metrics

| Output Variable | Description |
| --- | --- |
| `area_pixels` | Area of each segmented stress granule in pixels |
| `mean_intensity` | Average fluorescence signal intensity within the granule |
| `partitioning_coefficient` | Ratio of SG intensity vs. surrounding cytoplasmic background |
| `total_rna_spots` | Total count of smFISH RNA transcripts detected |
| `spots_in_sg` | Number of RNA spots localized inside stress granule boundaries |
| `pct_rna_in_sg` | Percentage of total RNA transcripts sequestered in SGs |

---

## 🛠️ Requirements

* `python >= 3.8`
* `numpy`
* `pandas`
* `scipy`
* `scikit-image`
* `matplotlib`
* `tifffile`

---

## 📄 License

Distributed under the MIT License. See `LICENSE` for details.

```

```
