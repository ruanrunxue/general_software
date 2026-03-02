---
title: 软件工程视角下的 GenAI
createTime: 2025/06/07 17:31:42
permalink: /genai/preface/
---

自革命性的 ChatGPT 诞生以来，大模型（Large Language Model，LLM）以前所未有的速度飞速发展。各大厂纷纷下场布局，小厂也频有惊艳出品。最典型的是幻方量化出品的 DeepSeek，2024 年 12 月 26 日 DeepSeek-V3 开源发布^[2]^，正式拉开了顶级模型开源的序幕，也向世界证明了中国的创新之力。

各厂商如同军备竞赛一般，每隔数月，就会有新的模型发布，每次发布必定瞄准榜单第一。在各厂商的“互卷”之下，模型的性能愈发强悍，而使用成本却越来越低，最大受益者成为广泛的 GenAI 用户。

近年来，GenAI 应用海量涌现^[3]^，以 ChatGPT 为代表的通用 AI 助手能够轻松应对各类内容生成任务；以 Perplexity 为代表的 AI 搜索引擎能够让搜索效率提升数倍；以 Cursor 为代表的 AI 辅助编程应用能够让不懂写代码的人也能开发应用，等等。

这些革命性的 GenAI 应用的背后不只有 LLM，而是底层硬件到上层应用的一整个 GenAI 技术栈。如今，GenAI 与云计算结合已经是一种必然的趋势，本文档所介绍的 GenAI 技术栈将会以云原生技术栈为基础，整体如下图 1. GenAI Stack 所示：	

![图 1. GenAI Stack](/imgs/genai/preface/1.genai_stack.png "图 1. GenAI Stack")

# 硬件层

硬件是一切的基础，图 1 中列举了与 AI 计算强相关的几类主流芯片。

CPU（Central Processing Unit）是面向通用场景的计算芯片，是冯·诺依曼计算机架构下的核心单元，能够处理复杂负载，可以轻松应对“*小量复杂任务*”。然而，LLM 的训练和推理主要涉及大量的矩阵运算，属于“*大量简单任务*”的负载特征，这使得 CPU 在模型训推上显得很吃力。所以，CPU 通常会协同其他 AI 加速芯片一起为训推业务提供算力，CPU 在其中主要用作任务调度或执行一些计算强度较低的算子。随着 CPU 算力的提升，以及 Intel AMX^[4]^ 此类面向矩阵运算的高级指令集出现，CPU 也慢慢被用于中小参数模型的推理^[5]^。相比其他 AI 加速芯片，CPU 在 LLM 推理场景具备大内存容量、低起建成本优势，对中小型企业非常友好。

GPU（Graphics Processing Unit）是如今最火的 AI 加速芯片，最初由英伟达（NVIDIA）推出。NVIDIA GPU 凭借其强悍的计算性能和完善的 CUDA 生态，在 AI 加速芯片市场上占据高达 89% 的份额^[6]^。从 GPU 的全称图形处理单元可看出，它最初被专门用于计算机图像渲染，后因其强大的并行计算能力天然适配 AI 负载的“*大量简单任务*”的负载特征，被广泛应用于深度学习训练，在大模型时代更是供不应求。与 CPU 相比，GPU 等 AI 加速芯片具备更强的算力和更大的显存（类比 CPU 中的内存）带宽，但显存容量却少了很多，导致大参数规模的模型必须由多卡协同部署，这又带来了新的通信消耗。如何把每张卡充分利用起来成为提升系统性能的关键。

NPU（Neural Processing Unit）是为专门为神经网络计算负载设计的一类场景定制化芯片，对矩阵乘法、卷积等神经网络常见的操作做了深度优化，在 LLM 的训推业务上有显著的加速效果。NPU 通常可以分成两类，一类是专门为云端负载设计，追求更高的性能，比如 Google 的 TPU^[7]^、华为的昇腾 NPU^[8]^；一类是专门为终端负载设计，追求更低的功耗，比如高通 NPU^[9]^、集成在苹果 M 系列芯片里的 Neural Engine^[10]^。NPU 在并行计算灵活性上不如 GPU，但能效比更高，在大规模包括 LLM 在内的机器学习任务上具有一定的成本优势。

# IaaS 层

IaaS（Infrastructure as a Service）是云计算的基础设施层，提供计算、存储、网络资源的虚拟化能力。GenAI 的快速发展，涌现了大量的模型训练、实时推理、多模态数据处理等业务，为 IaaS 层带来了显著的变革。

最明显的是，过去以 CPU 为中心的计算架构，演变为 CPU 与 GPU/NPU 协同、以 GPU/NPU 为中心的架构。这意味着数据不再需要经过 CPU  即可流转至 GPU/NPU，从而提升了 GPU/NPU 的计算效率。另外，云厂商也纷纷推出 GPU/NPU 云服务器^[11][12]^、大模型推理一体机^[13][14]^，为 AI 业务负载提供更强的算力底座。

传统的 TCP/IP 网络受数据拷贝开销大、协议栈处理繁琐等限制，已无法满足 AI 训推业务的高带宽、低时延的通信诉求。以 RDMA 为代表的新一代网络技术正成为 AI 时代网络的标配，它们通过零拷贝、内核旁路等技术，显著提升通信性能。但优化远不止如此，以 NVIDA 为例，NVIDIA 推出了 GPUDirect RDMA^[15]^ 允许 GPU 跨节点直连的。针对 RMDA 底层硬件协议 PCIe 带宽不足的问题，推出带宽是 PCIe 14倍的 NVLink^[16]^ 协议。各类厂商对网络的优化，为的就是不断减少 AI 负载的通信消耗，进一步榨干芯片的性能。

GenAI 也促使了存储的变革。SSD 替代传统 HDD 正成为主流，提供更高的读写带宽。海量的数据使大模型变得智能，如何高效、低成本地管理它们变得愈发关键，冷温热数据分层存储、数据智能下沉/预取逐渐成为存储解决方案的必备能力^[17][18]^。随着多模态大模型的迅速发展，大量非结构化多模态数据（PDF、图片、音频、视频等）的价值被重新挖掘，对象存储凭借其高扩展性、低成本、高效的非结构化数据管理能力，成为 AI 时代必不可少的数据底座。一些专门针对 AI 负载优化的存储技术也更多地被推出，比如 DeepSeek 的 3FS 文件系统^[19]^针对模型训练大量随机读的特点做了无缓存的架构设计；NVIDIA 的 GPUDirect Storage^[20]^通过 DMA 技术允许 GPU 直接从远端存储加载到显存，避免了 CPU 和 GPU 之间的数据拷贝。

# PaaS 层

PaaS（Platform as a Service）是云计算的平台层，提供灵活可伸缩的云平台来开发、部署、运行和管理应用。GenAI 的出现，一方面促使 PaaS 层往更加适配 AI 负载的方向演进，另一方面 PaaS 因与 AI 深度结合而变得更加智能和好用。

AI 时代最受益的人群可能是是开发者，AI 辅助编程（又称 Vibe Coding）的出现，使得开发变得更加容易，各类低码开发平台也纷纷向 AI 开发平台演进。不同于以往低码开发平台的拖拉拽开发界面，AI 开发平台的开发界面通常只有一个文本输入框。用户只需用自然语言跟 AI 对话，描述清楚需求，即可实现应用从开发到最终上线的全流程自动化托管，几乎不需要具备编程背景^[21][22]^。这将会促使开发者和应用数量的快速增长，对 PaaS 层带来新的挑战和机遇。

在应用部署和运行上，AI 辅助编程让应用数量的剧增，从而进一步带动 Serverless 的普及。AI 应用的一大特征是自然语言替代传统图形化界面作为用户交互方式，而自然语言的无边界、不确定特征，导致 AI 应用的流程和负载难以被预测。在这种业务特征下，固定的资源配置变得性价比极低，很有可能出现忙时资源不足、闲时资源浪费的问题，导致应用上线成本高昂。Serverless 的核心特点是无需开发者管理基础设施，提供按需计费、自动扩缩容等能力，这无疑能大幅降低了开发者上线新应用的成本。另外，随着 AI 模型训推业务迅速增长，GPU/NPU 计算服务的 Serverless 化成为一种趋势^[22]^，Serverless 平台也朝着 Scale to Zero 的极致低成本方向演进。

在原生时代，Kubernetes + 容器的应用管理平台底座已经成为业界标配，但过去更多的是以 CPU 为中心的调度机制。随着 AI 时代的来临，GPU 和 NPU 等异构算力占比越来越大、RDMA 等新型通信技术有替代 TCP 的趋势，支持多种通信网络拓扑感知、异构算力协同的调度机制变得必不可少。目前看，Kubernetes 在 AI 时代的地位仍无法被撼动，其与各类推理框架相结合逐渐成为主流，比如 vLLM Production Stack^[24]^ 就提供了与 Kubernetes 亲和的分布式部署方案。

数据一直以来都是企业最核心的资产之一，AI 时代更是如此，它决定了模型的质量。然而，企业中 80% 以上的数据是没有被充分利用的“暗数据”^[25]^，它们通常是非结构化和多模态的。随着多模态模型的普及，如何有效管理和利用这些“暗数据”变得愈发重要。传统的数据湖大多针对结构化和半结构化的数据而设计，比如 Apache Iceberg^[26]^、Apache Hudi^[27]^ 等，多模态的趋势促使了 Lance^[28]^、DeepLake^[29]^ 等多模态数据湖的崛起，对数据处理引擎也提出的新的要求。首先，数据处理引擎需要具备对海量多模态数据进行高效处理的基础能力。其次，AI 应用的典型特征是 CPU 和 AI 加速芯片协同计算、实时感知和响应，这要求数据处理引擎具备异构调度、动态负载调度的能力，Spark 等传统大数据引擎已难以满足，引出了 Ray 等专门为 AI 负载设计数据处理引擎^[30]^。

数据库也迎来的新的一波革新。大模型多轮对话的长记忆存储、检索增强生成等，都要求数据库具备向量存储和检索的能力。AI 应用实时感知和响应的动态负载特征，进一步加速了数据库往云原生 Serverless 方向的演进，快速冷启动和自动扩缩容的能力成为降低成本的关键。对普通用户来说，最直观的感受可能是数据交互变得更加容易，通过简单的自然语言即可完成数据查询和报表生成，数据洞察效率倍增。

# MaaS 层

MaaS（Model as a Service）指的是由云服务厂商提供预训练好的 AI 模型给用户直接使用，让企业或个人用户无需从零开始训练和部署模型，极大降低了模型使用门槛。

模型是整个 GenAI 技术栈的大脑中枢。我们说所的大模型，通常指是大语言模型（Large Language Models，LLM），它经过大量数据和算力的训练，相比传统的 ML 模型具有更好的泛化性。目前几乎所有的大模型都是以 Transformer 架构为基础，它使用一种被称为多头注意力（Multi-Head Attention，MHA）的机制来考虑整个输入上下文，能够有效捕捉词句间的长距离依赖关系，从而具备了极佳的语言理解能力。多模态大语言模型（Multimodal Large Language Model，MLLM）是 LLM 的下一代演进，它在 LLM 的基础上扩充了对图片、视频、音频等多模态数据的理解，突破了单一文本的限制，能够执行“文生视频”、“图讲故事”等多模态任务。

MaaS 对外是简单易用的 API 接口，背后则是一整套复杂的模型生命周期管理流程。

高质量的模型离不开高质量的数据，这些训练数据获取、清洗、标注、转换、管理的全过程被称为数据工程（Data Engineering）。

段落1：数据工程

段落2：模型训练

段落3：模型推理

段落4：提示工程、上下文学习

段落5：模型微调

段落6：RAG



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
>
> [7] [TPU 架构](https://cloud.google.com/tpu/docs/system-architecture-tpu-vm?hl=zh-cn), Google Cloud
>
> [8] [昇腾计算](https://e.huawei.com/cn/products/computing/ascend), 华为 Ascend
>
> [9] [NPU是什么？为何它是开启终端侧生成式AI的关键？](https://www.qualcomm.cn/news/blogs/2024/03/blog-2024-3-6), 高通
>
> [10] [Apple 发布 M4 Pro 和 M4 Max 芯片](https://www.apple.com.cn/newsroom/2024/10/apple-introduces-m4-pro-m4-max/), Apple
>
> [11] [火山引擎 GPU 云服务器](https://www.volcengine.com/docs/6419), 火山引擎
>
> [12] [GPU云服务器](https://www.aliyun.com/product/ecs/gpu), 阿里云
>
> [13] [火山引擎AI一体机](https://www.volcengine.com/product/ai-all-in-one), 火山引擎
>
> [14] [阿里云百炼专属版 AI Stack 一体机](https://www.aliyun.com/solution/tech-solution/bailian-aistack), 阿里云
>
> [15] [NVIDIA GPUDirect](https://developer.nvidia.com/gpudirect), NVIDIA
>
> [16] [NVIDIA NVLink 和 NVLink 交换机](https://www.nvidia.cn/data-center/nvlink/), NVIDIA
>
> [17] [火山引擎对象存储](https://www.volcengine.com/product/TOS), 火山引擎
>
> [18] [对象存储 OSS](https://www.aliyun.com/product/oss), 阿里云
>
> [19] [3FS Github](https://github.com/deepseek-ai/3FS), DeepSeek
>
> [20] [GPUDirect Storage](https://docs.nvidia.com/gpudirect-storage/index.html), NVIDIA
>
> [21] [Replit](https://replit.com), Replit
>
> [22] [Bolt.new](https://bolt.new), Bolt
>
> [23] [Cloud Run GPUs, now GA, makes running AI workloads easier for everyone](https://cloud.google.com/blog/products/serverless/cloud-run-gpus-are-now-generally-available), Google Cloud
>
> [24] [vLLM Production Stack](https://github.com/vllm-project/production-stack), vLLM
>
> [25] [What is Dark Data and Why Does It Matter to Public Agencies?](https://urbanlogiq.com/what-is-dark-data-and-why-does-it-matter-to-public-agencies/), URBAN LOGIQ
>
> [26] [Apache Iceberg](https://iceberg.apache.org/), Iceberg
>
> [27] [Apaxhe Hudi](https://hudi.apache.org/), Hudi
>
> [28] [LanceDB](https://www.lancedb.com/), LanceDB
>
> [29] [DeepLake](https://www.deeplake.ai/), activeloop 
>
> [30] [Comparing Ray and Apache Spark](https://www.anyscale.com/compare/ray-vs-spark), anyscale
>
> [31] [What is model as a service (MaaS)?](https://azure.microsoft.com/en-us/resources/cloud-computing-dictionary/what-is-models-as-a-service-maas), Azure

