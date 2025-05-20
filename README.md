# Functional Connectivity Builder

This repository contains modular Python code for building functional connectivity networks from spike train data using dot product-based similarity and null-hypothesis testing. It was developed for use in electrophysiology analysis and validated in a Neuron manuscript focused on brain network dysfunction after early-life seizures.

---

## **Features**

* **Spike timestamp parsing and formatting**
* **Firing rate binning over fixed intervals**
* **Sliding-window dot product computation**
* **Mean edge weight calculation**
* **ISI-preserving spike train shuffling (null model)**
* **Null distribution generation and statistical significance testing**
* **Exportable dot product matrices, firing rates, and significance results**

---

## 📁 **File Structure**

```
functional-connectivity-builder/
├── notebook/
│   └── Code for Dot product, normalization, edge weights and null hypothesis 5-19-25.ipynb    # Combined Jupyter workflow
├── scripts/                                                # Optional refactored .py versions
│   ├── convert_timestamps.py
│   ├── calculate_firing_rates.py
│   ├── generate_dot_products.py
│   ├── compute_edge_weights.py
│   ├── shuffle_isi_generate_null.py
│   └── test_edge_significance.py
├── data/                                                   # Place raw spike time data here
├── output/                                                 # Dot products, edge weights, p-values
├── README.md
├── requirements.txt
├── LICENSE
└── .gitignore
```

---

## **How to Use**

### 1. Convert Text Timestamps to Dict

Parse raw text-formatted neuron timestamps into a Python dictionary:

```python
from convert_timestamps import convert_timestamps_to_dict
neuron_data = convert_timestamps_to_dict(text_block)
```

### 2. Normalize and Calculate Dot Products

Compute binned firing rates and sliding window dot products:

```python
from generate_dot_products import *
common_start = find_earliest_timestamp(neuron_data)
adjusted, rates = adjust_timestamps_and_calculate_rates(neuron_data, bin_size=10, common_start=common_start)
save_firing_rates_to_excel(rates)
calculate_and_save_dot_products(rates, window_size=3)
```

### 3. Compute Mean Edge Weights

```python
from compute_edge_weights import compute_mean_edge_weights
compute_mean_edge_weights("actualdata_dot_products_normalized.xlsx", "actualdata_mean_edge_weights.xlsx")
```

### 4. Generate Null Model via ISI Shuffling

```python
from shuffle_isi_generate_null import *
shuffled_spike_trains = {n: shuffle_isi_and_create_spike_trains(calculate_isi(ts), 1000) for n, ts in neuron_data.items()}
save_shuffled_spike_trains(shuffled_spike_trains)
plot_isi_and_spike_trains(neuron_data['neuron 2'], shuffled_spike_trains['neuron 2'])
```

### 5. Test Statistical Significance

```python
from test_edge_significance import *
null_dist = build_edge_weight_distribution(shuffled_spike_trains)
plot_edge_weight_distribution(null_dist)
results_df = test_edge_significance("actualdata_mean_edge_weights.xlsx", null_dist)
```

---

## **Normalization Strategy**

We apply **min-max normalization** to each dot product matrix's upper triangle within a time window. This preserves pairwise similarity structure while scaling all edge weights to the \[0, 1] range, improving interpretability and statistical comparability.

---

## **Requirements**

Install the required packages:

```bash
pip install -r requirements.txt
```

---
