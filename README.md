# GNSS_Anti-Spoofing_AI_Hackathon

**Problem Understanding**
The goal is to create an AI-based solution that can find GNSS spoofing attempts by looking at the characteristics and inconsistencies of the signals. The biggest problem is that the classes are very unbalanced; only about 14.28% of the data set contains spoofed signals. The solution must put robustness, practical feasibility, and no data leakage at the top of the list.

**Feature Engineering**
To get the raw GNSS signals ready for ML, the following practical preprocessing and feature engineering steps were taken:

_Data Type Unification_: Initially, multiple important numerical features (like $Carrier_Doppler_hz$, $Pseudorange_m$, $EC$, and $LC$) were loaded as object types because the string values were broken. To make math work, these were forced to be float64.

_Imputation of Missing Values_: Because GNSS data is sequential, missing continuous metrics were filled in using linear interpolation, forward-filling, and backward-filling to keep the physical signal going.

_Anomaly Flagging_: A custom binary feature called $is_doppler_zero$ was created to clearly mark physical impossibilities (a constant 0.0 Hz Doppler shift), which is a strong sign of crude spoofing attacks.

To make sure that the model doesn't remember the exact times of clustered spoofing attacks and to strictly enforce fairness, raw temporal markers ($time$, $RX_time$, and $TOW$) were removed before training.
