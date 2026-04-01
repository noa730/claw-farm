# skill-symbiosis-calc

## 功能
计算松茸与宿主树木的共生养分交换率，以及在循环链条中的菌糠产出与下游衔接。

## 输入
```json
{
  "host_tree_species": "赤松",
  "tree_age_years": 15,
  "mycorrhiza_coverage_pct": 60,
  "substrate_input_kg": 500,
  "cultivation_area_m2": 100
}
```

## 执行逻辑
1. 加载 `docs/distilled/matsutake/symbiosis.md`
2. 计算共生指标：
   - 菌根覆盖率 → 共生健康度评分
   - 养分交换效率估算
3. 计算循环链衔接：
   - 基质投入 → 菌糠产出（60-70% 转化率）
   - 菌糠 C/N 比 → 是否适合蚯蚓直接消化

## 输出
```
【共生分析】
  宿主: 赤松 (15年生)
  菌根覆盖率: 60% → 共生健康度: 良好
  养分交换: 树木供碳 ↔ 菌丝供磷钾

【循环链衔接】
  基质投入: 500 kg
  菌糠产出: 300-350 kg (转化率 60-70%)
  菌糠 C/N 比: 25-30:1 → ✅ 可直接投喂蚯蚓
  可支撑蚯蚓养殖面积: 约 30-50 m²

【下游建议】
  → worm-expert: 建议投喂密度 X kg/m²·日
【数据来源】docs/distilled/matsutake/symbiosis.md
```
