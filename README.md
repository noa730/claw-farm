<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/images/logo-dark.svg" />
    <source media="(prefers-color-scheme: light)" srcset="assets/images/logo-light.svg" />
    <img src="assets/images/logo-light.svg" width="320" alt="Claw Farm Logo" />
  </picture>
</p>

# Claw Farm - 有机循环农业智能决策系统

> 探索 AI Agent 在复杂生态农业系统中的应用

## 项目简介

本项目是一个**合作探索性项目**，旨在研究智能体（AI Agent）如何辅助复杂生态农业系统的决策与管理。

我们以 **"松茸 - 蚯蚓 - 有机肥 - 稻"** 循环农业模式为核心场景，尝试回答一个问题：

*AI 能否理解并优化一个多物种、多变量、动态耦合的生态农业系统？*

## 循环模式

```mermaid
graph LR
    A[松茸种植] -->|菌糠废料| B[蚯蚓养殖]
    B -->|蚯蚓粪| C[有机肥加工]
    C -->|施肥还田| D[水稻种植]
    D -->|稻秆基质| A
    B -->|改善土壤| D
```

## 核心探索方向

- **松茸栽培管理** — 基质配方、温湿度与出菇周期的智能调控
- **蚯蚓养殖优化** — 密度、投喂策略与菌糠转化效率
- **有机肥质量控制** — 蚯蚓粪腐熟度、养分配比的分析与预警
- **水稻生长决策** — 从秧苗期到成熟期的施肥与管理
- **循环链路协调** — 四环节间物质流与时序的统筹决策

## 系统架构

```mermaid
graph TB
    subgraph CLAUDE_MD["CLAUDE.md — 全局指令"]
        SY[Synergy 循环规则]
        ST[Strategy 商业摘要]
    end

    subgraph AGENTS["agents/ — 专家 Agent"]
        MA[Matsutake-Expert<br/>松茸专家]
        RA[Rice-Expert<br/>水稻专家]
        WA["Worm-Expert ·····<br/>(规划中)"]
    end

    subgraph SKILLS[".claude/skills/ — 技能工具箱"]
        direction LR
        subgraph GLOBAL["全局技能"]
            S_ENV[skill-env-monitor<br/>环境监测]
            S_CAM[skill-camera-capture<br/>摄像头采集]
        end
        subgraph MATSUTAKE["松茸专属"]
            S_SP[skill-spore-analysis<br/>孢子分析]
            S_SY[skill-symbiotic-matching<br/>共生匹配]
            S_MY[skill-mycelium-growth-model<br/>菌丝生长模型]
        end
    end

    subgraph KNOWLEDGE["docs/distilled/ — 知识库"]
        K_SHARED["shared/<br/>context.md · strategy.md"]
        K_MAT["matsutake/<br/>生长条件 · 产量因子 · 共生"]
        K_RICE["rice/<br/>生长阶段 · 施肥逻辑"]
        K_WORM["worm/<br/>投喂SOP"]
    end

    CLAUDE_MD --> AGENTS
    MA --> MATSUTAKE
    MA --> GLOBAL
    RA --> GLOBAL
    AGENTS --> KNOWLEDGE

    style CLAUDE_MD fill:#2d7a3a,color:#fff
    style GLOBAL fill:#e8f5e9
    style MATSUTAKE fill:#fff3e0
    style KNOWLEDGE fill:#f3e5f5
```

## 项目结构

```
claw-farm/
├── CLAUDE.md                        # 全局指令（Synergy规则 + Strategy摘要）
├── agents/
│   └── matsutake-expert/            # 松茸专家 Agent 定义
│       └── AGENT.md
├── .claude/skills/                  # 技能工具箱
│   ├── skill-env-monitor/           #   🌍 全局：环境监测
│   ├── skill-camera-capture/        #   📷 全局：摄像头采集
│   ├── rice-expert/                 #   🌾 水稻专属技能
│   └── matsutake-expert/            #   🍄 松茸专属技能
│       ├── skill-spore-analysis/    #     孢子分析
│       ├── skill-symbiotic-matching/#     共生匹配
│       └── skill-mycelium-growth-model/  菌丝生长模型
├── docs/
│   ├── distilled/                   # 提炼后的知识库
│   │   ├── shared/                  #   项目背景 + 商业逻辑
│   │   ├── matsutake/               #   松茸知识
│   │   ├── rice/                    #   水稻知识
│   │   └── worm/                    #   蚯蚓知识
│   └── products/                    # 产品文档（Web问答PRD等）
├── assets/
│   ├── images/                      # Logo 等图片资源
│   └── sources/                     # 原始 PDF 参考资料
└── scripts/                         # 数据处理与分析脚本
```

## 参与方式

这是一个开放的探索性项目，欢迎对以下方向感兴趣的伙伴参与：

- 生态农业 / 循环农业实践经验
- AI Agent 应用开发
- 农业数据分析与建模

## 致谢

图片来源：[Unsplash](https://unsplash.com)（免费开源图片）
