# 🧠 Brain Tumor Multimodal Medical Image Fusion

A bioinformatics project that fuses multi-modal brain MRI scans (T1ce, T2, FLAIR) from the **BraTS 2020** dataset into a single, information-rich image — improving tumor visualization without relying on segmentation.

> Work-Based Professional Project in Bioinformatics (2) — BIO326
> Faculty of Artificial Intelligence, Delta University for Science and Technology
> Supervised by **Dr. Noha Elattar** — Head & Professor, Bioinformatics Department

---

## 📌 Overview

Brain tumor diagnosis relies on multiple MRI sequences, each highlighting different tissue properties — but no single sequence tells the whole story. This project explores **five image fusion techniques** to combine T1ce, T2, and FLAIR scans into one comprehensive image that helps clinicians better localize tumors, plan treatment, and guide surgery.

## 🩻 Dataset

**[BraTS 2020](https://www.med.upenn.edu/cbica/brats2020/)** — 369 patient cases, each with 4 MRI modalities (T1, T1ce, T2, FLAIR) + expert-annotated segmentation masks, in NIfTI (`.nii.gz`) format.

This project uses **T1ce, T2, and FLAIR** (excluding standard T1, which offers limited diagnostic value relative to the others for this task).

## ⚙️ Fusion Techniques Implemented

| Technique | Core Idea |
|---|---|
| **Spatial Domain – Max Pixel** | Selects the maximum pixel intensity at each location across modalities |
| **Multi-step Spatial Domain Fusion** | Weighted averaging → PCA → Laplacian Pyramid, applied progressively |
| **GAN (Residual CNN Generator)** | Learns to generate a fused slice from stacked T1ce/T2/FLAIR channels |
| **Adaptive Filtering** | Low/high-pass filtering in the Fourier domain with modality-specific weights |
| **Wavelet Transform – Maximum Rule** | DWT decomposition (Symlet 4, Daubechies 4, Haar) + max-coefficient fusion + inverse DWT |

## 🔬 Pipeline

```
Input Modalities (T1ce, T2, FLAIR)
        ↓
Preprocessing (MONAI: spacing, intensity scaling, cropping, resizing to 128³)
        ↓
Fusion (one of the 5 techniques above)
        ↓
Evaluation (MSE, PSNR, SSIM, Entropy, Mutual Information)
        ↓
Fused Image
```

**Preprocessing stack:** Python · PyTorch · MONAI
**Key steps:** `LoadImaged` → `EnsureChannelFirstd` → `Spacingd` (1mm³) → `ScaleIntensityRanged` ([0,1]) → `CropForegroundd` → `Resized` (128×128×128) → `ToTensord`

## 📊 Results

Evaluated across 150 patients; metrics compared against source modalities (T1ce, T2, FLAIR):

| Technique | MSE | PSNR (dB) | SSIM | Entropy |
|---|---|---|---|---|
| **Wavelet Fusion (db4)** | **0.0046** | **25.27** | 0.82 | 13.63 |
| Wavelet Fusion (sym4) | 0.0046 | 25.21 | 0.83 | 13.64 |
| Adaptive Filtering | 0.0057 | 23.54 | **0.90** | **14.00** |
| PCA + Laplacian Pyramid | 0.0083 | 22.71 | 0.87 | 13.54 |

**Wavelet Fusion (Daubechies 4)** achieved the best overall balance of noise reduction and detail preservation, particularly for T1ce and T2. **Adaptive Filtering** best preserved structural consistency but underperformed on FLAIR.

## 🛠️ Tech Stack

![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![PyTorch](https://img.shields.io/badge/PyTorch-EE4C2C?style=flat&logo=pytorch&logoColor=white)
![MONAI](https://img.shields.io/badge/MONAI-2bbbad?style=flat)
![NumPy](https://img.shields.io/badge/NumPy-013243?style=flat&logo=numpy&logoColor=white)

Key libraries: `nibabel`, `pywt` (wavelets), `scipy` (FFT/Gaussian filtering), `scikit-image` (SSIM/PSNR)


## 🔭 Future Work

- Improve FLAIR integration across all fusion techniques (e.g., adjusted weighting, hybrid approaches)
- Extend evaluation to larger, more diverse datasets
- Explore hybrid fusion strategies combining wavelet and deep-learning approaches

## 📚 References

Key sources include the original BraTS benchmark paper (Menze et al., 2014), foundational wavelet fusion work (Li, Manjunath & Mitra, 1995), and recent GAN/CNN-based medical fusion literature. Full reference list is available in the project report.

---

