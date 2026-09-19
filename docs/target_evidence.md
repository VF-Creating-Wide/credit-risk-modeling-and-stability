# Target Evidence｜标签证据边界

> 只记录可核验事实、未知项和禁止表述。任何关于 target 的新说法先更新本页，再修改 Charter、README 或简历。

## 1. 当前已核验

| 事实 | 状态 | 证据 |
|---|---|---|
| `train_base` 包含二元 `target` | 已确认 | Kaggle 数据说明；`notebooks/02_base_time_target_audit.ipynb` 实际审计 |
| `case_id` 是连接各表的唯一 case/loan 标识 | 已确认 | Kaggle 数据说明 |
| `date_decision` 是决策日期 | 已确认 | Kaggle 数据说明 |
| `WEEK_NUM` 用于时间聚合且 test 延续 train 周序号 | 已确认 | Kaggle 数据/evaluation |

## 2. 当前未核验

- target=1 对应的准确业务事件；
- 是否可称 default / payment difficulty；
- observation/performance window、DPD 阈值与标签成熟规则；
- 样本总体是否只包含已批准/已放款客户；
- 是否可把模型输出解释为某期限 PD。

## 3. 当前统一术语

| 避免使用 | 当前使用 |
|---|---|
| 坏客户/违约客户（针对本数据） | target=1 样本 |
| 坏率/违约率 | target rate |
| PD | target-event probability |
| 真实审批效果 | 离线策略回放结果 |
| 真实 Expected Loss | 条件式 illustrative scenario |

## 4. 禁止迁移的旧赛题口径

旧版 **Home Credit Default Risk** 的 `TARGET/payment difficulty` 说明不能作为 2024 **Credit Risk Model Stability** 的标签证据。两套比赛的数据表、主键和任务结构不同。

## 5. 后续核验流程

1. 保存 Kaggle data、overview、evaluation 与主办方讨论链接；
2. 下载后记录 `train_base` schema 和 target 取值；
3. 搜索主办方对 target、样本总体和时间窗的原始说明；
4. 对新增解释记录原文、URL、访问日期和证据强度；
5. 若仍无法核验，继续保留“未知”，不靠二手 notebook 补定义。

## 6. 一手入口

- [Kaggle Data](https://www.kaggle.com/competitions/home-credit-credit-risk-model-stability/data)
- [Kaggle Evaluation](https://www.kaggle.com/competitions/home-credit-credit-risk-model-stability/overview/evaluation)
- [Kaggle Discussion：decision date 与数据可用性](https://www.kaggle.com/competitions/home-credit-credit-risk-model-stability/discussion/472866)
