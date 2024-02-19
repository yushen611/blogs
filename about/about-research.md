---
title: 'Research Experience'
date: 2022-11-25 21:27:36
tags: [TransCGAN,Mocap]
published: true
hideInList: false
feature: 
isTop: true
---
📖 Interests:ML,DL,Natural Language Processing,Graph Neural Networks,Generative Adversarial Networks, Artificial Intelligence for Security,Fine-tuning Large Language Models,BlockChain
* 2023:Identifying Blockchain Transaction Risks Based on Graph Neural Networks and Large Language Models.
* 2023:Malicious SQL intelligent interceptor in Web applications(ML)
* 2023:Mocap Data Generation and Virtual Character Redirection Based on TransCGAN
* [Click to see more...](https://yushen611.github.io/post/about-research/)   
<!-- more -->
<meta name="referrer" content="no-referrer"/>

I'm interested in Deep Learning,Mocap Data Generation,SQL Injection Attack Detection,Model Compression,Smart Medical etc.

# 【2023-DL for security-Reseach】Identifying Blockchain Transaction Risks Based on Graph Neural Networks and Large Language Models

> research in ntu cslab

* **Summary** :To precisely track on-chain risks, we employ the advanced LLM (LLaMA2) language model for text embedding and use a Graph-Transformer for deep semantic analysis. This method both categorizes and scores transaction risks.

* **Fine-Tuning LLM**:By fine-tuning the LLM language model, we enhanced its ability to semantically map transaction text.

* **Optimize prompts with GOT**:Using the latest Prompt Optimization Technology (GOT), we significantly improved the quality of text fed into the large model.

* **Training Adoptor for semantic mapping of transactions**:By combining GNN and Transformers, we enhanced the model's understanding of transactions through precise semantic mapping.



# 【2023-DL for security-Reseach】Malicious SQL intelligent interceptor in Web applications

> **Outcome: the source code is for company,but docker image can be used**
>
> [88759863/sqli-detector-java - Docker Image | Docker Hub](https://hub.docker.com/r/88759863/sqli-detector-java)
>
> [88759863/sqli-detector-python - Docker Image | Docker Hub](https://hub.docker.com/r/88759863/sqli-detector-python)

* **web visualization**

<img src="https://gitee.com/yushen611/img/raw/master/image-20230907125115509.png" alt="image-20230907125115509"  />

This paper aims to study and enhance the capabilities of **SQL injection interception** and proposes a viable solution using machine learning-based classification models. For this purpose, the paper accomplishes the following:

1. **Collected and organized the largest publicly available SQL injection dataset**, consisting of 140,000 records, providing a reliable and effective data source for research on SQL injection interceptors.
2. **Designed and compared two query string vectorization methods**: the tf-idf method based on term frequency statistics and the word2vec model for word vectorization. Experimental results indicate that using the Random Forest model with tf-idf and SVD dimensionality reduction achieved the highest accuracy of 97%.
3. **Compared five different machine learning models and experimented with various query string vectorization methods**. The results show that the Random Forest model, when combined with tf-idf and SVD dimensionality reduction, can achieve a maximum accuracy of 97%. In practical applications, the word2vec model and related methods are more widely applicable and feasible, achieving an accuracy rate of up to 95%.
4. **Implemented a rule-based SQL injection interceptor using syntax analysis for comparative experiments**. The results show that its accuracy is only 78%, making machine learning-based methods and the word2vec model more preferable.
5. **Developed a user-friendly SQL injection interceptor explanation system** that details the underlying algorithms and provides public download links for Docker images of both rule-based and ML-based SQL injection interceptors.



<details>
<summary>Click to view details</summary>

SQL injection attacks exploit vulnerabilities in web applications to compromise database information and web application security. Developing effective SQL injection interceptors to detect and intercept user-submitted SQL statements is crucial for strong defense.

This thesis aims to enhance SQL injection interception capabilities by exploring various interception methods and proposing a machine learning classification model. A public SQL injection dataset containing 140,000 records was collected and organized, serving as a reliable source for research. Two query string vectorization methods were designed and compared using statistical frequency of terms and word2vec model. Singular value decomposition and positional encoding were applied to enhance dimensionality reduction and reduce noise. The edit distance method was employed for mapping word vectors and enabling vectorization of arbitrary strings.

We tested five machine learning models, including logistic regression, SVM, Naive Bayes, random forests, and decision trees, on different query string vectorization methods. Results show that the tf-idf method with SVD dimensionality reduction combined with random forests achieved the highest accuracy rate of 97%, while word2vec with convolutional dimensionality reduction combined with random forests had accuracy of 95%.

We tested a rule-based SQL injection interceptor and a machine learning model and found the rule-based model to have an accuracy rate of only 78%. Therefore, we believe that the machine learning classification model, which uses the word2vec model, is more practical and effective for SQL injection interception. Our research can contribute to improving SQL injection interception capabilities and network security. We have also developed a demo system for the SQL injection interceptor, explaining the principles and providing download links for Docker images of both rule-based and machine learning-based SQL injection interceptors.

* **Architecture of the interceptor** 

![image-20230907124613277](https://gitee.com/yushen611/img/raw/master/image-20230907124613277.png)

* **New method for word-Embedding**

![image-20230907124732043](https://gitee.com/yushen611/img/raw/master/image-20230907124732043.png)

* **Result**

![image-20230907125025187](https://gitee.com/yushen611/img/raw/master/image-20230907125025187.png)

</details>

# <center> 【2022-DL-Reseach】Mocap Data Generation and Virtual Character Redirection Based on TransCGAN</center>

> **Outcome**: **EI Paper** , be invited to be **Reviewers**

In the section II, the basic principles and advantages of GAN, CGAN and Transformer are explained and the network architecture of **TransCGAN is proposed**. In the section III, we provide a detailed description of the data pre-processing and model training process. In the section IV, we provide a **qualitative and quantitative analysis** of the generative power of the model, and in the next section an outlook on the application scenarios of the model.

<details>
<summary>Click to view details</summary>

## TransCGan Model Structure



![Fig1. Human skeleton model](https://gitee.com/yushen611/img/raw/master/HumanModel.png)

​																							Fig1. Human skeleton model



<img src="https://gitee.com/yushen611/img/raw/master/TRANSCGAN.jpg" alt="Fig2. Generator & Discriminator" style="zoom:50%;" />

​																					Fig2. Generator & Discriminator


## Different effects with one action

It was discovered that the model generates a variety of actions with the same label name. Using ‘Standing Up’ as an illustration (Figure 3), the diverse data provided by the model reveal that the spatial locations of the joints corresponding to different frame serial numbers are not identical, but the overall continuity is consistent with the action's intent.

<img src="https://gitee.com/yushen611/img/raw/master/generatadData_01.jpg" alt="Fig3. Different generation of the model for the Standing Up action" style="zoom:50%;" />

​													Fig3. Different generation of the model for the Standing Up action


## Different movements with different effects

the model was able to create the various actions accurately. Six actions were generated in total (Figure 4), and the findings indicated that each action was generated smoothly and in accordance with its meaning.



<img src="https://gitee.com/yushen611/img/raw/master/generatadData_02.jpg" alt="Fig4. Generative movements of the model for 6 types of movements(Walking Forward, Walking Back, Standing Up, Sitting Down, Picking Up and Body Rotation)" style="zoom: 50%;" />

​				Fig4. Generative movements of the model for 6 types of movements(Walking Forward, Walking Back, Standing Up, Sitting Down, Picking Up and Body Rotation)

</details>