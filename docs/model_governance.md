---
status: template
methodology_version: v3.0
project: credit-risk-modeling-and-stability
---

# 信贷风控建模｜模型治理、验证与风险缓释模板

> 本页比例化借鉴 PRA SS1/23。它是公开数据学生项目的治理演示，不代表英国监管合规，也不能替代机构内部独立验证。

## 1. 模型台账（Model Inventory）

每个模型一行，至少记录：

| 字段 | 内容 |
|---|---|
| model_id / version | 唯一模型版本 |
| purpose / intended use | 设计目的与允许用途 |
| prohibited use | 禁止或未验证用途 |
| owner / developer / user | 本项目均由作者承担，需披露角色重合 |
| validation status | 未验证 / 有条件通过 / 通过 / 限制使用 / 停用 |
| data / feature / split version | 输入版本与时间范围 |
| algorithm / parameters | 算法与参数 |
| calibration / policy version | 概率映射与策略版本 |
| operating boundaries | 时间、客群、字段、缺失率和性能范围 |
| assumptions / limitations | 假设与已知限制 |
| validation findings | 发现、严重程度、责任和截止时间 |
| monitoring thresholds | 预警与行动阈值 |
| code commit | 可复现提交 |
| created / last validated | 创建和最近验证日期 |

建议后续生成：`artifacts/model_inventory.csv`。

## 2. 模型风险分级

本项目只使用简单比例化分级，不模拟银行企业级 tiering。

| 维度 | 低 | 中 | 高 |
|---|---|---|---|
| 重要性 | 仅教学诊断 | 影响最终模型选择 | 影响策略回放与对外结论 |
| 复杂度 | Logistic/Scorecard | RF/常规聚合 | LightGBM + 多表高维 + 校准 |
| 数据风险 | 单表、口径明确 | 多表或匿名字段 | PIT 不确定、来源复杂、分布显著漂移 |
| 可解释性 | 系数/分箱直接解释 | 需特征重要性 | 需 SHAP、敏感性与局限说明 |

模型风险等级越高，验证范围、证据和复核强度越高。

## 3. Operating Boundaries

冻结候选时填写：

- 适用 `WEEK_NUM` / `date_decision` 范围；
- 适用样本与产品范围；
- 必需字段及可接受缺失率；
- 重要特征训练期取值/类别范围；
- 允许的 Score PSI/UPI 观察范围；
- 最低可接受 AUC/KS/Calibration 表现；
- 单周最小样本和事件数；
- 未覆盖的经济、政策和客群情景。

超出边界不是“模型一定失效”的证明，但必须触发调查、限制解释或升级处理。

## 4. 独立式验证清单

由于作者同时是开发者，本项目只能称“independent-style validation”，不能声称组织独立。验证材料与开发 Notebook 分开保存，并在候选冻结后执行。

### 概念与用途

- [ ] 预测目的、标签、时间锚点和用途一致；
- [ ] 模型方法与公开研究或行业实践相符；
- [ ] 复杂度相对增量价值合理；
- [ ] 已知限制与禁止用途已写明。

### 数据与实现

- [ ] 主键、粒度、JOIN 行数和重复样本通过；
- [ ] 所有预处理只从 Train 学习；
- [ ] PIT 和数据来源可追溯；
- [ ] 代码、参数、随机种子与环境可复现；
- [ ] 对关键聚合和预测结果做独立重算抽查。

### 性能与稳定性

- [ ] Scorecard、RF、LightGBM 同口径比较；
- [ ] 排序、概率、阈值指标分别报告；
- [ ] 周度样本数、事件数、趋势和区间估计完整；
- [ ] 不平衡方案只作用于 Train，概率重新校准；
- [ ] SHAP 只解释模型行为并检查相关特征分摊；
- [ ] 敏感性、替代模型和关键假设测试完成。

### 发现分级

- Critical：泄露、标签错误、主键错误、OOT 污染；不得进入 Final OOT。
- Major：结果不可复现、校准失效、关键特征不可用；整改后复核。
- Moderate：指标波动、解释不足、文档缺口；可有条件通过。
- Minor：格式、命名和非关键补充；记录修正。

建议输出：`reports/04_independent_style_validation.md`。

## 5. 监控包

### 即时监控（无需成熟标签）

- schema、行数、重复、缺失、新类别；
- 特征和分数分布；
- 固定训练分箱下的 PSI；
- UPI 仅作为空 bin/新类别敏感性分析；
- 数据处理耗时和失败率。

### 延迟监控（标签成熟后）

- target-event rate；
- AUC、KS、PR-AUC、Lift；
- Brier、Log Loss、校准曲线；
- Risk Band 单调性；
- 策略通过率、复核率、拒绝率和捕获率。

阈值必须根据开发期波动、样本量和业务影响校准；不得机械套用 PSI 0.1/0.25 或统一 UPI cutoff。

## 6. 风险缓释与行动矩阵

| 触发 | 初始行动 | 后续选择 |
|---|---|---|
| 数据字段缺失/口径改变 | 停止使用受影响特征，核查上游 | 修复数据、回退模型或限制使用 |
| 漂移升高但性能未知 | 加密监控并调查来源 | 等标签成熟后决定校准/重建 |
| 校准恶化、排序稳定 | 暂停概率型策略解释 | 重新校准并独立验证 |
| AUC/KS 持续跌破容忍线 | 限制模型用途 | 回退 champion、重训或重建 |
| 重大泄露/实现错误 | 停用该版本 | 修复、全链路重跑并重新冻结 |
| 人工覆盖/PMA 反复出现 | 报告调整前后结果 | 根因分析；判断是否模型设计错误 |

## 7. Post-model adjustment（PMA）规则

任何对模型输入、假设、概率或策略输出的人工调整必须记录：

- 为什么需要；
- 计算方法与负责人；
- 调整前后结果；
- 适用范围；
- 独立式复核结果；
- 何时减少或取消；
- 长期重复是否意味着需要重新校准或重建。

## 8. 变更控制

每次重大变化建立一条 decision record：

```md
# ADR-XXX｜决策标题
触发证据：
影响模型/数据/策略：
旧决策：
新决策：
验证要求：
是否影响 Final OOT 身份：
批准日期：
commit：
```
