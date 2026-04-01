# skill-yield-predict

## 功能
根据历史温湿度曲线和基质状态，预测本季松茸产量。

## 输入
```json
{
  "temp_curve_30d": [18, 19, 17, ...],
  "humidity_curve_30d": [88, 90, 85, ...],
  "substrate_weight_kg": 500,
  "substrate_cn_ratio": 28,
  "cultivation_area_m2": 100,
  "mushroom_stage": "原基期"
}
```

## 执行逻辑
1. 加载 `docs/distilled/matsutake/yield_factors.md`
2. 评估关键因子得分：
   - **温差刺激分**: 统计 30 天内昼夜温差 ≥8°C 的天数
   - **湿度稳定分**: 出菇期湿度 85-95% 的达标率
   - **基质质量分**: C/N 比是否在 25-30 范围内
3. 综合打分 → 映射到产量区间

## 输出
```
【产量预测】
  温差刺激: 22/30 天达标 → 73% (良好)
  湿度稳定: 26/30 天达标 → 87% (优秀)
  基质质量: C/N=28 → ✅ 适宜

  预测产量: 0.8-1.2 kg/m²
  总面积 100m² → 预计本季产出 80-120 kg
  预计菌糠废料: 300-350 kg

【置信度】中 (知识库数据待补充完整文献支撑)
【数据来源】docs/distilled/matsutake/yield_factors.md
```
