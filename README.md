Machine Learning-Based Analog Circuit Fault Diagnosis (ACFD)

## Overview
This project bridges the gap between hardware circuit analysis and artificial intelligence. It demonstrates how machine learning algorithms can be trained to detect and classify "soft faults" (parametric degradations like aging components) in an analog circuit by analyzing its frequency response.

The target circuit is a **Sallen-Key Low-Pass Filter** with an amplifier stage. By utilizing AC Sweep (Bode Plot) data generated in Proteus and applying Monte Carlo-style data augmentation in Python, a **Random Forest Classifier** was trained to achieve **100% fault detection accuracy**.

## Technologies & Tools
*   **Circuit Simulation:** Proteus Design Suite (ISIS)
*   **Machine Learning:** Python (Google Colab)
*   **Libraries:** `pandas`, `numpy`, `scikit-learn`, `matplotlib`, `seaborn`

## Project Workflow

### 1. Circuit Design & Simulation
A Sallen-Key Low-Pass Filter was designed in Proteus using an LM741 Operational Amplifier. The circuit was configured with a non-inverting gain of 2 (6.02 dB) using feedback resistors ($R3 = R4 = 330\Omega$). 
Three distinct circuit conditions were simulated using an AC Sweep analysis (10 Hz to 10 MHz) to extract the frequency response curves:
*   **Healthy Baseline (Class 0):** Nominal component values ($R1=330\Omega$, $C1=1nF$).
*   **R1 Faulty (Class 1):** R1 increased to $600\Omega$ (simulating thermal degradation/tolerance shift). Resulted in increased Q-factor and slight frequency shift.
*   **C1 Faulty (Class 2):** C1 decreased to $0.5nF$ (simulating capacitor drying). Resulted in a massive resonance spike before the cutoff frequency.

### 2. Overcoming the "Data Shape Trap"
Initially, feeding the raw point-by-point frequency/gain data into the model resulted in a 0% accuracy. The model failed because, at lower frequencies, all three conditions shared the exact same gain (6.02 dB), making individual points indistinguishable. 
*Solution:* The approach was shifted from analyzing *individual data points* to analyzing the *entire frequency curve* as a single holistic feature set.

### 3. Data Augmentation (Monte Carlo Approach)
To train the model effectively without running hundreds of manual simulations, a synthetic data generation script was implemented in Python. 
By adding Gaussian noise (representing natural measurement tolerances) to the three baseline curves, **300 unique circuit simulations** (100 per class) were generated. Each row in the final dataset represented a complete frequency response curve.

### 4. Machine Learning Model Training
The augmented dataset was split into 80% training and 20% testing sets. A **Random Forest Classifier** (`n_estimators=100`) was selected for its robustness in handling high-dimensional feature sets (where each frequency step acts as a feature).

## Results
The Random Forest model successfully learned the unique "fingerprints" (peaking characteristics and resonance spikes) of each fault condition.

*   **Model Accuracy:** **100.00%**
*   **Confusion Matrix:** The model achieved perfect classification across the test set, successfully distinguishing between a healthy circuit, a degraded resistor, and a dried capacitor with zero false positives or false negatives.

## How to Run
1. Clone this repository.
2. Open the provided Jupyter Notebook (`ACFD_RandomForest.ipynb`) in Google Colab or your local environment.
3. Ensure the three baseline CSV files (`healthy_baseline.csv`, `r1_faulty.csv`, `c1_faulty.csv`) are in the same directory.
4. Run all cells to see the data augmentation process and the model's confusion matrix.
