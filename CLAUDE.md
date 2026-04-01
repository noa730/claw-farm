# 循环农业项目 - 全局指令

## 核心理念
本项目围绕 **"稻-蚯蚓-有机肥" 循环农业模式**，用 AI Agent 辅助决策。

## 核心变量定义
| 变量 | 含义 | 单位 |
|------|------|------|
| `growth_stage` | 水稻当前生长阶段 | enum: 秧苗期/分蘖期/拔节期/抽穗期/灌浆期/成熟期 |
| `soil_ph` | 土壤酸碱度 | pH |
| `organic_matter` | 土壤有机质含量 | g/kg |
| `worm_density` | 蚯蚓投放密度 | 条/m² |
| `feed_ratio` | 蚯蚓日投喂量与体重比 | % |

## 知识库结构
- `docs/distilled/` — 经过转化的核心知识，Agent 优先读取
- `assets/sources/` — 原始 PDF 备份，仅在需要深入查证时引用
- `scripts/` — 数据处理与分析脚本

## 工作流约定
1. 回答农业问题时，先查 `docs/distilled/` 下对应主题的 md 文件
2. 若 distilled 文档不足以回答，再回溯 `assets/sources/` 中的原始 PDF
3. 所有施肥/投喂建议必须标注数据来源
