# Model Card

Basedn on [example Google model cards](https://modelcards.withgoogle.com/model-reports). 

## Model Description

**Input:** The inputs of the model are the network flow features extracted from the RT-IoT2022 dataset (See ref). Each input corresponds to a set of numerical attributes that describe the behavior of network traffic between IoT devices. 
The data was grouped based on the type of network activity recorded in the dataset. Each traffic flow was labeled as either normal or as one of several attack types (e.g., DoS_SYN_Hping, DDoS_Slowloris, etc.). For simplicity and to improve model performance, similar attack categories were sometimes aggregated into broader groups such as DoS, DDoS, and Benign. This grouping allowed the Logistic Regression model to focus on distinguishing between 4 categories as can be seen in the table bellow, reducing class imbalance and simplifying classification for resource-limited IoT scenarios.

Attack_type_agg
DOS_SYN_Hping       94659
Other Compromise    19563
Not Compromised      8361
DDOS_Slowloris        534

“DOS_SYN_Hping” refers to a Distributed Denial of Service (DDoS) attack generated using the Hping3 tool, where multiple attacker devices send a flood of TCP SYN packets to overwhelm the victim system. It’s sometimes called a SYN flood attack, one of the most common DoS/DDoS variants.

The second attack type is "DDOS_Slowloris" an application-layer Denial-of-Service technique that exhausts a server’s ability to accept new connections by opening many long-lived, slow HTTP connections and sending partial HTTP headers very slowly so connections remain open and consume server resources. Unlike SYN floods (network/transport layer), Slowloris keeps TCP handshakes and connections alive with minimal per-packet payload, aiming to tie up worker threads or file descriptors on the target.

It was selected a minimal, interpretable feature subset (≈8 core variables) that captures both volumetric and application-layer attack behaviour while staying lightweight enough for an edge-deployed Logistic Regression model.

 These include flow duration, packet rate, SYN flag count, payload throughput, header size, active and idle times, and the down-to-up ratio. Together, they describe complementary aspects of network behaviour: connection length and activity timing (for Slowloris), transport-layer aggressiveness (for SYN floods), and overall balance and periodicity (for normal IoT traffic). This approach ensures that the model remains interpretable and lightweight, allowing direct attribution of Logistic Regression coefficients to specific network phenomena while maintaining compatibility with real-time, resource-constrained IoT environments.

| Feature                    | Normal              | DDoS-SYN (DOS_SYN_Hping) | DDoS-Slowloris         |
| -------------------------- | ------------------- | ------------------------ | ---------------------- |
| `flow_duration`            | Short / periodic    | Short bursts             | Very long              |
| `flow_SYN_flag_count`      | Low (1 per session) | Very high                | Moderate               |
| `fwd_pkts_per_sec`         | Steady              | Very high                | Very low               |
| `payload_bytes_per_second` | Moderate            | High                     | Very low               |
| `fwd_header_size_max`      | Regular             | Small, fixed             | Small, irregular       |
| `active.avg`               | Regular pattern     | Short bursts             | Long periods           |
| `idle.avg`                 | Moderate            | ~0                       | High                   |
| `down_up_ratio`            | Balanced            | High asymmetry           | Balanced / slight skew |

Ref: S., B. & Nagapadma, R. (2023). RT-IoT2022  [Dataset]. UCI Machine Learning Repository. https://doi.org/10.24432/C5P338.


**Output:** The output of the model is a predicted class label that identifies the nature of each network flow. Specifically, the Logistic Regression model outputs one of several categories representing different traffic types in the RT-IoT2022 dataset — for example, “Not Compromised”, “DoS_SYN_Hping”, “DDoS_Slowloris”, or “Other Compromise.”

Internally, the model computes a probability score for each class, indicating how likely a given network flow belongs to that category. The class with the highest probability is selected as the final output. These predictions can be used by an IoT Intrusion Detection System to flag malicious traffic in real time, supporting network administrators in identifying and responding to security threats.

**Model Architecture:** The model architecture used in this project is based on a Logistic Regression classifier, a simple yet effective linear model suitable for lightweight intrusion detection in IoT environments. Logistic Regression works by learning a linear relationship between the input features (network flow metrics) and the probability of each output class.

This architecture was chosen because it is computationally efficient, requires minimal memory, and can be easily deployed on resource-constrained IoT devices. Additionally, its interpretable coefficients make it possible to understand how each feature influences the classification decision, an important property in cybersecurity applications where explainability is essential.

## Performance

Give a summary graph or metrics of how the model performs. Remember to include how you are measuring the performance and what data you analysed it on. 

## Limitations

The main limitations of the model stem from both the simplicity of the Logistic Regression algorithm and the nature of the dataset. Because Logistic Regression assumes a linear relationship between input features and the output, it may struggle to capture the complex, non-linear patterns often present in network traffic data. This limits its ability to detect sophisticated or stealthy attacks.

Additionally, the RT-IoT2022 dataset suffers from class imbalance, with certain attacks (like DDoS_Slowloris) underrepresented compared to others (like DoS_SYN_Hping). As a result, the model performs well on dominant classes but shows low precision and F1-score for minority attack types.

Other limitations include potential overfitting to specific device behaviors, reduced scalability when facing large or evolving IoT environments, and limited adaptability to unseen attack variants.


## Trade-offs

The main trade-off of the model lies between efficiency and detection accuracy. Logistic Regression was selected for its lightweight computational footprint, making it ideal for real-time intrusion detection on IoT devices with limited processing power and memory. However, this simplicity comes at the cost of reduced flexibility in modeling complex, non-linear attack patterns, which can lead to lower performance in detecting subtle or evolving threats.

Another trade-off is related to class imbalance: while the model achieves very high precision and recall for frequent attacks such as DoS_SYN_Hping, it struggles with rare attack types like DDoS_Slowloris, where performance metrics drop significantly. Techniques such as oversampling  could improve this, but they would also increase computational cost and potentially affect real-time detection capabilities.

Overall, the model provides a good balance between interpretability, speed, and resource efficiency, but at the expense of comprehensive detection coverage for all attack types.
