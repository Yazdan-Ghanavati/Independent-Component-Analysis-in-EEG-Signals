# EEG Signal Denoising and Source Separation using ICA

This repository contains the implementation of an Independent Component Analysis (ICA) pipeline designed for processing Electroencephalography (EEG) signals. The project focuses on robust signal preprocessing, data-driven determination of the optimal number of independent sources, and quantitative evaluation of the source separation process using Normalized Mutual Information (NMI).

## Project Overview

The core of this project is to apply advanced signal processing techniques to raw EEG data to isolate underlying neural sources from mixed observations and noise. The pipeline involves:
1.  **Preprocessing**: Centering, whitening, and dimensionality reduction using Principal Component Analysis (PCA).
2.  **Optimal Source Determination**: A data-driven approach to estimate the number of independent sources based on explained variance in PCA.
3.  **Independent Component Analysis (ICA)**: Utilizing the FastICA algorithm to unmix the signals into statistically independent components.
4.  **Quantitative Evaluation**: Assessing the separation quality using Normalized Mutual Information (NMI).
5.  **Visualization**: Illustrating the signal transformation at various stages of the pipeline.

## Core Algorithms

### 1. Normalized Mutual Information (NMI) Calculation

The `get_normalized_mi` function quantifies the statistical independence of a set of input signals. In our ICA pipeline, it assesses the effectiveness of signal separation; lower NMI indicates greater independence.

**Implementation and Flow:**

1.  **Signal Discretization**: Continuous input signals are discretized into bins. The number of bins is determined by Sturges' Rule, and each sample is assigned a bin index.
2.  **Marginal Entropy Calculation**: The probability distribution of binned values is estimated for each individual signal, from which its marginal entropy is calculated, reflecting its inherent uncertainty.
3.  **Joint Entropy Calculation**: The joint probability distribution of all signals is computed by considering unique combinations of bin assignments across all channels. This yields the joint entropy, representing the total uncertainty of the combined system.
4.  **Mutual Information and Normalization**: Mutual Information (MI) is derived by subtracting the joint entropy from the sum of marginal entropies. This MI value is then normalized by the sum of marginal entropies to produce the Normalized Mutual Information (NMI), a metric between 0 (perfect independence) and 1 (complete dependency), facilitating comparison across datasets.

This function provides an objective, numerical indicator of how effectively ICA has made the estimated sources statistically independent.

### 2. Principal Component Analysis (PCA) for Optimal Source Number Determination

The `preprocess_pca` function is a pivotal component of our Independent Component Analysis (ICA) pipeline. It utilizes Principal Component Analysis (PCA) not only for essential signal preprocessing (centering and whitening) but also, crucially, to determine the optimal number of independent sources for subsequent ICA. This data-driven approach to dimensionality reduction is fundamental for enhancing ICA performance and interpretability.

**Algorithm to Find the Optimal Number of Sources:**

The determination of the optimal number of sources is inherently linked to the PCA process within the `preprocess_pca` function:

1.  **Centering**: The initial step involves centering the observed signals by subtracting the mean of each channel.
2.  **Covariance and Eigen-Decomposition**: PCA computes the covariance matrix of the centered data and performs an eigen-decomposition to obtain eigenvalues and eigenvectors. Eigenvectors define principal components (directions of most variance), and eigenvalues quantify the variance each component explains.
3.  **Variance-Based Component Selection**: Eigenvalues are sorted in descending order. The `num_components` is determined as the minimum number of principal components whose cumulative explained variance meets or exceeds a predefined `variance_threshold` (e.g., 95%). This `num_components` is then adopted as the estimated number of independent sources for ICA.
4.  **Dimensionality Reduction and Whitening**: Only the selected `num_components` are retained. The centered data is projected onto these components and then scaled by the inverse square root of their eigenvalues, completing the whitening process crucial for FastICA.

**Advantages of this PCA-based Approach:**

* **Data-Driven Dimensionality Estimation**: Objectively determines the number of components based on signal variance, adapting to diverse data.
* **Noise Reduction**: Discards lower-variance components, effectively acting as a denoising step and improving signal-to-noise ratio for ICA.
* **Computational Efficiency**: Reduces data dimensionality before ICA, significantly decreasing computational load and processing time.
* **ICA Prerequisite**: Provides the essential whitening transformation (decorrelation and unit variance) required by many ICA algorithms.

**Limitations of this PCA-based Approach:**

* **Information Loss**: Discarding components, even those with minimal variance, can lead to some loss of subtle yet potentially relevant information.
* **Linearity Assumption**: PCA is a linear transformation; if underlying sources are non-linearly mixed, some dependencies might persist, potentially affecting ICA performance.
* **Biological Interpretability**: Principal components are statistical constructs and do not directly correspond to physiologically meaningful brain sources or artifacts.

## Experimental Results

This section presents the comprehensive results of applying the ICA pipeline to 60 EEG signals across 10 patients. For each signal, the optimal number of sources determined by PCA and the Normalized Mutual Information (NMI) before and after ICA are reported.

### Quantitative Performance

The tables below summarize the performance metrics, highlighting the reduction in NMI achieved by the ICA process for each patient's recordings.

#### Patient S001 Results

| File Name   | Optimal Sources | NMI Before ICA | NMI After ICA |
| :---------- | :-------------- | :------------- | :------------ |
| S001R01.edf | 7               | 0.923          | 0.296         |
| S001R02.edf | 10              | 0.931          | 0.566         |
| S001R03.edf | 7               | 0.920          | 0.275         |
| S001R04.edf | 10              | 0.924          | 0.499         |
| S001R05.edf | 10              | 0.920          | 0.447         |
| S001R06.edf | 6               | 0.895          | 0.135         |

#### Patient S002 Results

| File Name   | Optimal Sources | NMI Before ICA | NMI After ICA |
| :---------- | :-------------- | :------------- | :------------ |
| S002R01.edf | 12              | 0.921          | 0.588         |
| S002R02.edf | 16              | 0.926          | 0.687         |
| S002R03.edf | 23              | 0.895          | 0.691         |
| S002R04.edf | 17              | 0.911          | 0.684         |
| S002R05.edf | 13              | 0.902          | 0.538         |
| S002R06.edf | 20              | 0.900          | 0.637         |

#### Patient S003 Results

| File Name   | Optimal Sources | NMI Before ICA | NMI After ICA |
| :---------- | :-------------- | :------------- | :------------ |
| S003R01.edf | 8               | 0.927          | 0.437         |
| S003R02.edf | 16              | 0.930          | 0.713         |
| S003R03.edf | 13              | 0.923          | 0.599         |
| S003R04.edf | 15              | 0.921          | 0.673         |
| S003R05.edf | 3               | 0.925          | 0.029         |
| S003R06.edf | 4               | 0.905          | 0.063         |

#### Patient S004 Results

| File Name   | Optimal Sources | NMI Before ICA | NMI After ICA |
| :---------- | :-------------- | :------------- | :------------ |
| S004R01.edf | 17              | 0.926          | 0.736         |
| S004R02.edf | 25              | 0.933          | 0.807         |
| S004R03.edf | 19              | 0.924          | 0.743         |
| S004R04.edf | 19              | 0.925          | 0.740         |
| S004R05.edf | 14              | 0.922          | 0.642         |
| S004R06.edf | 19              | 0.922          | 0.739         |

#### Patient S005 Results

| File Name   | Optimal Sources | NMI Before ICA | NMI After ICA |
| :---------- | :-------------- | :------------- | :------------ |
| S005R01.edf | 21              | 0.935          | 0.787         |
| S005R02.edf | 19              | 0.937          | 0.762         |
| S005R03.edf | 20              | 0.922          | 0.752         |
| S005R04.edf | 16              | 0.919          | 0.697         |
| S005R05.edf | 19              | 0.928          | 0.745         |
| S005R06.edf | 18              | 0.925          | 0.726         |

#### Patient S006 Results

| File Name   | Optimal Sources | NMI Before ICA | NMI After ICA |
| :---------- | :-------------- | :------------- | :------------ |
| S006R01.edf | 12              | 0.923          | 0.600         |
| S006R02.edf | 21              | 0.931          | 0.783         |
| S006R03.edf | 14              | 0.914          | 0.628         |
| S006R04.edf | 14              | 0.915          | 0.633         |
| S006R05.edf | 11              | 0.915          | 0.552         |
| S006R06.edf | 13              | 0.913          | 0.581         |

#### Patient S007 Results

| File Name   | Optimal Sources | NMI Before ICA | NMI After ICA |
| :---------- | :-------------- | :------------- | :------------ |
| S007R01.edf | 7               | 0.931          | 0.356         |
| S007R02.edf | 7               | 0.934          | 0.384         |
| S007R03.edf | 5               | 0.928          | 0.123         |
| S007R04.edf | 6               | 0.924          | 0.212         |
| S007R05.edf | 5               | 0.923          | 0.111         |
| S007R06.edf | 6               | 0.924          | 0.214         |

#### Patient S008 Results

| File Name   | Optimal Sources | NMI Before ICA | NMI After ICA |
| :---------- | :-------------- | :------------- | :------------ |
| S008R01.edf | 14              | 0.927          | 0.676         |
| S008R02.edf | 11              | 0.931          | 0.616         |
| S008R03.edf | 18              | 0.922          | 0.729         |
| S008R04.edf | 19              | 0.924          | 0.761         |
| S008R05.edf | 14              | 0.925          | 0.672         |
| S008R06.edf | 15              | 0.921          | 0.696         |

#### Patient S009 Results

| File Name   | Optimal Sources | NMI Before ICA | NMI After ICA |
| :---------- | :-------------- | :------------- | :------------ |
| S009R01.edf | 19              | 0.933          | 0.760         |
| S009R02.edf | 25              | 0.932          | 0.816         |
| S009R03.edf | 21              | 0.927          | 0.766         |
| S009R04.edf | 21              | 0.924          | 0.768         |
| S009R05.edf | 19              | 0.928          | 0.743         |
| S009R06.edf | 21              | 0.927          | 0.769         |

#### Patient S010 Results

| File Name   | Optimal Sources | NMI Before ICA | NMI After ICA |
| :---------- | :-------------- | :------------- | :------------ |
| S010R01.edf | 27              | 0.927          | 0.816         |
| S010R02.edf | 17              | 0.933          | 0.733         |
| S010R03.edf | 24              | 0.926          | 0.794         |
| S010R04.edf | 15              | 0.929          | 0.668         |
| S010R05.edf | 12              | 0.927          | 0.578         |
| S010R06.edf | 8               | 0.930          | 0.388         |

### Discussion of Results

The tables consistently demonstrate that the Normalized Mutual Information (NMI) after ICA is significantly lower than before, confirming the successful reduction of statistical dependencies in the observed 64-channel EEG signals. This validates the effectiveness of our ICA pipeline in unmixing these complex signals.

The "Optimal Sources" count, determined by PCA (retaining 95% variance), varied considerably across recordings. This variability reflects the intrinsic dimensionality of each signal, influenced by patient characteristics and recording conditions. For instance, S003R05.edf achieved an exceptionally low NMI (0.029) with just 3 sources, indicating clear separation. In contrast, signals like S010R01.edf (27 sources) and S009R02.edf (25 sources) resulted in higher post-ICA NMI, suggesting more complex underlying processes or subtle mixing that made complete decorrelation more challenging. Nevertheless, the consistent NMI reduction across this range of optimal source counts confirms the pipeline's adaptability and efficacy in enhancing signal independence.

### Visual Examples of Signal Transformation

To complement the quantitative results, this section provides a visual example of the signal transformation process, illustrating the original EEG signal, the whitened channels after PCA, and the estimated independent sources after ICA.

**Example from File: S007R01.edf**

**(a) Original EEG Signal:**
The plot below depicts the original EEG signal from `S007R01.edf`. This represents the raw data from the 64-channel electrodes. For better visual clarity, channels are presented with increased vertical separation. For detailed visualization parameters, refer to the `plot_signal_channels` function in the code.

![Original Signal Example](path/to/your/original_signal_plot.png) **(b) Whitened Channels after PCA:**
This plot shows the whitened channels after applying PCA. For this example, 7 principal components were retained from the original 64-channel data to explain 95% of the variance.

![Whitened Signal Example](path/to/your/whitened_signal_plot.png) **(c) Estimated Independent Sources:**
This final plot displays the estimated independent sources after applying FastICA to the whitened channels, providing a demixed view of the underlying brain activity or artifacts.

![Estimated Sources Example](path/to/your/estimated_sources_plot.png) These visualizations provide a clear illustration of the signal transformation at each stage of the ICA pipeline, complementing the quantitative NMI results.

## How to Run the Code

1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/YourUsername/YourRepoName.git](https://github.com/YourUsername/YourRepoName.git)
    cd YourRepoName
    ```
2.  **Install dependencies:**
    ```bash
    pip install numpy scipy pandas mne scikit-learn matplotlib
    ```
    *(Note: `scikit-learn` is typically needed for FastICA implementation. `matplotlib` for plotting.)*
3.  **Prepare your dataset:**
    Place your EEG `.edf` files in a structured folder (e.g., `data/Patient_ID/signal.edf`). Update the `path_to_dataset_folder` variable in the main script to point to your data directory.
4.  **Run the main script:**
    Execute the Python script containing the main processing loop.

## Requirements

* Python 3.x
* `numpy`
* `scipy`
* `pandas`
* `mne` (for `.edf` file loading)
* `scikit-learn` (likely for FastICA if not custom implemented)
* `matplotlib` (for plotting)

## License

This project is licensed under the MIT License - see the `LICENSE` file for details. *(Replace with your chosen license if different, or create a LICENSE file if none exists yet.)*

---
