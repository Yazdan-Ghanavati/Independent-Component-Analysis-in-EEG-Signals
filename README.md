# 🎧 EEG Signal Separation using FastICA

This project explores how **Independent Component Analysis (ICA)**, specifically **FastICA**, can be used to separate statistically independent sources from multichannel **EEG signals** — a crucial preprocessing step in neuroscience, BCI, and mental health applications.

---

## 🧠 Motivation

Electroencephalography (EEG) data often contains **mixed signals** from different brain regions and non-neural sources (e.g., muscle movement, eye blinks). To make sense of this complex data, it's essential to **separate the underlying sources** — ideally into independent neural components.

This project demonstrates how FastICA can do just that, through:

- Whitening the data  
- Maximizing non-Gaussianity  
- Reconstructing statistically independent components

---

## 🔍 Project Overview

- **Data**: EEG signal from 10 channels (simulated or anonymized patient data)  
- **Goal**: Recover the original sources that generated the observed EEG signals  
- **Method**: Custom implementation of **FastICA**  
- **Output**: Visualization of  
  - Original mixed signals  
  - Whitened signals  
  - Estimated independent components  

---

## 🧪 Methods

The steps are as follows:

1. **Preprocessing**
   - Mean-centering
   - Whitening using eigen-decomposition

2. **FastICA Algorithm**
   - Iterative estimation of independent components using fixed-point updates
   - Non-linearity used: `g(u) = tanh(u)`

3. **Validation**
   - Visual inspection of separated signals
   - Statistical independence confirmed via contrast functions and visual comparison

---

## 📊 Visual Results

You’ll find three key plots in the output:

1. **Original EEG signals** (10-channel mixed signals)
   
   ![Original EEG signals](results/original_signals.png)

2. **Whitened signals** (decorrelated, unit-variance signals)
   
  ![Whitened signals](results/whitened_signals.png)

3. **Estimated Sources** (statistically independent components)  

  ![Estimated sources](results/estimated_sources.png)

These visuals clearly illustrate the transformation at each stage of the algorithm.


