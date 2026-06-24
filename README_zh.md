# 🚀 Awesome GLM-5.2 API Providers & Self-Hosting Guide

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![Model](https://img.shields.io/badge/Model-GLM--5.2%20%2F%20Z.ai-blue)](https://bigmodel.cn/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)
[![Updated](https://img.shields.io/badge/Last%20Updated-2026--06%20-orange)](#)

[English](README.md) | 简体中文

本仓库致力于收集、整理和持续更新全网所有支持 **GLM-5.2**（智谱 AI / Z.ai 旗舰开源大模型）的 API 服务提供商、云端加速方案、聚合网关以及本地自托管（Self-Hosting）部署指南。欢迎提交 PR 补充或修正信息！

> [!NOTE]
> **GLM-5.2 (开源版本于 2026 年 6 月中旬发布)** 是全球首款完全脱离英伟达（Nvidia）硬件依赖、基于华为昇腾（Huawei Ascend）计算芯片训练完成的超大规模混合专家（MoE）旗舰模型。其总参数量约 744B~753B，单 Token 激活参数约 40B，支持百万级（1,048,576 字节）超长上下文。

---

## 📌 目录
- [📊 全网 API 接入平台对照表](#-全网-api-接入平台对照表)
- [💡 核心服务商特色与技术深度剖析](#-核心服务商特色与技术深度剖析)
  - [官方平台与包月订阅](#官方平台与包月订阅)
  - [极致吞吐优化 (Baseten Blackwell)](#极致吞吐优化-baseten-blackwell)
  - [极致性价比路线](#极致性价比路线)
  - [聚合网关与多路容灾](#聚合网关与多路容灾)
- [🤖 智能体场景下的“执行税”与安全审计评估](#-智能体场景下的执行税与安全审计评估)
  - [智能体执行税对比](#智能体执行税对比)
  - [代码安全审计实证](#代码安全审计实证)
- [💻 本地自托管部署与硬件适配补丁](#-本地自托管部署与硬件适配补丁)
  - [消费级终端与统一内存 (Unsloth GGUF)](#消费级终端与统一内存-unsloth-gguf)
  - [CPU-GPU 异构分流 (SGLang + KT-Kernel)](#cpu-gpu-异构分流-sglang--kt-kernel)
  - [RTX 4090 多卡集群部署 (ada_dsa.py 补丁)](#rtx-4090-多卡集群部署-ada_dsapy-补丁)
  - [AMD MI300X 部署避坑指南](#amd-mi300x-部署避坑指南)
- [🎯 业务选型决策矩阵](#-业务选型决策矩阵)
- [🤝 贡献指南](#-贡献指南)
- [📄 开源协议](#-开源协议)

---

## 📊 全网 API 接入平台对照表

下表汇集了官方、主流第三方 Serverless 推理平台以及聚合网关的 GLM-5.2 接入核心数据（数据截至 **2026年6月24日**）：

| 接入平台 | 平台属性 | 官网与文档地址 | Model ID / 接入标识符 | 上下文上限 | 官方定价 / 计费模式 (每 1M Tokens) | 典型 TPS / 性能实测 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **智谱官方 (BigModel.cn)** | 官方中文平台 | [官网](https://bigmodel.cn/) | `glm-5-2` | 1,048,576 | 输入: ¥8.00 / 输出: ¥28.00<br>缓存命中: ¥2.00 | ~19.71 tok/s (常规流速) |
| **Z.ai 官方国际平台** | 官方全球接入 | [官网](https://z.ai/) / [文档](https://docs.z.ai/) | `glm-5.2` | 1,048,576 | 输入: $1.40 / 输出: $4.40<br>缓存命中: $0.26 | ~106 tok/s (高峰流速) |
| **DeepInfra** | 第三方 Serverless | [官网](https://deepinfra.com/) | `zai-org/GLM-5.2` | 1,048,576 | 输入: $0.95 / 输出: $3.00<br>缓存命中: $0.18 | ~32 - 46 tok/s (性价比最高) |
| **SiliconFlow (硅基流动)** | 第三方 Serverless | [官网](https://siliconflow.cn/) | `zai-org/GLM-5.2` | 1,048,576 | 输入: ¥6.00 / 输出: ¥28.00<br>缓存缓存: ¥2.00 | ~36 tok/s (国内超低延迟) |
| **Fireworks AI** | 第三方 Serverless | [官网](https://fireworks.ai/) | `accounts/fireworks/models/glm-5p2` | 1,040,000 | 输入: $1.40 / 输出: $4.40<br>缓存命中: $0.26 *(支持 Fire Pass 订阅)* | ~51 tok/s (高并发稳定性) |
| **Together AI** | 第三方 Serverless | [官网](https://www.together.ai/) | `zai-org/GLM-5.2` | **262,144 (受限)** | 输入: $1.40 / 输出: $4.40<br>缓存命中: $0.26 | ~62 tok/s (FP4 量化) |
| **Novita AI** | 第三方 Serverless | [官网](https://novita.ai/) | `zai-org/glm-5.2` | 1,048,576 | 输入: $1.40 / 输出: $4.40<br>缓存命中: $0.26 | ~18 tok/s |
| **Baseten** | 异构推理/预留部署 | [官网](https://www.baseten.co/) | `zai-org/GLM-5.2` | 1,048,576 | 企业独占 PTU 定制计费 | **超 280 tok/s (全网最速)** |
| **阿里云百炼 (DashScope)** | 云厂商 Serverless | [官网](https://www.aliyun.com/) | `glm-5-2` | 1,048,576 | 输入: ¥8.00 / 输出: ¥28.00 *(可使用 TokenPlan 算力包抵扣)* | N/A (企业级高并发保障) |
| **国家超算互联网** | 算力基础设施 | [官网](https://www.scnet.cn/) | `GLM-5.2` | 1,048,576 | 续购价: **¥0.10**<br>*(新用户赠送 10M Tokens)* | 极高 (多物理算力网节点) |
| **Cloudflare Workers AI** | 边缘托管 Serverless | [官网](https://cloudflare.com/) | `@cf/zai-org/glm-5.2` | **262,144 (受限)** | 输入: $1.40 / 输出: $4.40<br>缓存命中: $0.26 | N/A (边缘节点动态负载) |
| **OpenRouter** | API 聚合路由网关 | [官网](https://openrouter.ai/) | `z-ai/glm-5.2` | 1,048,576 | 综合混计低至 $0.95 / $3.00 | 视动态选定的具体通道而定 |
| **ZenMux.ai** | API 聚合网关 | [官网](https://zenmux.ai/) | `z-ai/glm-5.2` | 1,048,576 | 列表价: $1.40 / $4.40 *(支持月度套餐)* | 视动态选定的具体通道而定 |
| **Vercel AI Gateway** | 企业开发网关 | [官网](https://vercel.com/) | `zai/glm-5.2` | 1,048,576 | 依底层上游实际提供商透传 | 依实际底层通道时延而定 |
| **Neuralwatt** | 功耗计量专属平台 | [Reddit 社区](https://www.reddit.com/r/ZaiGLM/) | `zai-org/GLM-5.2` | 1,048,576 | 实际能耗换算折合 **$0.06 ~ $0.12** | ~60 - 100 tok/s |

> [!WARNING]
> 部分第三方平台（如 **Together AI** 和 **Cloudflare Workers AI**）对 GLM-5.2 的最大上下文窗口做了物理限制（目前为 262,144 Tokens）。若需要处理 1M 上下文长任务，请选择官方平台或其他未作硬性限制的 Serverless 平台。

---

## 💡 核心服务商特色与技术深度剖析

各家 API 提供商在底层硬件、编译器优化以及收费策略上有着极具差异化的定制方案。

### 官方平台与包月订阅
* **取消长度阶梯定价 (BigModel.cn)**: 智谱官方一改往期模型按上下文长度分段收费的模式，GLM-5.2 统一执行固定的 8 元/百万 Tokens 标准（不区分是否大于 32K），极大地平抑了长上下文检索（RAG）和分析场景下的爆单风险。
* **GLM Coding Plan (包月无限订阅)**: 智谱官方与国际平台均推出了包月订阅服务（国内 Lite 版 49 元/月，Pro 版 149 元/月，Max 版 469 元/月；企业 Team 版套餐 $12.60~$112 不等）。此包月模式完美契合 Cursor、Claude Code、Zed 等主流 IDE，支持高强度的代码编写（Vibe Coding），无需担心 Token 账单溢出。
* **阿里云百炼 (DashScope)**: 阿里云百炼提供了 **TokenPlan (企业套餐) 节省计划**，大企业采购统一算力包后，额度可直接跨模型在通义千问 Qwen3 Max 与 GLM-5.2 之间无缝共享和抵扣。

### 极致吞吐优化 (Baseten Blackwell)
Baseten 通过部署最新的 NVIDIA Blackwell GPU 架构，构建了目前全球推理速度最快的 GLM-5.2 专属 API，流式输出实测突破 **280 TPS**。
* **Blackwell NVFP4 动态量化**: 硬件原生级支持全新的 4-bit 浮点（FP4）精度，对 GLM-5.2 的 750B 权重进行了无损压缩，推理速度飞跃的同时保证了代码生成质量不降级。
* **Dynamo 预填充-解码分离 (PD Disaggregation)**: 将消耗算力的大序列 Prefill（预填充 KV Cache）和 Decode（流式 Token 解码）计算解耦至不同的 Blackwell 物理节点运行，彻底消除了高并发下的算力排队瓶颈。
* **Cache 敏感路由**: 精准追踪并复用前序交互留存的上下文缓存，对于长周期交互智能体（如 Claude Code），避免了高达 1MB 级 Prompt 数据的反复重算。

### 极致性价比路线
* **国家超算互联网**: 为扶持本土 OpenClaw 等智能体生态，向实名用户赠送 1000 万免费 Tokens，后续续购价格低至 **0.1 元/百万 Tokens**。
* **Fireworks AI Fire Pass (通票订阅)**: 提供类似于 Netflix 模式的订阅服务（通常为 $10/月），激活后可使用专用 Fire Pass 密钥在 Cline、Claude Code、Kilo Code 等端侧工具中无限次调用 GLM-5.2 模型（限个人开发非商用高并发）。
* **Neuralwatt (功耗计费模式)**: 抛弃按 Token 计费的传统，采用物理 GPU 能耗换算计费。在算力闲置的低谷期使用 GLM-5.2 的 `Max Thinking` 模式，折合每百万输出 Tokens 成本仅为 **$0.06 ~ $0.12**。

### 聚合网关与多路容灾
* **OpenRouter 多路自适应路由**: GLM-5.2 在发布初期由于流量极大，官方接口在高峰期经常触发严重的限流。OpenRouter 可在毫秒级内根据 DeepInfra、Novita、Together 等提供商的延迟、价格及实时在线率（Uptime Stats，GLM-5.2 均线约为 95.12%）自动实现请求的故障转移（Failover）。
* **ZenMux.ai 的“幻觉与时延险”**: ZenMux 内置了实时评测探针。如果检测到上游提供商服务降级、严重延迟抖动（Latency Spike）或者输出了不符合预定 JSON 格式的乱码，系统会自动对调用用户的账户转入等值点数（Credits）作为故障赔付。

---

## 🤖 智能体场景下的“执行税”与安全审计评估

### 智能体执行税对比
在复杂的 Agent 场景下（如自动化浏览器导航、复杂的工具调用、代码重构），如果模型生成不合规的 JSON 或陷入 Tool Call 死循环，框架会被迫不断重试并重复发送庞大的历史上下文。这部分重试产生的额外开销被称为 **“智能体执行税 (Agent Execution Tax)”**。

根据 Fireworks AI 针对 `WebVoyager` 任务（覆盖亚马逊、GitHub、谷歌航班等 15 个商业网站的 60 个多步浏览器任务，重复测试 180 次）的实测数据：

| 模型选型 (Fireworks 物理平台) | 总调用次数 | 格式解析失败/重试次数 | 单词调用重试率 | 任务平均调用步骤数 | 额外消耗的“智能体执行税” (Execution Tax) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **月之暗面 Kimi K2.5** | 852 | 0 | 0.0% | 10.2 | **0.0% (无额外损耗)** |
| **智谱 GLM-5 (旗舰)** | 884 | 5 | 0.6% | 10.3 | **0.6% (几乎无损运行)** |
| **百川 MiniMax M2.5** | 828 | 13 | 1.6% | 9.8 | **1.6% (极轻微损耗)** |
| **谷歌 Gemini 2.5 Flash** | 886 | 165 | 18.6% | 14.7 | **22.9% (严重财务与时延开销)** |

数据表明，虽然部分大厂低阶模型单价非常便宜，但在多步 Agent 任务中因 18.6% 的高重试率，会导致 22.9% 的资金白白浪费在重试 Token 上。GLM-5 旗舰版由于进行了严格的 Tool Calling、MCP 以及结构化输出微调，其执行税率仅为 **0.6%**，在生产环境中综合性价比优势极大。

### 代码安全审计实证
在针对 Flask 路由的越权漏洞（IDOR）静态审计测试中，GLM-5.2 在完全没有任何提示词工程（Prompt Engineering）或外部脚手架保护的裸奔（Bare Prompt）状态下，对漏洞特征的抓取达到了 **39% 的 F1 分数**。相较之下，拥有复杂端侧工具链的顶尖 Agent 工具 Claude Code 的 F1 成绩为 32%。按照 GLM-5.2 的 API 价格折算，每探出一个真实的越权漏洞，仅消耗 **$0.17** 的 Token 成本，极具商业可行性。

---

## 💻 本地自托管部署与硬件适配补丁

### 消费级终端与统一内存 (Unsloth GGUF)
智谱首发支持 Unsloth，推出了动态 GGUF 量化算法。
* **2-bit 动态量化 (UD-IQ2_M)**: 磁盘空间仅需 239GB（相比 1.51TB 的原始 FP16 权重缩减 84%），采用非均匀分配策略，动态保留核心注意力权重，仍保留了 Baseline 82% 的智商精度。
* **运行环境**: 该量化版本可以直接装载在配备 **256GB 统一内存的 Apple Mac Studio** 工作站上。或在个人 PC 上使用一张 24GB 显存显卡（如 RTX 3090/4090）作为 Prefill 引导，搭配 256GB 系统物理主存，即可通过 Ollama / Unsloth Studio 的离线 MoE 专家分流（MoE Offloading）运行。
* **Ollama 启动指令 (需 Ollama 0.30+)**:
  ```bash
  # 在终端中开启 Bash 自动执行 YOLO 模式
  ollama run frob/glm-5.2 --experimental --experimental-yolo

  # 如果任务难度较低，显式禁用思维链以极大加快推理速度
  ollama run frob/glm-5.2 --think=false
  ```

### CPU-GPU 异构分流 (SGLang + KT-Kernel)
通过清华开源的 **KTransformers (KT-Kernel)** 与 SGLang 融合分支，支持在 CPU 内存与 GPU 显存之间混合部署。显卡只承载 MLA 注意力层和少部分高频专家（Experts），而把多达 240 个不常用稀疏专家 offload 到 DDR5 内存中由 CPU 计算。

在 CPU-GPU 混合节点上拉起 FP8 动态量化版 GLM-5.2 的核心命令：
```bash
export PYTORCH_ALLOC_CONF=expandable_segments:True
export SGLANG_ENABLE_JIT_DEEPGEMM=0

python -m sglang.launch_server \
  --model-path /path/to/GLM-5.2-FP8 \
  --kt-weight-path /path/to/GLM-5.2-FP8 \
  --kt-cpuinfer 96 \
  --kt-threadpool-count 2 \
  --kt-num-gpu-experts 30 \
  --kt-method FP8 \
  --max-total-tokens 4096 \
  --trust-remote-code
```
*参数说明*: `--kt-cpuinfer 96` 指定 96 个 CPU 物理核心参与计算；`--kt-num-gpu-experts 30` 则是根据显存空余，在显存中保留 30 个高频专家以加速 Token 吞吐。

### RTX 4090 多卡集群部署 (ada_dsa.py 补丁)
由于 GLM-5.2 底层集成了 DeepSeek-Sparse-Attention (DSA) 算子，原生推理库硬性绑定了英伟达 Hopper 架构（H100/H200）和 Blackwell 架构特有的 WGMMA 指令。如果在消费级 RTX 4090 (sm_89) 上直接部署，会在初始化时崩溃。

社区提供的 [ada_dsa.py](https://github.com/renning22/glm-5.2-4090) 硬件重构补丁：
* **原理**: 利用 Triton 编译器算子以及非 WGMMA tilelang 通路，重写了 Indexer GEMM、Top-K 筛选和 Page-Mapping 算子，使其完美在 sub-Hopper 的 sm_89 显卡上平稳执行。
* **实测**: 在 3 个物理节点上分别配置 8 张 RTX 4090（总计 24 张卡，TP=8, PP=3 分配），配合 patch，可成功拉起完整 GLM-5.2-FP8 权重，并在单并发下实现约 **10 tokens/sec** 的高流式交互速度。

### AMD MI300X 部署避坑指南
在 AMD Instinct MI300X (192 GiB 显存) 集群上，通过 ROCm + vLLM 部署运行 GLM-5.2 时需规避以下两个生产级 Bug：
1. **PP 模式下的 Tool Calling 乱码漏洞**: 在开启 Pipeline Parallel (`PP>=2`) 时，vLLM 底层的 `condense()` 函数在处理带有复杂 tools XML 语法的长 Prompt 时，会由于 Token 计数器偏移发生微弱丢字，导致工具调用乱码死循环。**临时解决方案是：退回 PP1 模式并开启 TP8（单节点 8 卡张量并行）**，工具调用即恢复正常。
2. **HIP 内存崩溃崩溃 (OOM)**: 启动时必须在命令行参数中强制追加 `--disable-custom-all-reduce` 选项，以避免 HIP 内部自定义规规约缓冲池初始化失败导致的崩溃。同时，**必须**开启 `--enable-chunked-prefill`（分块预填充），否则在处理大序列 RAG 注意力时会直接撑爆显存。

---

## 🎯 业务选型决策矩阵

| 团队规模与场景定位 | 资源预算特征 | 推荐方案 | 核心考量与决策逻辑 |
| :--- | :--- | :--- | :--- |
| **独立开发者 / 个人初创** | 预算极其有限，以快速验证 MVP 为主 | **国家超算互联网 (中国大陆) / DeepInfra** | 利用超算互联网首发赠送的 **1000 万免费 Tokens** 快速冷启动；后续依靠 **0.1元/百万 Tokens** 续购价或 DeepInfra 最优单价降低测试与运行成本。 |
| **高频开发者 / Vibe Coding 玩家** | 每天极高频调用，需要深度嵌入 Cursor / Claude Code | **Z.ai 官方 Coding Plan 包月订阅** | 购买 **Pro (¥149/月) 或 Max (¥469/月) 订阅套餐**。包月订阅模式从根本上隔绝了因为高频代码库扫描/代码重写导致 API 额度超标的财务风险。 |
| **高并发、高实时性商业 SaaS** | 生产级负载，对延迟和流式吐出吞吐高度敏感 | **Baseten 专属 Blackwell 托管** | **效率决定体验**：Baseten 专属 Blackwell 能够压榨出 **280+ TPS** 极端性能，极大地缩短了用户使用复杂智能体时的页面白屏阻塞，显著优化商业 ROI。 |
| **涉密、金融/安全审计等红线企业** | 数据严禁出境，要求高并发与物理隐私权 | **RTX 4090 集群 (打上 ada_dsa 补丁)** | 绕过昂贵且受到出口禁运限制的 Hopper (H100) 芯片，直接在容易采购且极其廉价的 4090 显卡集群上拉起无损的 FP8 旗舰模型，实现完全物理隔离下的代码审计与工程分析。 |

---

## 🤝 贡献指南

我们非常欢迎社区参与本仓库的维护！您可以：
1. **修正或更新价格**：各大云厂商竞争激烈，价格常有变动。如果发现对照表数据过时，请随时提 PR 修正。
2. **分享自托管经验**：如果您在其他国产算力芯片（如寒武纪、燧原、摩尔线程等）或特定推理框架下成功跑通了 GLM-5.2，欢迎提交部署步骤补丁。
3. **补充新的 API 提供商**：请将新提供商加入对照表，并列出相应测试数据。

---

## 📄 开源协议

本项目基于 [MIT 协议](LICENSE) 开源。

---

*免责声明：本仓库所列出的服务商信息、定价以及实测数据均来自开源社区与研究公开文档，实际性能指标或商业定价可能会因厂商实时调整而有所变动，请在生产环境采购前以服务商官网最新页面为准。*
