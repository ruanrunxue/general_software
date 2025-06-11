---
title: 软件工程视角下的 GenAI
createTime: 2025/06/07 17:31:42
permalink: /genai/preface/
---

自革命性的 ChatGPT 诞生以来，大模型（Large Language Model，LLM）以前所未有的速度飞速发展。各大厂纷纷下场布局，小厂也频有惊艳出品。最典型的是幻方量化出品的 DeepSeek，2024 年 12 月 26 日 DeepSeek-V3 开源发布^[2]^，正式拉开了顶级模型开源的序幕，也向世界证明了中国的创新之力。

各厂商如同军备竞赛一般，每隔数月，就会有新的模型发布，每次发布必定瞄准榜单第一。在各厂商的“互卷”之下，模型的性能愈发强悍，而使用成本却越来越低，最大受益者成为广泛的 GenAI 用户。

近年来，GenAI 应用海量涌现^[3]^，以 ChatGPT 为代表的通用 AI 助手能够轻松应对各类内容生成任务；以 Perplexity 为代表的 AI 搜索引擎能够让搜索效率提升数倍；以 Cursor 为代表的 AI 辅助编程应用能够让不懂写代码的人也能开发应用，等等。

这些革命性的 GenAI 应用的背后不只有 LLM，而是底层硬件到上层应用的一整个 GenAI 技术栈。

![图 1. GenAI Stack](/imgs/genai/preface/1.genai_stack.png)

## 硬件层

硬件是一切的基础，图 1 中列举了与计算强相关的几类主流芯片。

CPU（Central Processing Unit）是面向通用场景的计算芯片，是冯·诺依曼计算机架构下的核心单元，能够处理复杂负载，可以轻松应对“*小量复杂任务*”。然而，LLM 的训练和推理主要涉及大量的矩阵运算，属于“*大量简单任务*”的负载特征，这使得 CPU 在模型训推上显得很吃力。所以，CPU 通常会协同其他 AI 加速芯片一起为训推业务提供算力，CPU 在其中主要用作任务调度或执行一些计算强度较低的算子。随着 CPU 算力的提升，以及 Intel AMX^[4]^ 此类面向矩阵运算的高级指令集出现，CPU 也慢慢被用于中小参数模型的推理^[5]^。相比其他 AI 加速芯片，CPU 在 LLM 推理场景具备大内存容量、低起建成本优势，对中小型企业非常友好。

GPU（Graphics Processing Unit）是如今最火的 AI 加速芯片，最初由英伟达（NVIDIA）推出。NVIDIA GPU 凭借其强悍的计算性能和完善的 CUDA 生态，在 AI 加速芯片市场上占据高达 89% 的份额^[6]^。从 GPU 的全称图形处理单元可看出，它最初被专门用于计算机图像渲染，后因其强大的并行计算能力天然适配 AI 负载的“*大量简单任务*”的负载特征，被广泛应用于深度学习训练，在大模型时代更是供不应求。与 CPU 相比，GPU 等 AI 加速芯片具备更强的算力和更大的显存（类比 CPU 中的内存）带宽，但显存容量却少了很多，导致大参数规模的模型必须由多卡协同部署，这又带来了新的通信消耗。如何把每张卡充分利用起来成为提升系统性能的关键。





> #### 参考
>
> [1] [Trends – Artificial Intelligence](https://www.bondcap.com/report/tai/), Mary Meeker
>
> [2] [DeepSeek-V3 正式发布](https://api-docs.deepseek.com/zh-cn/news/news1226), DeepSeek
>
> [3] [The Top 100 Gen AI Consumer Apps - 4th Edition](https://a16z.com/100-gen-ai-apps-4/), a16z
>
> [4] [Intel® Advanced Matrix Extensions (Intel® AMX)](https://www.intel.com/content/www/us/en/products/docs/accelerator-engines/advanced-matrix-extensions/overview.html), Intel
>
> [5] [阿里云弹性计算新升级：CPU上跑推理，模型起建成本降低50%](https://www.36kr.com/p/2605400806455937?spm=a2c6h.11547689.0.0.b4983fd2uCdfXR), 36Kr
>
> [6] [2025年AI芯片行业市场规模及主要企业市占率分析报告](https://www.chyxx.com/cyzx/1213004.html), 智研咨询
