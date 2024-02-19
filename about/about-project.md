---
title: 'Projects Experience'
date: 2022-11-26 11:25:18
tags: []
published: true
hideInList: false
feature: 
isTop: true
---
📖 Interests:Backend,Frontend,distributed system,BlockChain
* java develop:Intelligent Financial Reimbursement System
* golang develop: Online Video Play System
* frontend develop:Desktop Grid Five Axis Linkage Denture
* Flutter APP:Development of Power Detection APP Based on Modbus TCP
*  [Click to see more...](https://yushen611.github.io/post/about-project/)   
<!-- more -->

<meta name="referrer" content="no-referrer"/>

[TOC]

# PART 1: Internship Experience

# <center>[InternEx-2022]:Intelligent Financial Reimbursement System

> **Outcome : Software Copyright (First author)** 
>
> **Company** : GUOCHUANG CLOUD Technology Co.,Ltd
>
> From **2021.12** to **2022.02**

The "Intelligent Financial Reimbursement System" provides enterprises with online invoice upload, identification and approval services, which can simplify the enterprise reimbursement process and reduce financial labor costs.

Note: The system supports the identification of multiple invoices(Seven different types)

* **Automatic Invoice Information Entry**:Employing Tencent Cloud AI APIs for invoice OCR recognition, our System can detect all info from invoice picture within 1s.

* **Automatic Travel Allowance Calculation**:Utilized Genetic Algorithms for Global Path Planning to verify the Correctness of travel routes in reimbursement invoices. Automatically inferred missing travel points to prevent reimbursement issues due to lost invoices.

* **Microservices Architecture**: Implemented a Microservices Architecture using Spring Cloud to decouple various modules, including User Management, Invoice Processing, and Expense Report Management, thereby enhancing process modularity.

* ***Project Sustainability Management***: As a team leader, managed a team of 9 individuals,utilized Weekly Report and Gantt charts to document requirements and schedule tasks for unified management and tracking.Finally, a total of over 20 technical documents were created and our team completing feature development 7 days ahead of schedule.

<details>
<summary>Click to view details</summary>

## Identification of value-added tax invoices



<div align="center">
<img src=https://gitee.com/yushen611/img/raw/master/增值税发票表单.png width=60%  />
<p>Fig1. value-added tax invoice</p>
</div>


## Ordinary form recognition

<div align="center">
<img src=https://gitee.com/yushen611/img/raw/master/普通类表单.png width=60%  />
<p>Fig2. Ordinary form</p>
</div>


## Train ticket identification

<div align="center">
<img src=https://gitee.com/yushen611/img/raw/master/火车票类表单.png width=60%  />
<p>Fig3. Train ticket</p>
</div>


</details>

# PART 2: Project Experience

# [ProjectEx-2023]Online Video Play System

* **Stability of Distributed System Microservices Architecture**:Successfully keeping RPC Error Rate below 3%, through Consul and GRPC for service registration and discovery, combined with the weighted round-robin algorithm to optimize load balancing for the online video system.

* ***System Metrics Monitoring and Alerting***:Monitored server status using Prometheus,Grafana and Altermanager.After Post-Load Testing through Jmeter, performance bottleneck analysis is employed with ELK log recording and PPROF flame graphs for performance bottleneck analysis.

* **High Concurrency and High Performance**:Achieved an interface QPS of over 1500 by using Redis, establishing DB Index and Read-write separation,Server Scale Out with Kubernetes,and a pagination query to optimize the slow-query video list interface.

* **Continuous Integration/Continuous Deployment**:Compressing the entire process from Code Changes to Deployment into a 2-minute timeline by implementing CI/CD using Docker and Github Action.

* **Project Link**: http://175.178.59.92:8081/

# <center>[ProjectEx-2022]Desktop Grid Five Axis Linkage Denture Manufacturing System

> **Outcome : Software Copyright (First author)**

The customized printing system of Beidite cloud will be used for daily data analysis, model management and equipment management of denture equipment users such as hospitals and medical institutions.The system supports model upload, model viewing, model printing, and equipment activation to improve work efficiency, thereby comprehensively enhancing the profitability and competitiveness of enterprises.



<details>
<summary>Click to view details</summary>
## One-click printing all models
  One-click printing can print all selected models.According to the model structure, the G code corresponding to the five-axis denture processing machine can be automatically generated.

  The G code will be transmitted to the next machine.If there are multiple models that need to be printed, the priority of printing is determined by intelligent sorting based on the total number of machines and resources.

<div align="center">
<img src=https://gitee.com/yushen611/img/raw/master/一键打印.png width=80%  />
<p>Fig1. One-click printing all models</p>
</div>


## Online management of 3D models

The details include two parts, one is the basic information of the model, and the other is the 3D model of the model.

The basic information of the model includes the number, patient, creation date, material, size, quality level, etc.
<div align="center">
<img src=https://gitee.com/yushen611/img/raw/master/模型信息.png width=80%  />
<p>Fig2. basic information of the model</p>
</div>

The 3D model module of the model can view the uploaded model online, and you can zoom and scroll to view the relevant details.
<div align="center">
<img src=https://gitee.com/yushen611/img/raw/master/模型查看.png width=80%  />
<p>Fig3. 3D model module online show</p>
</div>

</details>

# <center>[ProjectEx-2022]Development of Power Detection APP Based on Modbus TCP

> **Outcome**: **BEST open source implement of Modbus tcp Protocol** 

The goal of this system: in order to be able to interact with power equipment in a WIFI local area network (no 4G network) environment, and efficiently and conveniently realize the operation and maintenance management of power equipment, including: 1) DCU, power equipment data management; 2) power equipment remote control, remote adjustment, telemetry, remote communication; 3) and power equipment historical data query and other functions.

In order to complete the communication, I independently developed the dart version interface of the Modbus Tcp protocol and opened it [**open source**](https://github.com/yushen611/modbusTcpApi "Modbus Tcp Api In github").

**It is the best Modbus Tcp Api for dart.**

# <center>[ProjectEx-2021]A Wireless Torque Sensor of High-Speed Rotating Shafting Torque for In Situ Measurement

> **Outcome**: **patent** and **paper**

  At present, torque sensors are widely used in industry to measure static and dynamic torque.Since the torque sensor is mostly installed between the input shaft and the output shaft at this stage, this measurement method leads to a variety of factors affecting the test results, such as the axial degree error causing additional deformation of the measurement part, so a method for in situ direct measurement of the measuring shaft is proposed.In this paper, a wireless torque measurement method for in situ measurement of torque of rotating shaft system is studied.A mechanical model for in situ measurement of torque is established, and the correctness of the theoretical model is verified through finite element simulation analysis.The steps of using this method for torque measurement are introduced, and static and dynamic repetitive experiments are carried out.The uncertainty of the measurement of torque on the surface of the experimental structure in this method can be maintained below 3%.It provides a reference for the study of torque in situ measurement.

<details>
<summary>Click to view details</summary>


## Simulation and analysis
  In this section, finite element simulation is carried out to analyze the axial strain distribution law of the test piece under torsion.According to the performance of the MTS torsional universal material testing machine and considering the rigidity and strength of the test piece, the material of the test piece is selected as 45#steel with good overall performance.ANSYS Workbench 2020 R2 is used to apply external torque to the test piece to realize statics simulation. In order to obtain high simulation accuracy, the meshing is selected as Hex dominant method, and the free surface mesh type is quadrilateral/triangle.A fixed restraint is applied to one end of the cylinder of the test piece, and a torque load is applied to the other end.Through ANSYS simulation, the surface strain and stress distribution of the test piece are shown in Figure 1.
<div align="center">
<img src=https://gitee.com/yushen611/img/raw/master/等效力.png width=80%  />
<p>Fig1. Equivalent elastic strain / equivalent force</p>
</div>

<div align="center">
<img src=https://gitee.com/yushen611/img/raw/master/角度与扭矩.png width=80%  />
<p>Fig2.  Position, angle, torque relationship</p>
</div>



## Experiment

<div align="center">
<img src=https://gitee.com/yushen611/img/raw/master/机械结构设计.png width=80%  />
<p>Fig3. Mechanical structure design</p>
</div>



In order to verify the performance of the wireless in situ measurement system and test system designed in this paper, static cyclic loading experiments were carried out on the built test prototype.

<div align="center">
<img src=https://gitee.com/yushen611/img/raw/master/实验设备.png width=80%  />
<p>Fig4. Torsion MTS universal material testing machine loading experiment</p>
</div>

In situ dynamic detection of the output shaft is carried out on the friction tester. The maximum speed of the tester is 300RPM. Four progressive loads are carried out. As the speed increases, the loading force decreases. Since the measuring device is fixed on the output shaft, excessive load at high speed will cause greater vibration.



<div align="center">
<img src=https://gitee.com/yushen611/img/raw/master/机械结构实现.png width=80%  />
<p>Fig5. Realization of mechanical structure</p>
</div>

</details>

# <center>[ProjectEx-2021]Design and Manufacturing of An Intelligent Ankle and Foot Orthosis

> **Outcome**: **National Third Prize in ICAN** Entrepreneurship Competition

This project aims to develop a customized intelligent ankle and foot orthosis to solve the problems of many complications caused by wearing common orthotics, long production cycles and single functions of orthotics.Through the research on the additive manufacturing technology of ankle and foot orthotics considering mechanical properties and the 3D printing technology of porous foam materials with gradient elastic modulus, the rapid design and manufacture of orthotics is realized, and the comfort and accuracy of orthotics are improved; through the development of an orthosis information feedback system based on flexible sensors, the patient's recovery is analyzed, and the functions are diversified and the products are intelligent.

<details>
<summary>Click to view details </summary>
<div align="center">
<img src=https://gitee.com/yushen611/img/raw/master/扫描后的模型.png width=60%  />
<p>Step1. model scan</p>
</div>

<div align="center">
<img src=https://gitee.com/yushen611/img/raw/master/机械结构设计图.png width=60%  />
<p>Step2. model build</p>
</div>

<div align="center">
<img src=https://gitee.com/yushen611/img/raw/master/湿固化硅胶3D打印机.png width=60%  />
<p>Fig1. Wet curing silicone 3D printer</p>
</div>

<div align="center">
<img src=https://gitee.com/yushen611/img/raw/master/个性化定制鞋垫.png width=60%  />
<p>Fig2. Personalized custom insoles through 3D printer</p>
</div>


</details>

# [ProjectEx-2020]An Intelligent Walking Aid with Navigation and Anti-Tripping Functions

> **Outcome**:

The product is an intelligent walker with multi-functions such as navigation and anti-fall. It mainly solves the safety problems of people getting lost and falling when they go out, as well as the difficulty of young people obtaining effective information about complex traffic. At the same time, taking into account the difficulty of displaying health codes in public places during the epidemic, the function of generating health codes has been added.
<details>
<summary>Click to view details </summary>
<div align="center">
<img src=https://gitee.com/yushen611/img/raw/master/外观结构.png width=60%  />
<p>Fig1. Appearance structure</p>
</div>

<div align="center">
<img src=https://gitee.com/yushen611/img/raw/master/防摔功能.png width=60%  />
<p>Fig2. Anti-fall function</p>
</div>


</details>