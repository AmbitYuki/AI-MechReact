<div align="center">

<!-- Logo placeholder: replace with actual logo image -->
<img src="assets/logo.png" alt="AI-MechReact Logo" width="80"/>

# AI-MechReact

**AI-Guided Mechanistic Reaction Modeling for Drug Synthesis & Novel Compound Discovery**

![Python](https://img.shields.io/badge/Python-3.9%2B-blue?style=flat-square)
![PyTorch](https://img.shields.io/badge/PyTorch-2.0%2B-orange?style=flat-square)
![RDKit](https://img.shields.io/badge/RDKit-2023%2B-green?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-purple?style=flat-square)
![Status](https://img.shields.io/badge/Status-Under%20Review-yellow?style=flat-square)

</div>

---

## Overview

This repository provides the official implementation of a **three-stage progressive framework** that models chemical bond dynamics for targeted drug synthesis and novel therapeutic compound discovery.

Unlike conventional approaches that treat chemical reactions as discrete structural transformations, this framework explicitly encodes:
- Frontier molecular orbital (HOMO/LUMO) interactions
- Homolytic vs. heterolytic mechanistic pathways
- Thermodynamic feasibility and stoichiometric constraints

```
Reactivity-aware SMILES encoding  →  Hierarchical mechanistic attention  →  Constraint-guided generation
        (Stage 1)                              (Stage 2)                           (Stage 3)
```

---

## Three-Stage Framework

### Stage 1 — Enhanced SMILES Representation
Augments standard SMILES notation with three hierarchical encoding layers:
- **Structural features**: atom type, bond order, aromaticity
- **Reactivity descriptors**: Gasteiger partial charges, HOMO/LUMO contributions (Eq. 1)
- **Chemical context**: multi-scale message passing over 1–3 hop neighborhoods (Eq. 3)

### Stage 2 — Hierarchical Bond Transformation Modeling
Multi-head mechanistic attention layer distinguishes:
- Homolytic cleavage (radical formation)
- Heterolytic cleavage (ionic fragmentation)
- Bond formation via orbital overlap

Orbital interaction scoring uses HOMO-LUMO energy gap (Eq. 6) to bias attention toward chemically favorable reaction centers.

### Stage 3 — Constraint-Guided Reaction Generation
Autoregressive decoder produces complete, chemically valid reaction equations by enforcing:
- Stoichiometric balance (atom conservation)
- Charge conservation (electron balance)
- Valence constraints (maximum valence per atom)
- Thermodynamic feasibility (ΔG threshold)

A controlled novelty mechanism (Eq. 15) systematically explores underrepresented molecular scaffolds for novel compound discovery.

---

## Repository Structure

```
AI-MechReact/
├── datasets/
│   ├── dataset_qm9_quantum_properties.csv         # QM9 benchmark, 134k molecules
│   ├── dataset_qmof_molecular_features.csv        # MOF molecular features
│   ├── dataset_qmof_scaffold_splits.csv           # Scaffold-based splits
│   └── dataset_homo_lumo_orbital_energies.xlsx    # Per-atom orbital energies (DFT)
│
├── networks/
│   ├── bond_transformation_action_network.py      # Bond state update (Eq. 5)
│   ├── progressive_framework_builder.py           # Three-stage builder
│   ├── molecular_graph_augmented_representation.py # Augmented graph G̃=(Ṽ,Ẽ)
│   ├── lora_mechanistic_attention_finetuning.py   # LoRA fine-tuning (Stage 2)
│   ├── constraint_guided_decoder_factory.py       # Validity mask decoder (Eq. 12)
│   ├── pretrained_stage_model_loader.py           # Stage checkpoint loader
│   ├── reactivity_descriptor_feature_utils.py    # χ, q, f_HOMO, f_LUMO utils
│   └── scaffold_reaction_dataset_splitter.py      # Scaffold-based data splitting
│
├── feature_processing/
│   ├── smiles_reactivity_aware_preprocessing.py   # τ_react tokenizer (Eq. 1–3)
│   ├── homo_lumo_descriptor_extractor.py          # HOMO/LUMO per-atom extraction
│   ├── hierarchical_smiles_representation.py      # e_struct ∥ e_react ∥ e_context
│   ├── molecular_fingerprint_reactivity_encoder.py # Morgan/ECFP baseline encoders
│   ├── llm_finetuned_property_regressor.py        # LLM-based property regression
│   └── mechanistic_metric_evaluator.py            # Mechanistic accuracy metrics
│
├── experiments/
│   ├── experiment_reaction_center_prediction.py
│   ├── experiment_bandgap_homo_lumo_prediction.py
│   ├── experiment_lipophilicity_admet_profiling.py
│   ├── experiment_solubility_admet_optimization.py
│   ├── experiment_cross_validation_reaction.py
│   ├── experiment_freesolv_hydration_energy.py
│   ├── experiment_henry_gas_adsorption.py
│   ├── experiment_opv_photovoltaic_properties.py
│   ├── experiment_polymer_scaffold_reactivity.py
│   ├── experiment_photoswitch_electronic_transition.py
│   ├── experiment_water_stability_physiological.py
│   ├── experiment_balasz_scaffold_benchmark.py
│   └── experiment_randomforest_baseline_comparison.py
│
├── train_three_stage_reaction_framework.py        # Main training entry point
├── train_llm_reaction_class_classifier.py         # Reaction class prediction (Eq. 11)
├── train_llm_molecular_property_regressor.py      # ADMET property regression
├── chemical_constraint_validation_utils.py        # Four-constraint validation (Fig. 8)
└── README.md
```

---

## Quick Start

```bash
# 1. Clone the repository
git clone https://github.com/your-org/AI-MechReact.git
cd AI-MechReact

# 2. Install dependencies
pip install -r requirements.txt

# 3. Train the full three-stage framework
python train_three_stage_reaction_framework.py \
    --data datasets/dataset_qm9_quantum_properties.csv \
    --homo_lumo datasets/dataset_homo_lumo_orbital_energies.xlsx \
    --epochs 100 \
    --batch_size 32

# 4. Run a reaction prediction experiment
python experiments/experiment_reaction_center_prediction.py

# 5. Evaluate ADMET-aware compound optimization
python experiments/experiment_lipophilicity_admet_profiling.py
```

---

## Datasets

| File | Description | Source |
|---|---|---|
| `dataset_qm9_quantum_properties.csv` | 134k small molecules, quantum properties (≤9 heavy atoms) | QM9 |
| `dataset_qmof_molecular_features.csv` | Metal-organic framework molecular features | QMOF DB |
| `dataset_qmof_scaffold_splits.csv` | Scaffold-based train/val/test splits | Derived |
| `dataset_homo_lumo_orbital_energies.xlsx` | Per-atom HOMO/LUMO contributions (used in Eq. 1 & 6) | DFT computed |

> Train/validation/test datasets will be made fully publicly available upon paper acceptance.

---

## Dependencies

```
Python >= 3.9
PyTorch >= 2.0
torch-geometric
RDKit >= 2023.03
transformers >= 4.35
peft              # LoRA fine-tuning
numpy
pandas
scikit-learn
openbabel
```

---

## Applications

This framework directly supports:
- **Drug synthesis planning** — automated retrosynthetic pathway generation
- **ADMET optimization** — lipophilicity, solubility, water stability profiling
- **Novel compound discovery** — controlled scaffold exploration via novelty bonus (Eq. 15)
- **Materials science** — organic semiconductors, photovoltaic materials (OPV experiments)
- **Green chemistry** — atom economy optimization through mechanistic route selection

---


<div align="center">
Released under the MIT License · Code and data available upon acceptance
</div>
