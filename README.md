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

我们以 **"菌菇 - 蚯蚓 - 生物肥 - 稻"** 循环农业模式为核心场景，尝试回答一个问题：

*AI 能否理解并优化一个多物种、多变量、动态耦合的生态农业系统？*

项目脱胎于梁子湖区梁子镇全域国土综合整治项目，覆盖刘斌村、沙湾村、长岭居委会，总面积约 1065 公顷。

## 循环模式

```mermaid
graph LR
    A[菌菇种植] -->|菌糠废料| B[蚯蚓养殖]
    B -->|蚯蚓粪| C[生物肥加工]
    C -->|施肥还田| D[水稻种植]
    D -->|稻秆基质| A
    B -->|改善土壤| D
    B -->|蚯蚓活体| E[养殖/销售]
```

循环链路遵循三层逻辑：**物质流循环**（废料→原料）、**空间复合利用**（林下种菌/养鸭）、**产出多向输出**（蚯蚓→改土/喂鱼/销售/生物肥）。

## 分期计划

| 阶段 | 时间 | 核心目标 | 状态 |
|------|------|---------|------|
| 第一期 | 2026.4 起 | 水稻启动 + 菌菇试种 + 框架搭建 | 进行中 |
| 第二期 | 待定 | 蚯蚓养殖启动，闭环跑通 | 规划中 |
| 第三期 | 待定 | 规模化 + 外部合作（光伏等） | 远期 |

## 核心探索方向

- **菌菇栽培管理** — 基质配方、温湿度与出菇周期的智能调控（草菇→松茸路线）
- **蚯蚓养殖优化** — 密度、投喂策略与菌糠转化效率
- **生物肥质量控制** — 蚯蚓粪腐熟度、养分配比的分析与预警
- **水稻生长决策** — 从秧苗期到成熟期的施肥与管理
- **循环链路协调** — 四环节间物质流与时序的统筹决策

## 系统架构

<p align="center">
  <img src="assets/images/architecture.svg" width="700" alt="System Architecture" />
</p>

```mermaid
graph TB
    subgraph CLAUDE_MD["CLAUDE.md — 全局指令"]
        SY[Synergy 循环规则]
        ST[Strategy 商业摘要]
    end

    subgraph AGENTS["agents/ — 专家 Agent"]
        MA[Matsutake-Expert<br/>菌菇专家]
        RA[Rice-Expert<br/>水稻专家]
        WA["Worm-Expert ·····<br/>(规划中)"]
        FA["Fertilizer-Expert ·····<br/>(规划中)"]
    end

    subgraph SKILLS[".claude/skills/ — 技能工具箱"]
        direction LR
        subgraph GLOBAL["全局技能"]
            S_ENV[skill-env-monitor<br/>环境监测]
            S_CAM[skill-camera-capture<br/>摄像头采集]
        end
        subgraph MUSHROOM["菌菇专属"]
            S_SP[skill-spore-analysis<br/>孢子分析]
            S_SY[skill-symbiotic-matching<br/>共生匹配]
            S_MY[skill-mycelium-growth-model<br/>菌丝生长模型]
        end
        subgraph RICE["水稻专属"]
            S_RE[rice-expert<br/>水稻种植专家]
        end
    end

    subgraph KNOWLEDGE["docs/distilled/ — 知识库"]
        K_SHARED["shared/<br/>context · strategy · synergy · phasing"]
        K_MUS["mushroom/<br/>生长条件 · 产量因子 · 共生 · 设施方案"]
        K_RICE["rice/<br/>生长阶段 · 施肥逻辑"]
        K_WORM["worm/<br/>投喂SOP · 产出利用 · 场地规划"]
        K_FERT["fertilizer/<br/>加工流程 · 检测标准"]
    end

    subgraph PRODUCTS["docs/products/ — 产品文档"]
        P_QA["web-qa-prd.md<br/>Web问答系统PRD"]
    end

    CLAUDE_MD --> AGENTS
    MA --> MUSHROOM
    MA --> GLOBAL
    RA --> RICE
    RA --> GLOBAL
    AGENTS --> KNOWLEDGE
    AGENTS --> PRODUCTS

    style CLAUDE_MD fill:#2d7a3a,color:#fff
    style GLOBAL fill:#e8f5e9
    style MUSHROOM fill:#fff3e0
    style RICE fill:#e3f2fd
    style KNOWLEDGE fill:#f3e5f5
    style PRODUCTS fill:#fce4ec
```

## 项目结构

```
claw-farm/
├── CLAUDE.md                        # 全局指令（Synergy规则 + Strategy摘要）
├── agents/
│   └── matsutake-expert/            # 菌菇专家 Agent 定义
│       └── AGENT.md
├── .claude/
│   ├── commands/                    # 自定义命令
│   │   ├── commit.md                #   智能提交
│   │   ├── pr.md                    #   Pull Request
│   │   ├── optimize.md              #   性能优化
│   │   ├── setup-ci-cd.md           #   CI/CD 配置
│   │   └── ...                      #   doc-refactor / push-all / unit-test-expand 等
│   └── skills/                      # 技能工具箱
│       ├── skill-env-monitor/       #   全局：环境监测
│       ├── skill-camera-capture/    #   全局：摄像头采集
│       ├── rice-expert/             #   水稻专属技能
│       └── matsutake-expert/        #   菌菇专属技能
│           ├── skill-spore-analysis/       孢子分析
│           ├── skill-symbiotic-matching/   共生匹配
│           └── skill-mycelium-growth-model/菌丝生长模型
├── docs/
│   ├── distilled/                   # 提炼后的知识库
│   │   ├── shared/                  #   context（项目背景）· strategy（商业逻辑）
│   │   │                            #   synergy（循环链路）· phasing（分期计划）
│   │   │                            #   exhibition_boards（展板计划）
│   │   ├── mushroom/                #   growth_conditions · yield_factors · symbiosis
│   │   │                            #   facility_plan（设施方案）
│   │   ├── rice/                    #   growth_stages · nutrition_req
│   │   ├── worm/                    #   feeding_sop · products（产出利用）· site_plan
│   │   └── fertilizer/              #   processing（加工流程）· quality（检测标准）
│   ├── products/                    # 产品文档
│   │   └── web-qa-prd.md            #   Web问答系统需求文档
│   └── superpowers/specs/           # 设计文档
│       └── knowledge-base-restructure-design.md
├── assets/
│   ├── images/                      # Logo · 架构图 · 全景规划图
│   └── sources/                     # 原始 PDF 参考资料
└── scripts/                         # 数据处理与分析脚本（规划中）
```

## 参与方式

这是一个开放的探索性项目，欢迎对以下方向感兴趣的伙伴参与：

- 生态农业 / 循环农业实践经验
- AI Agent 应用开发
- 农业数据分析与建模

## 致谢

图片来源：[Unsplash](https://unsplash.com)（免费开源图片）
