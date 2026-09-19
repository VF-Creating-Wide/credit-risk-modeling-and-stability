---
status: frozen
methodology_version: v3.0
freeze_date: 2026-09-11
project: credit-risk-modeling-and-stability
---

# Project Charter｜信贷风控建模（冻结版 v3.0）

## 项目名称

**信贷风控建模：信用评分卡、机器学习与模型稳定性分析**

## 1. 背景与核心问题

本项目基于 **Home Credit - Credit Risk Model Stability (2024)** 公开数据，以 `date_decision` 为时间锚点，对每个 `case_id` 的二元 `target` 事件进行风险排序与概率估计。

核心问题是：如何在约 26GB、多表、depth=0/1/2 的数据结构中构造时间正确且可复现的 case-level 特征，并检验模型在更晚时间段的区分能力、概率质量和稳定性？

## 2. 预测设计与证据边界

| 项目 | 冻结定义 | 证据边界 |
|---|---|---|
| 预测单位 | 一个 `case_id` 对应的一笔 credit case/loan record | 以官方数据和实际主键审计为准 |
| 时间锚点 | `date_decision` | 所有特征必须在该时点可获得 |
| 标签 | 公开训练数据中的二元 `target` | 仅称 target event |
| 模型输出 | 风险分数；校准后可称 target-event probability | 不自动称为 PD |
| 开发验证 | 时间更晚的 Temporal Validation | 用于特征、模型和参数选择 |
| 校准与策略 | 独立 Calibration/Policy Validation；样本不足时采用时间顺序 OOF 并披露 | 不接触 Final OOT |
| 最终验证 | 最新且有标签、候选冻结后只打开一次的 Final OOT | Kaggle hidden test 无本地标签 |

不把 `target` 擅自定义为 12M PD、90+ DPD、旧版 Home Credit Default Risk 的 payment difficulty，或真实机构的坏账/资本计量口径；不声称完成真实线上审批、A/B Test、利润提升、监管认证或因果识别。

## 3. 三个评价维度与决策门

三个维度都必须报告，但不做“各占 1/3”的伪精确加权。它们单位不同且存在依赖关系，因此采用决策门和权衡记录：

| 决策门 | 必须回答的问题 | 主要证据 |
|---|---|---|
| A｜区分与稳定 | 模型能否在未来时间段保持风险排序能力？ | AUC、KS、PR-AUC、Lift、weekly Gini、区间估计 |
| B｜概率质量 | 风险概率是否与实际 target-event rate 相符？ | Log Loss、Brier、校准曲线、分组实际事件率 |
| C｜策略可用 | 在明确阈值下，风险与覆盖率如何权衡？ | approval/review/reject rate、target-event capture、组合事件率、Risk Band 单调性 |

任何模型若存在时间泄露、不可复现或重大 OOT 失效，不因单项 AUC 较高而入选。模型间无法完全支配时，记录透明的 Pareto 权衡，不制造一个任意综合分数。

## 4. 模型与实验范围

冻结的核心比较为：

1. Logistic/WOE Scorecard：低复杂度、可解释基准；
2. Random Forest：固定参数的非 Boosting 参考模型，不进行大规模调参；
3. LightGBM：主要 challenger。

类别不平衡处理按消融实验比较：

- 原始训练分布；
- class weight；
- 明确策略阈值；
- 仅训练集内的 SMOTE（可选对照）。

Validation、Calibration 和 Final OOT 不做过采样。任何改变样本先验的方法都必须重新检查概率校准。SHAP 仅解释模型如何使用特征，不证明业务因果。

## 5. Intended use、Operating boundaries 与限制

允许用途：

- 公开数据上的离线风险排序研究；
- target-event probability 校准研究；
- 风险分层与阈值策略回放；
- 模型稳定性、漂移与治理演示。

禁止或未验证用途：

- 直接用于真实客户审批；
- 资本计量、IFRS 9 ECL 或监管 PD；
- 真实利润、真实公平性或真实线上效果声明；
- 超出训练数据时间、客群、字段可用范围的外推。

运行边界将在数据审计后补充：适用周次、样本范围、特征取值范围、缺失率范围、模型性能容忍区间和已知不适用场景。

## 6. 模型风险管理的比例化实现

借鉴 PRA SS1/23，但本项目不声称满足英国监管要求。学生项目采用以下比例化控制：

- 建立模型台账，记录目的、版本、数据、假设、限制、验证和状态；
- 开发、校准、策略和 Final OOT 之间保留边界；
- 候选模型冻结后执行一次“独立式验证”；由于项目为单人开发，不能宣称组织独立；
- 预先定义性能阈值、升级路径、限制使用、重新校准和重建条件；
- 任何人工调整或 post-model adjustment 同时报告调整前后结果、理由和退出条件；
- 版本变更必须写入决策记录。

## 7. 成功标准

- Feature Mart 一行一个 `case_id`，主键和 JOIN 行数可审计；
- 重要特征通过 point-in-time、来源、缺失和漂移审计；
- 增量实验只使用 Temporal Validation；
- 三个模型在同一数据、切分和指标口径下比较；
- 不平衡处理具有训练集内消融证据；
- 概率映射只在独立校准数据或合规替代方案上学习；
- 候选模型、代码和监控标准在打开 Final OOT 前冻结；
- Final OOT 只用于一次最终评价，并报告周度不确定性；
- 结论可回溯至 data/feature/model/calibration/policy version 与 commit；
- 策略结果明确标注为 retrospective/offline simulation；
- 结论不超过公开数据证据。

## 8. 冻结与变更控制

本 Charter 自 2026-09-11 起冻结为 v3.0。新增论文、新算法或微小指标提升不构成改版理由。仅在下列情况出现时允许变更：

1. 官方数据证据推翻 target、主键、时间或字段可用性假设；
2. 发现数据泄露、评价错误、代码错误或不可复现；
3. Final OOT 或监控证据表明模型超出运行边界；
4. 资源限制使既定步骤无法执行；
5. 出现影响结论的重大监管、伦理或安全问题。

变更必须记录：触发证据、影响范围、旧决策、新决策、批准日期和 commit。若 Final OOT 已打开，后续开发不得继续称原区间为“未触碰 Final OOT”。

## 9. 当前阶段

Phase A 正在执行。Step 0 项目章程、Step 1 文件级审计和 Step 2 基础表/时间/标签审计已形成可复现证据；下一步冻结时间切分与 Final OOT。
