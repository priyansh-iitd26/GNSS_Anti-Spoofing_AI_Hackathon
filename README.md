# GNSS_Anti-Spoofing_AI_Hackathon

**Problem Understanding**

The goal is to create an AI-based solution that can find GNSS spoofing attempts by looking at the characteristics and inconsistencies of the signals. The biggest problem is that the classes are very unbalanced; only about 14.28% of the data set contains spoofed signals. The solution must put robustness, practical feasibility, and no data leakage at the top of the list.

**Feature Engineering**

To get the raw GNSS signals ready for ML, the following practical preprocessing and feature engineering steps were taken:

_Data Type Unification_: Initially, multiple important numerical features (like Carrier_Doppler_hz, Pseudorange_m, EC, and LC) were loaded as object types because the string values were broken. To make math work, these were forced to be float64.

_Imputation of Missing Values_: Because GNSS data is sequential, missing continuous metrics were filled in using linear interpolation, forward-filling, and backward-filling to keep the physical signal going.

_Anomaly Flagging_: A custom binary feature called is_doppler_zero was created to clearly mark physical impossibilities (a constant 0.0 Hz Doppler shift), which is a strong sign of crude spoofing attacks.

To make sure that the model doesn't remember the exact times of clustered spoofing attacks and to strictly enforce fairness, raw temporal markers (time, RX_time, and TOW) were removed before training.

**Model Architecture**

A Random Forest Classifier is what the core detection engine uses.
It is a strong, tree-based ensemble method that can naturally handle complicated, non-linear interactions between tabular GNSS features, like the links between Early, Late, and Prompt correlators. The architecture uses inverse-proportional class weighting (class_weight='balanced') instead of synthetic oversampling methods to deal with the extreme class imbalance. This helps avoid mode collapse and overfitting on noisy signal distributions.

**Training Methodology**

The training pipeline was designed with a strict focus on preventing data leakage:

_Stratified Splitting_: The dataset was divided into three parts: Training (70%), Validation (15%), and internal Test (15%). This was done in a way that made sure the 14.28% attack ratio stayed the same across all splits.

_Strict Scaling_: A StandardScaler was only fitted to the training set to find the mean and variance. We then used these parameters to change the validation and test sets, making sure that no data from the future leaked into them.

_Evaluation Metric_: The **Weighted F1 Score** was used to check that performance was balanced across the imbalanced classes when making hyperparameter choices.

**Justification of Design Decisions**

To ensure engineering stability, reproducibility, and practical feasibility, a Random Forest baseline was chosen over deep learning architectures (like CNNs or LSTMs). Initial trials demonstrated that advanced synthetic augmentation (SMOTE) compromised the model's capacity to discern the genuine spoofing signature, suggesting significant overlap within the feature space. So, using algorithmic class weighting was the best and most general way to protect against changes in data distribution.
