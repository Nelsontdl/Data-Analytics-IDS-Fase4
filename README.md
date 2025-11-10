# Data-Analytics-IDS-Fase4
Proyecto de Detección de Intrusiones IoT (IDS) con Random Forest


# Data Analytics Project - IoT Intrusion Detection System (IDS)

Este repositorio contiene la ejecución de la Fase 4 para el curso de Data Analytics, implementando un prototipo de Sistema de Detección de Intrusiones (IDS) basado en el algoritmo Random Forest sobre datos de tráfico IoT (Gotham Dataset).

## 1. Consolidated Execution Log (Steps 1-6)

| Steps | Phase Title | Code to Implement (Fragment) | Objective / Code Explanation |
| :---: | :--- | :--- | :--- |
| **1.1** | **Library Import** | `import pandas as pd`<br>`import numpy as np`<br>`from sklearn.model_selection import train_test_split`<br>... | I load all necessary Python libraries: **Pandas** for data manipulation, **NumPy** for numerical operations, and **Scikit-learn** for all aspects of Machine Learning (splitting, scaling, modeling, and evaluation). |
| **1.2** | **Environment Configuration** | `from google.colab import drive`<br>**`drive.mount('/content/drive')`** | I **mount Google Drive** so the Colab environment can access the CSV dataset file stored in my personal cloud. |
| **2.1** | **Dataset Loading** | `ruta_csv = 'YOUR_PATH_HERE'`<br>**`df = pd.read_csv(ruta_csv)`** | I **read the network traffic CSV file** and store it in the main working variable, the DataFrame named **`df`**. |
| **2.2** | **Initial Exploration** | `display(df.head())`<br>**`display(df['label'].value_counts())`** | I display the first rows of the DataFrame and **confirm the count of the `label` column**, verifying the proportion between `Normal` traffic and the different types of attacks. |
| **3.0** | **Column Verification** | **`print(df.columns.tolist())`** | I print the exact list of column names to **confirm the syntax** (lowercase `label`, names with dots) and use them without errors in the pre-processing steps. |
| **3.1** | **Definition of X (Features) and y (Target)** | `if 'label' in df.columns:`<br>`X = df.drop('label', axis=1)`<br>**`y = df['label']`** | I **define the input variables (X) and the variable to predict (y)**. I assign all columns except `label` to X, and the `label` column to the target variable y. |
| **3.2** | **Categorical Encoding** | **`X = pd.get_dummies(X, columns=['ip.proto'])`** | I apply the **One-Hot Encoding** method to convert the categorical protocol column (`ip.proto`) into a binary numerical format usable by the Machine Learning model. |
| **4.1** | **Data Cleaning (Processing)** | `columns_to_drop = [...]`<br>**`X_processed = X_encoded.drop(...)`** | I **remove columns that are not useful or numerical** (such as timestamps, MAC/IP addresses, *checksums*, and *flags*), as they do not provide direct value or would cause errors in the scaling process. |
| **4.2** | **Data Splitting** | **`X_train, X_test, ... = train_test_split(X_processed, y, test_size=0.2, random_state=42, stratify=y)`** | I divide the dataset into two subsets: **80% for Training** and **20% for Testing**. I use `stratify=y` to ensure that the proportion of attacks remains similar in both sets. |
| **4.3-4.4**| **Feature Scaling** | `scaler = StandardScaler()`<br>**`X_train[numeric_cols] = scaler.fit_transform(...)`** | I **scale the numerical data** using `StandardScaler`. This normalizes the Features so they all have a similar impact on the model, regardless of their magnitude (e.g., packet length doesn't dominate TTL). |
| **5** | **Modeling (IDS Training)** | **`model = RandomForestClassifier(..., class_weight='balanced')`**<br>`model.fit(X_train, y_train)` | I initialize and **train the Random Forest algorithm**. I include `class_weight='balanced'` to compensate for class imbalance (much more normal traffic than attacks), prioritizing the detection of the minority class (Attacks). |
| **6** | **Prediction and Classification Report** | **`y_pred = model.predict(X_test)`**<br>`print(classification_report(y_test, y_pred))` | I use the trained model to generate **predictions** on the test set. The **Classification Report** evaluates the key metrics (*Precision*, *Recall*, *F1-Score*) of our IDS. |

## 2. ## 2. Predictive Modeling Results and IDS Analysis

The Intrusion Detection System (IDS) prototype, developed using IoT traffic data and the **Random Forest** algorithm, was evaluated using a test dataset, yielding the following results:

### Table 1. Classification Report (Random Forest)

| Metric | Class 0 (Normal Traffic) | Class 1 (Attack) |
| :---: | :---: | :---: |
| **Precision** | 1.00 | **1.00** |
| **Recall (Sensitivity)** | 1.00 | **0.70** |
| **F1-Score** | 1.00 | **0.82** |
| **Support (Real Cases)** | 64907 | 152 |
| **Accuracy (General Accuracy)** | | **1.00** |

### Analysis of Critical Metrics for the IDS

The model's performance must be analyzed from a security perspective:

* **Precision in Attacks (1.00):** The 100% precision for the Attack class (1) is **exceptional**. This means that out of all the times the IDS issued an attack alarm, **100% of them were real**. This minimizes the risk of **False Alarms (False Positives)**, which cause operator fatigue.
* **Sensitivity (Recall) in Attacks (0.70):** **Recall** is the most critical metric for an IDS, as it measures the system's ability to detect **real attacks**. A value of **70%** implies that the IDS was able to detect **7 out of every 10 attacks** that occurred in the test set.
* **Missed Detection (False Negatives):** Despite the high Accuracy, the system failed to detect **30%** of the real attacks. This translates to **46 False Negatives** (attacks that the IDS mistakenly classified as Normal traffic). This is the main **security gap** identified and must be the focus of continuous model improvement.

---

### Conclusions

The Intrusion Detection System (IDS) prototype implemented with the **Random Forest** algorithm demonstrated an **Accuracy** of **1.00**, indicating a high general classification capability.

#### Performance and Risk

1.  **Attack Detection (Focus on Recall):** The model achieved a **Recall of 70% (0.70)** in the critical class (Attack). This demonstrates the viability of using Data Analytics for security in IoT environments. However, the remaining 30% of attacks (**46 False Negatives**) were not detected, representing a security gap that must be addressed.
2.  **Risk of False Alarms (Focus on Precision):** The **Precision** in attack detection was **1.00**. This is exceptional and means the IDS is highly reliable, and the risk of generating false alarms is minimal, which is a major strength for avoiding operator fatigue in a real-time monitoring system.

#### Strengths and Weaknesses:

* **Fortaleza (Strength):** Extremely low **False Positive** rate (only 1).
* **Debilidad (Weakness):** The **Recall of 70%** is improvable. It is recommended that future phases test oversampling techniques like **SMOTE** to balance the data and improve the detection of the remaining 30% of attacks.

#### Final Deduction:

The execution of the project demonstrated that the Random Forest model is a viable and robust solution for intrusion detection in IoT environments, standing out for its high reliability and minimal generation of False Alarms. The main challenge is to **improve Recall** to reduce the number of attacks that slip past the system, achievable through data balancing techniques (like SMOTE) or the exploration of other Machine Learning algorithms.
