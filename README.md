# Lightweight IoT Intrusion Detection with Logistic Regression using RT-IoT2022


## NON-TECHNICAL EXPLANATION OF YOUR PROJECT


The objective of this project is to build an interpretable, edge-friendly classifier that labels each network flow as:
    • Normal (benign device behavior)
    • DDoS (SYN flood / Slowloris) [Network Denial of Service, Technique T1498 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1498/)

I tried to achieve that by implementing and optimizing a Lightweight Intrusion Detection System (IDS) for IoT environments using the [RT-IoT2022 ](https://archive.ics.uci.edu/dataset/942/rt-iot2022) dataset. This project apply Logistic Regression as a lightweight classification method suitable for resource-constrained IoT devices. I used a Bayesian Optimization library https://bayesian-optimization.github.io/BayesianOptimization/3.1.0/basic-tour.html to tune two Logistic Regression hyperparameters: the Regularisation strength and the penalty.

Implementation details: Python code for model fitting, hyperparameter tuning, and visualization of optimization progress.

## DATA
The RT-IoT2022 dataset was used, this is a comprehensive collection of network traffic generated from a real-time IoT infrastructure that integrates various IoT devices such as smart bulbs, temperature sensors, and LEDs. It includes both benign and malicious traffic, capturing realistic attack behaviors like DoS_SYN_Hping and DDoS_Slowloris. Network data were collected using the Zeek network monitor and Flowmeter plugin, providing bidirectional flow attributes that reflect real-world communication patterns. This dataset is particularly valuable for developing and evaluating lightweight intrusion detection systems in resource-constrained IoT environments, as it balances complexity, realism, and diversity of attack types.

The data was grouped based on the type of network activity recorded in the dataset. Each traffic flow was labeled as either normal or as one of several attack types (e.g., DoS_SYN_Hping, DDoS_Slowloris, etc.). For simplicity and to improve model performance, similar attack categories were sometimes aggregated into broader groups such as DoS, DDoS, and Benign. This grouping allowed the Logistic Regression model to focus on distinguishing between 4 categories as can be seen in the table bellow, reducing class imbalance and simplifying classification for resource-limited IoT scenarios.

Attack_type_agg
DOS_SYN_Hping       94659
Other Compromise    19563
Not Compromised      8361
DDOS_Slowloris        534

Ref: S., B. & Nagapadma, R. (2023). RT-IoT2022  [Dataset]. UCI Machine Learning Repository. https://doi.org/10.24432/C5P338.


## MODEL 
For this project, we implemented a Logistic Regression model as a lightweight yet effective approach for intrusion detection in IoT environments. Logistic Regression was chosen because it offers low computational complexity and easy deployment on resource-constrained devices, unlike more complex deep learning models that require significant processing power. Despite its simplicity, it performs well in multi-class classification tasks, making it suitable for distinguishing between the forth categories of network traffic. Moreover, Logistic Regression provides interpretable results, allowing us to understand the contribution of each feature to the classification decision, which is crucial for maintaining transparency in security applications.

## HYPERPARAMETER OPTIMSATION
In this project, I performed Bayesian Optimization to tune the key hyperparameters of the Logistic Regression model. The main hyperparameters optimized were:

* **Regularization strength (C):** controls the trade-off between fitting the training data and maintaining generalization.
* **Penalty type:** determines the form of regularization (e.g., *L1* or *L2*) to prevent overfitting and reduce feature noise.

## RESULTS
The results show that the Logistic Regression model achieved an overall accuracy of 91.84%, demonstrating strong performance in identifying most attack types. The DoS_SYN_Hping class was detected with very high precision (0.9994) and recall (0.9862), indicating that the model can effectively recognize this type of attack. However, due to significant class imbalance in the dataset, the DDoS_Slowloris class had a much lower precision (0.1052) and F1-score (0.1897), despite its high recall (0.9626), meaning the model tends to overpredict this class. In future phases of the project, I plan to apply oversampling or class weighting techniques to mitigate this imbalance and improve the precision and F1-score for minority classes. Overall, the results suggest that Logistic Regression performs well for dominant attack types but needs further tuning to achieve balanced detection across all categories.


## (OPTIONAL: CONTACT DETAILS)
For more information about this you can contact me to inge.gabs@gmail.com or follow me on https://www.linkedin.com/in/gabriela-espinosa-39a75462/ 

