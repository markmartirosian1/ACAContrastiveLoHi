Experiment: 

Binary hit-identification (matching the Lo-Hi benchmark's Hi task), but training on continuous activity labels from ChEMBL as the primary signal. These continuous labels are used as opposed to the binary ones because they are more precise, and also because continuous magnitude is what makes ACA's activity-cliff-aware contrastive loss possible: a binary label alone can't distinguish a small activity difference from a large one, which is exactly the information that the triplet loss needs to weight its margins correctly. Binary metrics (PR AUC, accuracy) are computed by thresholding the continuous prediction against an empirically discovered cutoff, not through separate classification supervision. Tested on matched-continuous versions of three Lo-Hi Hi datasets: KDR-Hi, DRD2-Hi, and Sol-Hi.
Method
Corrected ACA triplet loss: uncapped margin (|y_a - y_n| - |y_a - y_p|), ordinary (not squared) L2 distance, explicit c_l/c_u conditional mining (no arbitrary structural-similarity gate), active-triplet-only per-anchor averaging, all matching the original paper.
Paired baseline/ACA comparison: identical model initialization, batch order, and dropout seed within each fold -- only the ACA loss term differs.
Non-overlapping k-fold splits: every test molecule evaluated exactly once across the union of folds, making the pooled comparison statistically valid (not just descriptive).
Symmetric checkpoint-eligibility window for baseline and ACA
PR AUC (not ROC AUC or accuracy) as the primary binary metric, following the original Lo-Hi paper's own choice for imbalanced hit-identification tasks.
Primary result is the per-fold paired comparison (mean delta, sign consistency across folds)
Data
Three CSVs, one per dataset, each row a molecule matched from ChEMBL to the original Lo-Hi Hi binary label/split. Columns: smiles, log_activity_value (continuous training target), existing_binary (original Lo-Hi label), rederived_binary / label_mismatch (binary label re-derived from the continuous value via an empirically discovered threshold, and whether it agrees with existing_binary), split_hi (the original Lo-Hi Hi-splitter assignment -- not used for the k-folds here, kept for reference and the Hi-separation diagnostic each notebook prints). KDR-Hi and DRD2-Hi's matched CSVs are included in data/; Sol-Hi's notebook builds its matched dataset at runtime from the Lo-Hi and Computational-ADME source repos (see Section 5-8 of that notebook), so no local CSV is needed for it.
Results
KDR-Hi (3-fold CV): low-similarity bucket (sim_to_train < 0.4) accuracy delta +0.0304, 3/3 folds positive; PR AUC delta +0.0095, 2/3 folds positive. Whole-dataset PR AUC delta +0.0008, 2/3 folds positive, so the effect is concentrated in the low-similarity subset, as hypothesized.
DRD2-Hi (3-fold CV): low-similarity bucket PR AUC delta +0.0150, 3/3 folds positive -- the cleanest, most consistent result across both datasets. Whole-dataset PR AUC delta -0.0005 (essentially flat), 2/3 folds positive.
Sol-Hi: baseline PR AUC lands at/near the positive-class prevalence (~0.21-0.24) on the true held-out test set - across every variant tried (class-weighted loss, isolated ranking loss, PR-AUC-based checkpoint selection, an auxiliary classification head with direct binary supervision). The best result came from the class-weighted-MAE run: low-similarity bucket PR AUC delta +0.0018 (2/3 folds positive), accuracy delta +0.0104 (2/3 folds positive), ROC AUC delta +0.0049 (3/3 folds positive) -- whole-dataset PR AUC delta was still essentially flat (-0.0005, 1/3 folds positive). Small, mixed signals at best; still under investigation. 
Papers
Steshin, S., 2023, "Lo-Hi: Practical ML Drug Discovery Benchmark." 
Shen et al, 2024, "Activity Cliff-Informed Contrastive Learning for Molecular Property Prediction"

