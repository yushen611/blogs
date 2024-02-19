---
title: 'LLAMA2实践'
date: 2023-10-01 14:16:37
tags: []
published: true
hideInList: false
feature: 
isTop: false
---
# 参考

官方文档

* [Llama 2 is here - get it on Hugging Face](https://huggingface.co/blog/llama2)
* [meta-llama/Llama-2-7b · Hugging Face](https://huggingface.co/meta-llama/Llama-2-7b)

llama2教程文档

* [开源大模型 LLAMA2 colab 快速下载上手使用 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/652588148)
* [在16G的推理卡上微调Llama-2-7b-chat - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/645152512)
* [微调llama2模型教程：创建自己的Python代码生成器 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/652242117)

* [Llama2+QLora微调大模型-超详细教程（适合小白）_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1594y1y76m/?spm_id_from=333.337.search-card.all.click&vd_source=9710fe8f4dbfeb6bd0b7202815b341c2) （==有用教程==）
  * [Instruction fine-tuning Llama 2 with PEFT's QLoRa method (ukey.co)](https://ukey.co/blog/finetune-llama-2-peft-qlora-huggingface/)（==有用==）

LLMTUNE

* [康奈尔大学发布可以在一张消费级显卡上微调650亿参数规模大模型的框架：LLMTune - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/629376619#Llmtune简介)
* [kuleshov-group/llmtools: 4-Bit Finetuning of Large Language Models on One Consumer GPU (github.com)](https://github.com/kuleshov-group/llmtools)



huggling face

* [抱脸-Hugging face AI模型库使用教程(快速入门) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/651132477)
* HF dataset 官方自带与自定义:[5分钟NLP：HuggingFace 内置数据集的使用教程 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/483618490)
* [Hugging Face快速入门（重点讲解模型(Transformers)和数据集部分(Datasets)）_huggingface_iioSnail的博客-CSDN博客](https://blog.csdn.net/zhaohongfei_358/article/details/126224199)



HF模型微调

1. [Fine-tune a pretrained model - Hugging Face](https://huggingface.co/docs/transformers/training)
   - 这是一个关于如何使用深度学习框架对预训练模型进行微调的教程。
2. [Fine-tuning a pretrained model - Hugging Face](https://huggingface.co/docs/transformers/v4.15.0/training)
   - 在这个教程中，将展示如何使用Transformers库对预训练模型进行微调。该教程提供了TensorFlow和PyTorch两种框架的方法。
3. [Fine-tuning a model with the Trainer API - Hugging Face NLP Course](https://huggingface.co/learn/nlp-course/chapter3/3?fw=pt)
   - Transformers提供了一个`Trainer`类，帮助您在自己的数据集上对其提供的任何预训练模型进行微调。
4. [How to fine-tune a model for common downstream tasks - Hugging Face](https://huggingface.co/docs/transformers/v4.15.0/custom_datasets)
   - 这个指南将展示如何为常见的下游任务微调Transformers模型。您将使用Datasets库快速加载和处理数据。
5. [Fine-tuning a masked language model - Hugging Face NLP Course](https://huggingface.co/learn/nlp-course/chapter7/3?fw=tf)
   - 对于涉及Transformer模型的许多NLP应用，您可以直接从Hugging Face Hub获取一个预训练模型，并直接在您的数据上对其进行微调。
6. [Fine-tuning pretrained NLP models with Huggingface's Trainer - Towards Data Science](https://towardsdatascience.com/fine-tuning-pretrained-nlp-models-with-huggingfaces-trainer-6326a4456e7b)
   - 这是一个简单的教程，展示了如何使用Hugging Face的`Trainer`对预训练的NLP模型进行微调，无需使用原生的Pytorch或Tensorflow。

