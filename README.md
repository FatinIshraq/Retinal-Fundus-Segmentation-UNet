# Retinal Fundus Image Segmentation — U-Net for Optic Disc Delineation

![Python](https://img.shields.io/badge/Python-3.8+-blue?logo=python&logoColor=white)
![TensorFlow](https://img.shields.io/badge/TensorFlow-2.x-orange?logo=tensorflow)
![Dataset](https://img.shields.io/badge/Dataset-RIM--ONE%20v3-lightgrey)
![Task](https://img.shields.io/badge/Task-Medical%20Image%20Segmentation-red)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

A U-Net implementation for pixel-wise segmentation of the optic nerve head (ONH) in retinal fundus images. Trained and evaluated on the RIM-ONE v3 dataset, which includes both healthy eyes and eyes with glaucoma at different stages.

---

## Results

## Results

| Metric | Train | Validation |
|--------|-------|------------|
| Loss (BCE) | 0.0732 | 0.0773 |
| Pixel Accuracy | 60.28% | 48.55% |

---

## Dataset — RIM-ONE v3

- **Source:** [RIM-ONE r3](http://medimrg.webs.ull.es/research/downloads/) — MIAG Research Group, Universidad de La Laguna
- **Size:** 159 stereo retinal fundus images
- **Annotations:** Optic disc and optic cup segmented by 2 ophthalmology experts; average segmentation used as gold standard
- **Classes:** Healthy eyes + glaucoma eyes at multiple stages
- **Format:** HDF5 — images and masks stored as arrays
- **Split:** 80% train / 20% validation (random stratified)

---

## Model — U-Net

U-Net is an encoder-decoder architecture with skip connections, originally designed for biomedical image segmentation. The skip connections between encoder and decoder preserve fine spatial detail lost during downsampling.

```
Input (256×256×3)
    │
    ├── Encoder
    │     Conv2D(64) → Conv2D(64)  → MaxPool  ─────────────────────────┐ skip
    │     Conv2D(128) → Conv2D(128) → MaxPool  ──────────────────────┐  │ skip
    │     Conv2D(256) → Conv2D(256) → MaxPool  ───────────────────┐  │  │ skip
    │
    ├── Bottleneck
    │     Conv2D(512) → Conv2D(512)
    │
    └── Decoder
          UpSample → Conv2D(256) → Concat(skip) → Conv2D(256) → Conv2D(256)  ←┘
          UpSample → Conv2D(128) → Concat(skip) → Conv2D(128) → Conv2D(128)  ←┘
          UpSample → Conv2D(64)  → Concat(skip) → Conv2D(64)  → Conv2D(64)   ←┘
          Conv2D(1, sigmoid)  →  Segmentation Mask (256×256×1)
```

---

## Data Augmentation

Applied identically to image and mask pairs (same random seed):

| Augmentation | Value |
|-------------|-------|
| Rotation | ±45° |
| Width / Height shift | ±25% |
| Horizontal flip | Yes |
| Vertical flip | Yes |
| Zoom | 0.2 |
| Shear | 0.05 |
| Channel shift | 0.1 |
| Rescale | 1/255 |

---

## Training Setup

| Hyperparameter | Value |
|---------------|-------|
| Input size | 256 × 256 × 3 |
| Batch size | 16 |
| Epochs | 16 |
| Optimizer | Adam |
| Loss | Binary Cross-Entropy |
| Metric | Pixel Accuracy |

---

## Project Structure

```
Retinal-Fundus-Segmentation-UNet/
├── retinal_segmentation_unet.ipynb   # Data loading → U-Net → training → predictions
├── results/
│   ├── Loss and accuracy curve.png                    # Train vs validation loss
│   └── Sample images and ground truth masks.png  
│   └── Validation.png                  # Original · ground truth · predicted mask
├── requirements.txt
└── README.md
```

---

## Quickstart

```bash
git clone https://github.com/FatinIshraq/Retinal-Fundus-Segmentation-UNet
cd Retinal-Fundus-Segmentation-UNet
pip install -r requirements.txt
# Download dataset
wget http://medimrg.webs.ull.es/research/downloads/RIM_ONE_v3.hdf5
jupyter notebook notebook/retinal_segmentation_unet.ipynb
```

---

## Key Concepts Demonstrated

- U-Net encoder-decoder architecture with skip connections
- HDF5 data loading and preprocessing with h5py
- Synchronized augmentation of image-mask pairs using identical random seeds
- Binary cross-entropy loss for pixel-wise binary segmentation
- Medical image segmentation evaluation: visual overlay of predictions vs ground truth

---

## Roadmap

- [x] U-Net implementation from scratch
- [x] Synchronized image-mask augmentation pipeline
- [x] Training on RIM-ONE v3 (optic disc segmentation)
- [x] Visual prediction comparison: original · ground truth · predicted
- [ ] Dice loss / IoU metric
- [ ] U-Net++ (nested skip connections)
- [ ] Transfer learning backbone (ResNet34 encoder)

---

## Author

**Fatin Ishraq** 

[![GitHub](https://img.shields.io/badge/GitHub-FatinIshraq-black?logo=github)](https://github.com/FatinIshraq)
