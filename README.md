# 信贷风控建模：信用评分卡、机器学习与模型稳定性分析

**Credit Risk Modeling: Scorecard, Machine Learning and Model Stability**

> Work in progress. 本仓库以可复现、时间正确和证据边界清晰为原则，构建一个面向零售信贷场景的端到端风控建模项目。

## 项目目标

基于 Home Credit - Credit Risk Model Stability 公开数据，围绕 `case_id` 构建 case-level 特征，并在严格的时间切分下完成信用评分卡与机器学习模型的同口径比较、概率校准、OOT 验证、模型稳定性监控和离线策略回放。

核心关键词：**信贷风控、信用评分卡、Logistic Regression、WOE/IV、机器学习、LightGBM、时间验证、OOT、模型校准、PSI、模型稳定性、模型治理**。

## 当前进度

- [x] 项目章程、目标定义与证据边界
- [x] 约 26GB 多表 Parquet 的文件级审计
- [x] 基础表、时间字段和 target 审计
- [x] 冻结开发集、Temporal Validation、Calibration 与 Final OOT
- [ ] 构建 point-in-time correct Feature Mart
- [ ] 建立 Logistic / Scorecard、Random Forest、LightGBM 基准
- [ ] 完成校准、解释、稳定性与误差分析
- [ ] 完成模型冻结、Final OOT 与离线策略回放

## 方法概览

| 模块 | 设计 |
|---|---|
| 预测单位 | 每个 `case_id` 一笔 credit case / loan record |
| 时间锚点 | `date_decision` |
| 标签口径 | 公开训练数据中的二元 `target`；不擅自解释为特定期限 PD |
| 传统模型 | Logistic Regression / Scorecard，包含 WOE、IV、分箱与系数解释 |
| 机器学习 | Random Forest 与 LightGBM，使用同一数据切分和评价口径 |
| 验证设计 | Temporal Validation、独立校准区间、冻结后的 Final OOT |
| 评价指标 | AUC、Gini、KS、PR-AUC、Brier Score、校准曲线及周度稳定性 |
| 稳定性 | PSI、缺失率漂移、分数漂移、分群表现和时间退化 |
| 策略层 | 基于冻结模型的 retrospective / offline policy replay |

## 仓库结构

```text
.
├── data/          # 数据说明；原始数据不入库
├── docs/          # 项目章程、标签证据与模型治理
├── metadata/      # 文件清单和表级目录
├── notebooks/     # 可复现的数据审计与实验
├── reports/       # 后续生成的结论摘要和图表
├── sql/           # 后续的特征加工与监控查询
└── src/           # 后续从 Notebook 抽取的可复用代码
```

## 数据与复现

原始数据约 26GB，不提交到 Git。请按 [data/README.md](data/README.md) 配置环境变量 `HOME_CREDIT_DATA_ROOT`，然后从仓库根目录启动 Jupyter。当前审计 Notebook 主要依赖 Python 3.12、pandas、PyArrow 与 Matplotlib。

```bash
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter lab
```

## 重要边界

- 本项目是公开数据上的离线研究，不代表真实银行生产审批或监管认证。
- 在官方证据不足时，模型输出称为 `target-event probability`，不自动称为 PD。
- Kaggle hidden test 无本地标签；最终评价使用预先冻结、候选模型确定后只打开一次的有标签时间外样本。
- 所有尚未完成的模型与指标均标注为计划项，不预填结果。

更完整的方法与治理约束见 [项目章程](docs/project_charter.md)、[方法路线图](docs/methodology_roadmap.md) 和 [模型治理模板](docs/model_governance.md)。
