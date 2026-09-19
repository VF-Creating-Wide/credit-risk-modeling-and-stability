# 方法路线图

本路线图用于约束项目顺序，避免在数据与时间边界尚未冻结时提前优化模型。

## Phase A：定义与数据基础

1. 冻结项目章程、target 术语和证据边界。
2. 建立文件级数据清单与表级目录。
3. 审计基础表、主键、时间字段和标签分布。
4. 冻结开发、时间验证、校准和 Final OOT 区间。
5. 构建 point-in-time correct 的 case-level Feature Mart，并记录 JOIN 行数与字段来源。

## Phase B：可复现基准

1. 建立简单规则与 Dummy baseline。
2. 构建 Logistic Regression / Scorecard 基准，包括分箱、WOE、IV 与系数解释。
3. 在统一切分和指标下构建 Random Forest 与 LightGBM 基准。

## Phase C：特征与模型增量

1. 按表组逐批加入特征，保留增量实验记录。
2. 审计缺失、异常值、潜在泄露与时间可用性。
3. 仅在 Temporal Validation 上进行特征和参数选择。
4. 比较区分能力、概率质量、解释性、训练成本与稳定性。

## Phase D：冻结与最终验证

1. 在独立区间完成概率校准与阈值/策略设计。
2. 冻结数据、特征、模型、校准和策略版本。
3. 只打开一次 Final OOT，报告整体与周度表现、不确定性和限制。

## Phase E：策略与生命周期

1. 执行 retrospective / offline policy replay。
2. 建立 PSI、缺失率、分数、校准与分群表现监控。
3. 定义预警、限制使用、重新校准、重训和停用条件。
4. 以模型台账和 ADR 记录关键变更。
