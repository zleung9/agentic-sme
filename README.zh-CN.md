# SME-Agent

> [English](README.md) | **中文**

面向量子干涉单分子器件的、基于物理假设驱动的多智能体分子发现框架。

本项目正从旧版 **SMEGen**（闭环生成管线）演进为 **SME-Agent**——一个智能体分子联合科学家，能够阅读文献、提出量子输运假设、设计分子、评审候选分子、进行计算验证，并支持前瞻性实验验证。

---

## 生成模型 — 架构

旗舰生成器为**条件图扩散（DiGress）搭配 TransformerConv 主干网络**。这一选择基于对两类主流离散扩散方法的评估：

| 方法 | 优势 | 对 SME-Agent 的不足 |
|---|---|---|
| **DiGress**（GNN 去噪器，默认 RGCN） | 小数据集（~171 个分子）上数据效率高；擅长局部锚基团-骨架模式识别 | 逐层消息传递会稀释量子干涉所需的长程信号 |
| **Graph DiT**（Transformer 去噪器） | 全局注意力机制原生捕捉 QI 相关的长程效应；随数据量扩展性好 | 小数据集冷启动困难；代码库成熟度低 |

**TransformerConv 主干网络**（PyG）将 DiGress 默认的 RGCN 替换为局部注意力 GNN 层——这是务实的折中方案。它在保留 DiGress 数据效率的同时，实现了比纯 RGCN 更好的长程信号传播。当数据集增长到 ~500 个分子以上时，计划升级为完整 Graph DiT。

### 生成器阵容

| 生成器 | 角色 |
|---|---|
| **图扩散（DiGress + TransformerConv）** | 主模型 |
| **REINVENT4 风格 Transformer/RL** | 强实用基线 |
| **旧版 LSTM（字符级 SMILES）** | 小数据 / 复现基线 |
| **随机 SME 式枚举** | 非 AI 基线（衡量任务难度） |
| **专家规则枚举** | 化学专家会怎么做？ |
| **SELFIES Transformer** | LSTM 和图扩散之间的桥梁 |

所有生成器均支持基于以下条件进行分子生成：DQI 标签、Seebeck 系数、调控范围、调控效率、SAscore、骨架约束和锚基团有效性。

### SMEPre 代理预测器

与生成器并行构建，采用不确定性感知集成模型（RDKit/XGBoost 快速层 + 5 成员 MPNN 主层）。处理 DQI 分类、Seebeck/调控回归以及 SAscore——用于条件控制和 REINVENT4 评分函数。

---

## 实施计划

### 阶段 0 — 项目骨架与数据（第 1-2 周）
- uv + pyproject.toml 包结构搭建
- 从手稿提取 SMEMol 数据集（DOCX → 解析 SMILES）
- 去重、标准化、标注锚基团/骨架/层级
- 基于骨架拆分的训练/验证/测试集

### 阶段 1 — 基线模型（第 3-4 周）
- 实现全部五个基线，统一 `.generate(n)` 接口
- 基准测试：有效性、唯一性、新颖性、多样性、骨架新颖性、锚基团有效性、SAscore

### 阶段 2 — SMEPre 预测器（第 3-4 周，并行）
- 构建不确定性感知集成模型（XGBoost + 5 成员 MPNN）
- 骨架拆分交叉验证；校准 + 不确定性诊断

### 阶段 3 — 图扩散生成器（第 5-8 周）
- 将 DiGress 纳入 `src/sme_agent/generators/digress/`
- 添加 SME 特定的原子特征化（锚原子、骨架原子、环大小）
- 将 RGCN 主干替换为 PyG TransformerConv
- 实现无分类器引导的多属性条件生成
- 无监督预训练 → 条件微调；对比基线验证生成质量

### 阶段 4 — 评估框架（第 4 周起，持续迭代）
- 统一 CLI：`scripts/run_benchmarks.py --generator <名称>`
- 评估指标：有效性、唯一性、新颖性、多样性、骨架新颖性、锚基团有效性、SAscore、属性可控性
- 结果记录至 `results/benchmark_{时间戳}.json`

### 阶段 5 — 主动学习循环（第 9-10 周）
- 生成 10 万-20 万个分子 → 过滤 → SMEPre 评分 → 多样性筛选 → DFT 批次
- 选择 50 个分子（40% 利用、40% 探索、20% 对照）
- 准备 DFT+NEGF 输入文件；锁定前瞻性验证集

---

## 快速开始

```bash
# 安装（源码就绪后）
uv pip install -e .

# 提取并审计旧版数据集
python scripts/extract_smemol.py
python scripts/audit_dataset.py

# 运行基线基准测试
python scripts/run_benchmarks.py --generator lstm --n 10000
python scripts/run_benchmarks.py --generator selfies_tf --n 10000
python scripts/run_benchmarks.py --generator random_enum --n 10000
```

---

## 文档

| 文档 | 用途 |
|---|---|
| [`.claude/CLAUDE.md`](.claude/CLAUDE.md) | 完整项目规范、架构、数据集策略、智能体指令 |
| [`.claude/MEMORY.md`](.claude/MEMORY.md) | 项目历史、源文档、3 个月研究计划 |
| [`docs/literature-survey.html`](docs/literature-survey.html) | 领域文献综述 |
| [`docs/literature-survey-ai-sme.html`](docs/literature-survey-ai-sme.html) | AI-for-SME 文献综述 |

---

## 许可证

待定
