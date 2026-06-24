# 🚀 Awesome GLM-5.2 API Providers & Self-Hosting Guide

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![Model](https://img.shields.io/badge/Model-GLM--5.2%20%2F%20Z.ai-blue)](https://bigmodel.cn/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)
[![Updated](https://img.shields.io/badge/Last%20Updated-2026--06%20-orange)](#)

English | [简体中文](README_zh.md)

This repository is dedicated to collecting, organizing, and continuously updating the list of all API providers, cloud acceleration solutions, aggregation gateways, and local self-hosting deployment guides for **GLM-5.2** (Z.ai / Zhipu AI's flagship open-weight LLM). Contributions via PRs are welcome!

> [!NOTE]
> **GLM-5.2 (Open-weight version released in mid-June 2026)** is the world's first frontier MoE model trained completely without Nvidia hardware dependencies, built entirely on Huawei Ascend hardware. It features a total parameter count of 744B~753B, dynamically activating 40B parameters per token, and supports up to 1M (1,048,576 tokens) context window.

---

## 📌 Table of Contents
- [📊 API Providers Matrix](#-api-providers-matrix)
- [💡 Deep Dive: Core Providers & Features](#-deep-dive-core-providers--features)
  - [Official Portals & Subscription Plans](#official-portals--subscription-plans)
  - [Extreme Throughput (Baseten Blackwell)](#extreme-throughput-baseten-blackwell)
  - [Extreme Cost Efficiency](#extreme-cost-efficiency)
  - [Aggregation Gateways & Multi-Path Failover](#aggregation-gateways--multi-path-failover)
- [🤖 Agentic Workflows & "Execution Tax" Evaluation](#-agentic-workflows--execution-tax-evaluation)
  - [Agent Execution Tax Comparison](#agent-execution-tax-comparison)
  - [Vulnerability Audit Case Study](#vulnerability-audit-case-study)
- [💻 Self-Hosting & Hardware Compatibility Guide](#-self-hosting--hardware-compatibility-guide)
  - [Consumer Edge & Unified Memory (Unsloth GGUF)](#consumer-edge--unified-memory-unsloth-gguf)
  - [CPU-GPU Heterogeneous Offloading (SGLang + KT-Kernel)](#cpu-gpu-heterogeneous-offloading-sglang--kt-kernel)
  - [RTX 4090 Cluster Deployment (ada_dsa.py Patch)](#rtx-4090-cluster-deployment-ada_dsapy-patch)
  - [AMD MI300X Production Workarounds](#amd-mi300x-production-workarounds)
- [🎯 Business Decision Matrix](#-business-decision-matrix)
- [🤝 Contributing](#-contributing)
- [📄 License](#-license)

---

## 📊 API Providers Matrix

The following table aggregates the technical and economic metrics of official channels, third-party Serverless platforms, and API gateways for GLM-5.2 (Data verified as of **June 24, 2026**):

| Provider | Classification | Website & Docs | Model ID / Identifier | Context Limit | Pricing / Billing Model (per 1M Tokens) | Typical TPS / Performance |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Z.ai Global (Official)** | Official Global Portal | [Website](https://z.ai/) / [Docs](https://docs.z.ai/) | `glm-5.2` | 1,048,576 | Input: $1.40 / Output: $4.40<br>Cache Hit: $0.26 | ~106 tok/s (Peak flow speed) |
| **BigModel.cn (Official)** | Official China Portal | [Website](https://bigmodel.cn/) | `glm-5-2` | 1,048,576 | Input: ¥8.00 / Output: ¥28.00<br>Cache Hit: ¥2.00 | ~19.71 tok/s (Standard speed) |
| **DeepInfra** | Serverless Provider | [Website](https://deepinfra.com/) | `zai-org/GLM-5.2` | 1,048,576 | Input: $0.95 / Output: $3.00<br>Cache Hit: $0.18 | ~32 - 46 tok/s (Best value) |
| **SiliconFlow** | Serverless Provider | [Website](https://siliconflow.cn/) | `zai-org/GLM-5.2` | 1,048,576 | Input: ¥6.00 / Output: ¥28.00<br>Cache Hit: ¥2.00 | ~36 tok/s (Low latency in China) |
| **Fireworks AI** | Serverless Provider | [Website](https://fireworks.ai/) | `accounts/fireworks/models/glm-5p2` | 1,040,000 | Input: $1.40 / Output: $4.40<br>Cache Hit: $0.26 *(Fire Pass available)* | ~51 tok/s (High concurrency) |
| **Together AI** | Serverless Provider | [Website](https://www.together.ai/) | `zai-org/GLM-5.2` | **262,144 (Restricted)** | Input: $1.40 / Output: $4.40<br>Cache Hit: $0.26 | ~62 tok/s (FP4 Quantized) |
| **Novita AI** | Serverless Provider | [Website](https://novita.ai/) | `zai-org/glm-5.2` | 1,048,576 | Input: $1.40 / Output: $4.40<br>Cache Hit: $0.26 | ~18 tok/s |
| **Baseten** | Dedicated Instance / PTU | [Website](https://www.baseten.co/) | `zai-org/GLM-5.2` | 1,048,576 | Custom PTU Enterprise Billing | **>280 tok/s (Fastest overall)** |
| **Alibaba Cloud (DashScope)** | Cloud Provider | [Website](https://www.aliyun.com/) | `glm-5-2` | 1,048,576 | Input: ¥8.00 / Output: ¥28.00 *(TokenPlan credits apply)* | N/A (Enterprise concurrency SLA) |
| **Cloudflare Workers AI** | Edge Serverless | [Website](https://cloudflare.com/) | `@cf/zai-org/glm-5.2` | **262,144 (Restricted)** | Input: $1.40 / Output: $4.40<br>Cache Hit: $0.26 | N/A (Dynamic edge load) |
| **OpenRouter** | Aggregation Gateway | [Website](https://openrouter.ai/) | `z-ai/glm-5.2` | 1,048,576 | Dynamic Blended down to $0.95 / $3.00 | Depends on chosen backend |
| **ZenMux.ai** | Aggregation Gateway | [Website](https://zenmux.ai/) | `z-ai/glm-5.2` | 1,048,576 | List Price: $1.40 / $4.40 *(Subscription tiers available)* | Depends on chosen backend |
| **Vercel AI Gateway** | Developer Gateway | [Website](https://vercel.com/) | `zai/glm-5.2` | 1,048,576 | Passthrough billing of backend | Depends on chosen backend |
| **Neuralwatt** | Energy-Metered Platform | [Reddit Community](https://www.reddit.com/r/ZaiGLM/) | `zai-org/GLM-5.2` | 1,048,576 | GPU Energy Metered: **$0.06 ~ $0.12** | ~60 - 100 tok/s |
| **OpenCode Go** | Subscription Service | [Website](https://opencode.ai/) | `opencode/glm-5.2` | 1,048,576 | $5 first month, then $10/mo<br>*(Rolling credits: $12/5h, $30/wk, $60/mo)* | Depends on backend provider |

> [!WARNING]
> Some third-party platforms (like **Together AI** and **Cloudflare Workers AI**) physically limit the maximum context window of GLM-5.2 to **262,144 tokens**. For 1M long-context tasks, choose the official platform or other Serverless providers without context window caps.

---

## 💡 Deep Dive: Core Providers & Features

API providers have heavily optimized the GLM-5.2 infrastructure layer, offering distinct hardware setups, compiler improvements, and commercial models.

### Official Portals & Subscription Plans
* **Flat-Rate Long Context Pricing (BigModel.cn / Z.ai)**: Official pricing has removed length-based tiers. GLM-5.2 charges a flat ¥8.00 / $1.40 per million input tokens, completely mitigating the risk of billing spikes during full-codebase audits or massive RAG search tasks.
* **GLM Coding Plan (Unlimited Monthly Subscription)**: Official subscriptions (Lite: 49元/mo, Pro: 149元/mo, Max: 469元/mo; Enterprise Team plans: $12.60 to $112) allow developers to bind GLM-5.2 into Zed, Cursor, Claude Code, or Z.ai's official Z Code desktop terminal, facilitating high-intensity "Vibe Coding" without per-token constraints.
* **Alibaba Cloud DashScope**: DashScope offers **TokenPlan and Savings Plan** models. Unified enterprise credits can be shared across models, letting enterprises seamlessly balance Qwen3 Max and GLM-5.2 calls.

### Extreme Throughput (Baseten Blackwell)
Baseten utilizes dedicated NVIDIA Blackwell GPU clusters to achieve the fastest GLM-5.2 API worldwide, clocking in at over **280 TPS** for streaming output.
* **Blackwell NVFP4 Native Quantization**: Using native 4-bit floating-point (FP4) hardware execution on Blackwell GPUs to compress the 750B model weights without degrading coding generation quality.
* **Dynamo Prefill-Decode Disaggregation (PD separation)**: Decouples the computationally heavy Prompt Prefill (establishing KV Cache) from the streaming Decode phase by running them on distinct physical Blackwell nodes.
* **Cache-Sensitive Routing**: Tracks and preserves KV Caches across interactive turns. In long-horizon coding tasks (e.g., using Claude Code), this avoids repeated calculation of up to 1MB prompts.

### Extreme Cost Efficiency
* **Fireworks AI Fire Pass**: A subscription model ($10/month) that unlocks a developer key for GLM-5.2. Personal projects using Claude Code, Cline, or Kilo Code can call the model with zero per-token cost (restricted to non-production usage).
* **Neuralwatt (Energy-based Billing)**: Users pay based on real GPU power consumption. Running GLM-5.2 in `Max Thinking` mode during off-peak night hours yields an effective pricing of **$0.06 ~ $0.12 per million output tokens**.
* **OpenCode Go Subscription**: A low-cost developer bundle subscription costing $5 for the first month, then $10/month. Instead of raw call counts, it operates on a rolling dollar-equivalent credit system ($12 per 5 hours, $30 per week, and $60 per month). It provides a unified API key to access GLM-5.2 alongside other open-weight models, offering excellent value for active agent development.

### Aggregation Gateways & Multi-Path Failover
* **OpenRouter Multi-Provider Failover**: GLM-5.2's launch triggered huge traffic spikes, causing official endpoint throttling. OpenRouter mitigates this by automatically failing over in milliseconds across DeepInfra, Novita, Together, and official servers based on real-time latency, price, and uptime statistics (GLM-5.2 uptime averaging ~95.12%).
* **ZenMux.ai "Hallucination & Latency Insurance"**: An integrated probe monitors upstream API performance. If it detects severe latency spikes, upstream silent downgrades, or invalid JSON structures, ZenMux credits the user's account automatically as compensation.

---

## 🤖 Agentic Workflows & "Execution Tax" Evaluation

### Agent Execution Tax Comparison
In complex, multi-step Agent workflows (such as browser control, tool calls, and codebase refactoring), if a model outputs invalid JSON or gets stuck in tool-calling loops, the framework must resend the entire interaction history. This overhead is defined as the **"Agent Execution Tax"**.

Based on system-wide stress tests on the `WebVoyager` suite (60 tasks on 15 commercial websites like Amazon and GitHub, repeated 180 times):

| Model Selection (on Fireworks) | Total LLM Calls | Parse/JSON Retries | Retry Rate | Avg. Calls per Task | "Agent Execution Tax" (Overhead) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Moonshot Kimi K2.5** | 852 | 0 | 0.0% | 10.2 | **0.0% (Zero overhead)** |
| **Z.ai GLM-5 (Flagship)** | 884 | 5 | 0.6% | 10.3 | **0.6% (Near-perfect run)** |
| **Baichuan MiniMax M2.5** | 828 | 13 | 1.6% | 9.8 | **1.6% (Negligible overhead)** |
| **Google Gemini 2.5 Flash** | 886 | 165 | 18.6% | 14.7 | **22.9% (Severe financial/latency tax)** |

While some low-cost models seem cheaper on paper, their 18.6% error rate means 22.9% of budget is wasted on retries. Due to dedicated fine-tuning for Tool Calling, MCP, and structured outputs, GLM-5 flagship experiences a tax of only **0.6%**, offering superior cost-efficiency at scale.

### Vulnerability Audit Case Study
In auditing Insecure Direct Object Reference (IDOR) vulnerabilities on Flask backends, GLM-5.2 achieved a **39% F1-score** using raw prompts ("bare prompt") without prompt engineering or scaffolds. In comparison, Claude Code (utilizing its full agent toolset) achieved a 32% F1-score. Based on GLM-5.2's API cost, the scanning system averaged just **$0.17 per confirmed vulnerability**, making enterprise-wide code auditing highly viable.

---

## 💻 Self-Hosting & Hardware Compatibility Guide

### Consumer Edge & Unified Memory (Unsloth GGUF)
Unsloth provides day-zero optimized dynamic GGUF quantization tables:
* **2-bit Dynamic Quantization (UD-IQ2_M)**: Only requires 239GB disk space (84% reduction from 1.51TB FP16 weights). By dynamically preserving linear attention weights, it retains **82% of baseline intelligence**.
* **System Requirements**: Runs natively on an **Apple Mac Studio (256GB Unified Memory)**. On personal PCs, it requires a single 24GB VRAM GPU (e.g., RTX 3090/4090) for Prefill acceleration paired with 256GB system RAM, utilizing off-line MoE offloading.
* **Ollama Boot Command (Ollama v0.30+)**:
  ```bash
  # Enable Bash auto-execution (YOLO mode)
  ollama run frob/glm-5.2 --experimental --experimental-yolo

  # Disable thinking process for simpler tasks to speed up inference
  ollama run frob/glm-5.2 --think=false
  ```

### CPU-GPU Heterogeneous Offloading (SGLang + KT-Kernel)
Utilizing Tsinghua's open-source **KTransformers (KT-Kernel)** SGLang integration allows running the model across CPU RAM and GPU VRAM. The GPU handles MLA attention layers and high-frequency experts, offloading the remaining 240 sparse experts to DDR5 system memory.

Run SGLang server with FP8 quantized GLM-5.2:
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
*Note*: `--kt-cpuinfer 96` designates 96 CPU cores; `--kt-num-gpu-experts 30` keeps 30 experts cached in VRAM to boost throughput.

### RTX 4090 Cluster Deployment (ada_dsa.py Patch)
GLM-5.2 uses DeepSeek-Sparse-Attention (DSA) operators, which native vLLM and SGLang compilers bind to Nvidia Hopper (H100/H200) and Blackwell (B200) specific WGMMA instructions. Running it on consumer Ada GPUs (RTX 4090 / sm_89) triggers runtime crashes.

The community patch [ada_dsa.py](https://github.com/renning22/glm-5.2-4090) resolves this:
* **Principle**: Uses custom Triton operators and non-WGMMA tilelang paths to rewrite Indexer GEMM, Top-K, and Page-Mapping operators, enabling execution on sm_89 hardware.
* **Deployment**: Distributing the FP8 model across 24x RTX 4090 GPUs (spread across 3 nodes with TP=8, PP=3 configuration) yields a stream throughput of **~10 tokens/sec** at full FP8 accuracy.

### AMD MI300X Production Workarounds
When serving GLM-5.2 via ROCm + vLLM on AMD Instinct MI300X (192 GiB) nodes, work around these issues:
1. **PP Garbled Output during Tool Calls**: When Pipeline Parallelism is enabled (`PP>=2`), vLLM's internal `condense()` function experiences token pointer alignment shifts on long XML-heavy tool prompts. **Fix**: Run in single-node Tensor Parallelism mode (`PP1`, `TP8`).
2. **HIP Memory Crashes (OOM)**: You must append `--disable-custom-all-reduce` to prevent HIP custom all-reduce allocator buffer initialization failures. Additionally, enable `--enable-chunked-prefill` to prevent initial RAG context windows from saturating the MI300X VRAM.

---

## 🎯 Business Decision Matrix

| Team Profile / Scenario | Resource Budget | Recommended Path | Decision Rationale |
| :--- | :--- | :--- | :--- |
| **Independent Dev / MVP Startup** | Extremely low budget, validating MVPs | **DeepInfra (US) / SiliconFlow (CN)** | Utilize pay-per-token options on DeepInfra ($0.95/1M input) or SiliconFlow (¥6.00/1M input) to start MVP testing with minimal early capital expenses. |
| **Active Developer / Vibe Coding** | Continuous daily calls, deep Cursor/Claude Code binding | **Z.ai Official Coding Plan** | The flat-rate monthly subscriptions (**Pro at ¥149/mo** or **Max at ¥469/mo**) shield developers from heavy billing run-ups caused by continuous codebase scanning. |
| **High Concurrency SaaS Platform** | Large scale production workloads, highly latency-sensitive | **Baseten Dedicated Blackwell** | **Latency defines retention**: Baseten's Blackwell native FP4 path outputs at **280+ TPS**, eliminating UI blocking during multi-step Agent calls. |
| **Regulated/Financial/Auditing Corp** | Hard data-compliance guidelines, strict physical privacy | **RTX 4090 Cluster (with ada_dsa patch)** | By bypassing export-restricted H100s, enterprises can build secure, air-gapped FP8 instances on affordable, consumer-grade hardware. |

---

## 🤝 Contributing

We welcome community contributions! You can:
1. **Submit Price Updates**: Cloud provider pricing changes rapidly. Feel free to open a PR to adjust tables.
2. **Share Self-Hosting Guides**: If you successfully run GLM-5.2 on alternative AI accelerators (e.g., Ascend, MI300X native configurations, Moore Threads), submit an issue or PR with instructions.
3. **Add New Providers**: Add new verified API channels along with corresponding latency/concurrency metrics.

---

## 📄 License

This repository is licensed under the [MIT License](LICENSE).

---

*Disclaimer: All provider details, pricing, and benchmark metrics are compiled from public community resources and documentation. Commercial offerings may change; check vendor portals before purchasing.*
