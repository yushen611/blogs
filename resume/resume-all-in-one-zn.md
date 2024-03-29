# 余文涯-简历 -ALL In One

::: left

icon:blog 新加坡

[icon:github   https://github.com/yushen611](  https://github.com/yushen611[)


:::

::: right

icon:email   ywy_hfut@163.com

icon:phone +86 15347118275

:::



## 教育背景

:::left
**南洋理工大学 - 计算机理学硕士 (区块链技术)**
:::
:::right
**2023.08-2024.07**
:::

- 在NTU CSLab研究大型语言模型(LLMS)用于监控和预警区块链交易风险。

:::left
**合肥工业大学 -软件工程（本科）**
:::
:::right
**2019.09-2023.06**
:::

- 成绩与排名：91.02/100 (排名 5/169)
- 获奖与荣誉：国家奖学金，校一等奖学金，优秀三好学生，计算机设计大赛国赛三等奖




## 实习经历
:::left
**Tiktok(Singapore) - Golang Backend**
:::
:::right
**2023.12-2024.06**
:::

`FaaS` `AWS Lambda` `workflow` 

* 实现了超过10种原子服务，通过使用类似AWS Lambda的功能即服务（FaaS）工具。
* 构建了自愈工作流程，用于ingress和节点资源的恢复，将推流和拉流节点的在线率提高到90%以上。


:::left
**科大国创云网科技有限公司 -Java后端**
:::
:::right
**2021.12-2022.02**
:::

`Spring Boot` `OCR `  `Redis ` 


- 成果：已获得《智能化企业财务报销系统》软件著作权（第一作者）。
- 增值税发票OCR识别：使用YOLOv3 + CRNN + CTC实现发票OCR识别与自动录入。
- 微服务架构：使用Spring Cloud实现微服务架构，通过gRPC调用基于Flask框架搭建的发票中心接口。
- 单点登录功能：利用Spring Security实现单点登录功能（OAuth2协议），进行密码加密管理与防止跨站点请求伪造（CSRF）。
- 身份认证与授权：使用JWT进行身份认证与授权，实现了服务端的无状态认证。
- 防止缓存雪崩：利用Redis实现分布式互斥锁，有效防止缓存雪崩。




## 项目经历:开发
:::left
**在线视频播放系统：微服务+分布式架构 - Golang后端**
:::
:::right
**2023.03-2023.06**
:::

`Gin` `GPRC `  `Consul ` `Redis ` `dcoker ` `CI/CD ` `Kubernetes` `Prometheus`

- **项目简介**：开发了一个基于gin与gprc的Golang后端在线视频播放系统，通过微服务和分布式架构，实现了高QPS、高稳定性、日志与指标监控、以及CI/CD，显著提升了系统性能和开发效率。
- **分布式系统指标监控**：使用Prometheus+Grafana+Alertmanager实现了服务器与接口的指标监控与预警。
- **实时评分榜功能并发优化**：通过分析出实时评分排名是读多写少的场景，对mysql主从复制以进行读写分离，再利用Kubernetes编排多个微服务POD到两个2核4G服务器，成功将接口QPS提升至超过1500。
- **持续集成与部署**：通过使用Docker和Github Action实现CI/CD，将从代码更改到部署的整个过程压缩至2分钟内。

## 项目经历:AI

:::left
**基于大语言模型的区块链交易风险检测 - 南洋理工大学CSLab**
:::
:::right
**2023.08-2023.12**
:::

`LLM` `LLaMA2` `Fine-tuning`

- **项目简介**：通过微调LLaMA2，实现了EVM交易风险检测大模型
- **微调大语言模型**：通过微调大型语言模型，我们成功优化了其对交易文本的语义映射能力，以进行交易风险的分类，准确率达到80%
- **prompt优化**：通过GOT等prompt优化技术，进一步提高了模型风险识别的准确率

:::left
**SQL注入智能检测器 -合肥工业大学**
:::
:::right
**2023.03-2023.06**
:::
`SQL Injection` `machine learing` `nlp`

- **项目简介**：实现了一种基于机器学习的SQL注入智能检测器，检测准确率97%。
- **词嵌入优化**：采用了word2vec算法并融入位置编码，结合一维卷积池化层，从而实现了对SQL查询字符串的高效向量化与降噪
- **模型选择**：通过训练不同机器学习模型，比较不同指标后，选择在数据集上准确率最高的随机森林模型
- **数据集**：由我们自主收集，是目前全网规模最大的SQL注入（SQLI）数据集。



:::left
**基于TransCGAN的人体三维动作指令生成器 - 合肥工业大学虚拟现实实验室**
:::
:::right
**2022.03-2022.12**
:::
`TransCGAN` `Text-to-Video` 
- **项目简介**：增强对数据时空序列信息的提取: 发表的TransCGAN模型，成功地融合了CGAN与Transformer，能够根据文字指令即时生成定制的人物动作视频,专为高质量人体动作生成设计。
- **论文**:"TransCGan-based human motion generator"(第一作者)



## 项目经历-BlockChain

:::left
**EVM去中心化铭文索引器 - NTU**
:::
:::right
**2024.01-2024.02**
:::

* **项目概述**：通过实现ERC-7583协议，实现了EVM铭文系统，以及开源索引器。

* **降低Gas fee**：通过在交易event中存储NFT/FT以降低gas fee，使每次铭刻的费用趋近于最低的交易费用
* **高检索速度**：通过采用缓存技术和增量读取优化的索引器，将数据检索响应时间从原来的13秒提升至1秒。



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

## 论文 && 专利 && 软著

- **论文:** "TransCGAN-based Human Motion Generator", International Conference on Computer Information Science and Artificial Intelligence. First Author, 08.2022
- **软著:** "Intelligent Financial Reimbursement System", No. 2022SRO576160. First Author, 03.2022
- **软著:** "Beichite Cloud Customized Printing System", No. 2022SRO758646. First Author, 03.2022
- **专利:** "A Wireless Torque Sensor", No.202210304204.9. Second Inventor, 06.2022
- **专利:** "A Walking Aid Stick", No. 202010166261.6. Second Inventor, 05.2020