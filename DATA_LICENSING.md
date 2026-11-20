# Dataset Availability, Provenance, and Licensing

**Associated Manuscript:** *A Deep Learning Framework for Morphological Identification of Hilsa in High-Turbidity Riverine Ecosystems* (Revised submission to *Ships and Offshore Structures*, Taylor & Francis)  
**Author / Maintainer:** Md. Mahim Khan, Department of Naval Architecture and Offshore Engineering, Bangladesh Maritime University, Dhaka, Bangladesh  
**Repository:** `https://github.com/D3V1LxD/Yolo`  
**Last Updated:** November 2026

---

## 1. Scope & Domain Transparency

Because live *Tenualosa ilisha* (Hilsa shad) cannot be maintained in standard aquaria and no open-access dataset of genuine in-situ riverine underwater Hilsa footage currently exists, this repository documents a **hybrid terrestrial + synthetic turbid-river corpus** paired with an **external real-world underwater negative-control benchmark** (DeepFish).

* **Positive-class detection metrics** are evaluated on a strictly **leak-free 70/15/15 (Train/Val/Test)** split derived from terrestrial Hilsa photographs, synthetic turbid composites, and empty background frames, with test results reported separately for **Synthetic Underwater** and **Terrestrial** subsets.
* **Optical turbidity robustness** is evaluated via deterministic Jaffe–McGlamery / Beer–Lambert attenuation tiers ($\beta_0 \in \{0.0, 0.4, 0.8, 1.2\}\ \text{m}^{-1}$).
* **Real-world underwater specificity (cross-species and murky-background false-alarm rejection)** is evaluated strictly out-of-distribution on **400 genuine underwater frames** from the open-access **DeepFish** dataset (Saleh et al., 2020; CC BY 4.0).

---

## 2. Dataset Composition & Count Reconciliation

To resolve discrepancies between image count, bounding-box instances, and negative background frames, the complete audited corpus is reconciled below (generated deterministically by `audit_split_and_benchmark.py` and logged in `dataset_split_summary.csv`):

| Split | Unique Source Clusters | Total Images | Share (%) | Annotated *T. ilisha* Instances | Avg. Instances / Image | Background-Only Images (0 Boxes) |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Train** | 2,054 | 2,951 | 69.95% | 2,950 | 1.000 | 198 |
| **Validation** | 439 | 633 | 15.00% | 623 | 0.984 | 44 |
| **Independent Test** | 453 | 635 | 15.05% | 612 | 0.964 | 46 |
| **Total (Primary Corpus)** | **2,946** | **4,219** | **100.00%** | **4,185** | **0.992** | **288** |
| **External Real Underwater Controls (DeepFish)** | 10 habitats (20 clips) | 400 | Held-out | 0 (*non-Hilsa / empty*) | 0.000 | 400 (200 non-Hilsa fish + 200 empty) |

### Reconciliation with Initial Submission Counts
* The initial manuscript reported **3,949 fish-bearing/split images** with **3,339 training bounding-box instances** and **846 validation instances** (`3,339 + 846 = 4,185` total annotated *T. ilisha* instances).
* The audited repository contains **4,219 total images**, comprising **3,931 fish-bearing images** (`4,185` total annotated instances) plus **288 background-only negative frames** (`0` annotated fish boxes) retained to penalize background false positives.

---

## 3. Data Sources, Permissions, and Licensing Matrix

| Component | File Pattern / Directory | Original Source & Attribution | License | Redistribution Status in This Repo |
| :--- | :--- | :--- | :--- | :--- |
| **A. Terrestrial *T. ilisha* Photographs** | `Ilish*.jpg.rf.*`, raw terrestrial frames | **[FILL IN: e.g., Original post-harvest market photography by author AND/OR exact Roboflow Universe / Kaggle dataset name, author, and URL]** | **[FILL IN: e.g., CC BY 4.0 / CC0 1.0]** | **[FILL IN: Redistributed under stated license OR labels + download links only]** |
| **B. Estuarine / Riverine Background Images** | Source backgrounds used to composite `synthetic_hilsa_*.jpg` | **[FILL IN: e.g., Author-captured river imagery / Wikimedia Commons / Pexels / Unsplash with exact URLs]** | **[FILL IN: e.g., CC BY 4.0 / CC0 / Royalty-Free]** | **[FILL IN: Included with attribution OR generation script provided]** |
| **C. Synthetic Turbid Composites & Beer–Lambert Degradation Tiers** | `synthetic_hilsa_*.jpg`, `module1_turbidity_ablation/` | Generated programmatically from Components A & B using background removal, affine transforms, alpha blending, and Jaffe–McGlamery optical attenuation (`ablation_new.py`) | **CC BY 4.0** (derivative data); **MIT** (generation scripts) | Fully reproducible via repository scripts; YOLO annotations redistributed under CC BY 4.0 |
| **D. External Real-World Underwater Negative Controls (DeepFish)** | `external_real_underwater_negatives/` (`non_hilsa_fish/`, `empty_murky_habitats/`) | DeepFish Dataset — Saleh et al. (2020), *Scientific Reports*, 10, 14671 (`https://doi.org/10.1038/s41598-020-71639-x`); streamed from official Hugging Face mirror (`Alzayats/DeepFish`) | **CC BY 4.0** | Exact frame-level manifest logged in `deepfish_provenance_manifest.csv`; reproducible via `fetch_deepfish_negatives.py` |
| **E. Pretrained Backbone Checkpoints & Framework** | `yolov5nu.pt`, `yolov8n.pt`, `yolov9s.pt` | Ultralytics YOLO (pretrained on MS COCO) | **AGPL-3.0** (Ultralytics library); **CC BY 4.0** (COCO annotations) | Downloaded automatically at runtime via `ultralytics`; not redistributed directly |

---

## 4. External Real-World Underwater Benchmark Provenance (DeepFish)

To prevent consecutive-frame pseudo-replication and ensure environmental diversity, the 400 held-out real underwater negative frames were deterministically sampled from `DeepFish/Classification/` using `fetch_deepfish_negatives.py` with an **8-frame temporal stride** and a cap of **25 frames per habitat per category**:

* **Non-Hilsa Underwater Fish (`non_hilsa_fish/`, $n = 200$):** 8 distinct estuarine/coastal habitats (`7117, 7268, 7393, 7398, 7426, 7434, 7463, 7482`) across 9 independent video clips, including silvery laterally compressed juvenile *Caranx sexfasciatus* (Habitat `7117`).
* **Empty Murky Underwater Habitats (`empty_murky_habitats/`, $n = 200$):** 9 distinct habitats (`7117, 7393, 7398, 7426, 7434, 7463, 7482, 7490, 7585`) across 11 independent video clips.
* **Paired-Habitat Control:** 7 habitats (`7117, 7393, 7398, 7426, 7434, 7463, 7482`) are paired across both subsets to isolate cross-species morphological false positives from benthic silt/vegetation clutter.
* **Full Frame-Level Audit Log:** Every filename, habitat ID, original archive path, DOI, and license is recorded in `external_real_underwater_negatives/deepfish_provenance_manifest.csv`.

---

## 5. Data Leakage Audit & Leak-Free Splitting Protocol

Prior to final retraining, `audit_split_and_benchmark.py` audited all 4,219 images using a