# 个人信息 

| type   | info                         |
| ------ | ---------------------------- |
| home   | 新加坡                       |
| email  | ywy_hfut@163.com             |
| phone  | +86 15347118275              |
| github | https://github.com/yushen611 |

# 教育背景

### 合肥工业大学

* **title**
  - **Period:** 2019.09-2023.06
  - **University**: 合肥工业大学
  - **Degree:** 软件工程（本科）
  
- **content**
  - 成绩与排名：91.02/100 (排名 5/169)
  - 获奖与荣誉：国家奖学金，校一等奖学金，优秀三好学生，计算机设计大赛国赛三等奖

### 南洋理工大学

* **title**
  - **Period:** 2023.08-2024.07
  - **University**: 南洋理工大学
  - **Degree:** 区块链技术（硕士）

- **content**
  - 在NTU CSLab研究大型语言模型(LLMS)用于监控和预警区块链交易风险。

# 实习经历

## 科大国创云网科技有限公司

* **title**
  - **Period:** 2021.12-2022.02
  - **Company**: 科大国创云网科技有限公司
  - **Position:** 职责: Java后端开发
  
- **content**
  - 成果：已获得《智能化企业财务报销系统》软件著作权（第一作者）。
  - 增值税发票OCR识别：使用YOLOv3 + CRNN + CTC实现发票OCR识别与自动录入。
  - 微服务架构：使用Spring Cloud实现微服务架构，通过gRPC调用基于Flask框架搭建的发票中心接口。
  - 单点登录功能：利用Spring Security实现单点登录功能（OAuth2协议），进行密码加密管理与防止跨站点请求伪造（CSRF）。
  - 身份认证与授权：使用JWT进行身份认证与授权，实现了服务端的无状态认证。
  - 防止缓存雪崩：利用Redis实现分布式互斥锁，有效防止缓存雪崩。

# 项目经历

## 项目经历:开发

### 在线视频播放系统：微服务+分布式架构

* **title**
  - **Period:** 2023.03-2023.06
  - **Role:** Golang后端开发
- **content**
  - 高稳定性的微服务架构：使用gin+gorm框架搭建了User service与Video-stream两个微服务，使用Consul进行服务注册和发现，使用GRPC实现微服务间的通信，并结合Weighted Round Robin Algorithm实现负载均衡，将RPC错误率控制在3%以下。
  - 分布式系统日志：使用ELK+Kafka实现了分布式系统的日志采集与可视化分析。
  - 分布式系统指标监控：使用Prometheus+Grafana+Alertmanager实现了服务器与接口的指标监控与预警。
  - 压测与瓶颈分析：通过Jmeter进行压力测试后，使用ELK日志记录和PPROF火焰图进行性能瓶颈分析，并使用Redis、实施分页查询、设置数据库索引优化慢SQL查询。
  - 实时评分榜功能并发优化：通过分析出实时评分排名是读多写少的场景，对mysql主从复制以进行读写分离，再利用Kubernetes编排多个微服务POD到两个2核4G服务器，成功将接口QPS提升至超过1500。
  - 持续集成/持续部署：通过使用Docker和Github Action实现CI/CD，将从代码更改到部署的整个过程压缩至2分钟内。

## 项目经历:AI

### 基于大语言模型的区块链交易风险监测

* **title**
  - **Period:** 2023.08-2023.12
  - **Location:** 南洋理工大学CSLAB
- **content**
  - 概要：为了精准监控链上风险交易，我们创新性地采用了LLM（LLaMA2）大型语言模型进行交易文本的词嵌入，并通过Graph-Transformer模型进行深度语义分析。这一设计不仅实现了交易风险的分类，还提供了交易风险评分。
  - 微调大语言模型：通过微调大型语言模型，我们成功优化了其对交易文本的语义映射能力。
  - 使用GOT进行Prompt优化：运用最新的Prompt优化技术（GOT）,显著提升了送入大模型的文本质量。
  - 训练Adoptor以实现交易语义映射：综合应用图神经网络（GNN）与Transformer模型，我们实现了从文本语义到交易语义空间的精准映射，从而让模型对交易数据具有更深入的理解能力。

### AI恶意SQL注入检测器

* **title**
  - **Period:** 2023.03-2023.06
  - **Location:** 合肥工业大学AI实验室
- **content**
  - 词嵌入优化：采用了word2vec算法并融入位置编码，从而实现了对SQL查询字符串的高效向量化。进一步地，我们通过一维卷积神经网络有效降低了句子向量中的噪声成分。
  - 准确率最高的ML-SQL注入检测器：模型训练采用了随机森林算法，在最大的数据集上达到了99%的准确率，是目前ML类SQL注入检测器中准备率最高的算法。
  - 比DL更快的生成速度:相较于深度学习（DL），传统机器学习（ML）仅需CPU便可高效运算，从而实现更快的处理速度。这一优势使其在工业应用中更具实用性。
  - 整理了世界最大的SQL注入公开数据集：https://www.kaggle.com/datasets/gambleryu/biggest-sql-injection-dataset
  - 公开了算法的DOCKER镜像
  
    * JAVA版:https://hub.docker.com/r/88759863/sqli-detector-java
  
    * Python版:https://hub.docker.com/r/88759863/sqli-detector-python


### AI文字到视频生成器

* **title**
  - **Period:** 2022.03-2022.12
  - **Location:** 合肥工业大学虚拟现实实验室
- **content**
  - 文字生成视频功能：该算法采用高度优化的TransCGAN模型，能够根据文字指令即时生成定制的人物动作视频。在性能方面达到了全球领先标准，同时展现出强大的工业应用潜力。
  - 增强对数据时空序列信息的提取: 发表的TransCGAN模型，成功地融合了CGAN与Transformer，专为高质量人体动作生成设计。该模型在速度和质量上均达到了业界领先水平，具备出色的工业应用潜力。
  - 成果:EI会议论文《TransCGan-based human motion generator》(第一作者)
  - 概要链接: [https://yushen611.github.io/post/about-research/](https://yushen611.github.io/post/about-research/)

# 技能

## 技能 (SOFTWARE)

- **概要:** Golang后端，分布式系统，DevOps，高性能软件开发
- **外语:** IELTS 6.5, GRE 329+3.5
- **编程语言:** Go, Python, Java, C++
- **开发框架:** GIN, GORM
- **中间件:** Consul, GRPC, Redis, RocketMQ, Prometheus, Grafana, Altermanager, EKL
- **运维:** Linux, CI/CD, Docker, Kubernetes

## 技能 (AI)

- **概要:** 深度学习与机器学习，自然语言处理，大语言模型微调
- **外语:** IELTS 6.5, GRE 329+3.5
- **编程语言:** Go, Python, Java, C++
- **AI:** Pytorch, Opencv, LLAMA2, Transformer, GAN

## 技能 (BC)

- **概要:** 区块链技术，Golang后端，分布式系统，高性能软件开发
- **外语:** IELTS 6.5, GRE 329+3.5
- **编程语言:** Solidity, Typescript, Go, Python, Java, C++
- **开发框架:** Web3.js, HardHat, GIN, GORM, Spring Cloud, Spring Boot
- **数据库:** MySQL, ClickHouse
- **中间件:** IPFS, Consul, GRPC, Redis, RocketMQ, Prometheus, Grafana, Altermanager, EKL
- **运维:** Linux, CI/CD, Docker, Kubernetes

# 论文 && 专利 && 软著

- **论文:** "TransCGAN-based Human Motion Generator", International Conference on Computer Information Science and Artificial Intelligence. First Author, 08.2022
- **软著:** "Intelligent Financial Reimbursement System", No. 2022SRO576160. First Author, 03.2022
- **软著:** "Beichite Cloud Customized Printing System", No. 2022SRO758646. First Author, 03.2022
- **专利:** "A Wireless Torque Sensor", No.202210304204.9. Second Inventor, 06.2022
- **专利:** "A Walking Aid Stick", No. 202010166261.6. Second Inventor, 05.2020