# Home Credit 2024 文件级表目录

- 生成时间：2026-09-12T15:12:12
- 数据根目录：`<HOME_CREDIT_DATA_ROOT>`
- Parquet物理文件数：68
- 逻辑表结构数：17
- Parquet总大小：1.24 GB
- Train base行数：1,526,659
- 原始数据处理原则：只读

## 1. 审计结论

- 68个Parquet物理文件对应17种逻辑表结构，分片不能视为独立业务表。
- Train包含32个文件，Test包含36个文件。
- 所有同组分片的字段名一致；Train各表组内部schema一致。
- Test为10个case的无标签样例，部分全空字段存在物理类型变化，后续读取时需要显式统一类型。
- 候选键仅根据base/depth结构登记，本步骤未验证唯一性。
- 所有文件均包含其所属层级需要的候选键字段。

## 2. 逻辑表目录

| 表组 | Depth | Train文件数 | Test文件数 | Train物理行数 | Test物理行数 | Train字段数 | Test字段数 | 候选键 | 候选键字段完整 | Train Schema版本数 | Test Schema版本数 | 时间字段数 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| applprev | 1 | 2 | 3 | 6525979 | 30 | 41 | 41 | case_id + num_group1 | True | 1 | 2 | 7 |
| applprev | 2 | 1 | 1 | 14075487 | 10 | 6 | 6 | case_id + num_group1 + num_group2 | True | 1 | 1 | 0 |
| base | base | 1 | 1 | 1526659 | 10 | 5 | 4 | case_id | True | 1 | 1 | 3 |
| credit_bureau_a | 1 | 4 | 5 | 15940537 | 50 | 79 | 79 | case_id + num_group1 | True | 1 | 4 | 12 |
| credit_bureau_a | 2 | 11 | 12 | 188298452 | 120 | 19 | 19 | case_id + num_group1 + num_group2 | True | 1 | 1 | 0 |
| credit_bureau_b | 1 | 1 | 1 | 85791 | 10 | 45 | 45 | case_id + num_group1 | True | 1 | 1 | 3 |
| credit_bureau_b | 2 | 1 | 1 | 1286755 | 10 | 6 | 6 | case_id + num_group1 + num_group2 | True | 1 | 1 | 1 |
| debitcard | 1 | 1 | 1 | 157302 | 10 | 6 | 6 | case_id + num_group1 | True | 1 | 1 | 1 |
| deposit | 1 | 1 | 1 | 145086 | 10 | 5 | 5 | case_id + num_group1 | True | 1 | 1 | 2 |
| other | 1 | 1 | 1 | 51109 | 10 | 7 | 7 | case_id + num_group1 | True | 1 | 1 | 0 |
| person | 1 | 1 | 1 | 2973991 | 10 | 37 | 37 | case_id + num_group1 | True | 1 | 1 | 3 |
| person | 2 | 1 | 1 | 1643410 | 10 | 11 | 11 | case_id + num_group1 + num_group2 | True | 1 | 1 | 1 |
| static | 0 | 2 | 3 | 1526659 | 30 | 168 | 168 | case_id | True | 1 | 3 | 15 |
| static_cb | 0 | 1 | 1 | 1500476 | 10 | 53 | 53 | case_id | True | 1 | 1 | 9 |
| tax_registry_a | 1 | 1 | 1 | 3275770 | 10 | 5 | 5 | case_id + num_group1 | True | 1 | 1 | 1 |
| tax_registry_b | 1 | 1 | 1 | 1107933 | 10 | 5 | 5 | case_id + num_group1 | True | 1 | 1 | 1 |
| tax_registry_c | 1 | 1 | 1 | 3343800 | 0 | 5 | 5 | case_id + num_group1 | True | 1 | 1 | 1 |

## 3. 时间字段目录

- `applprev__depth_1`：`approvaldate_319D`、`creationdate_885D`、`dateactivated_425D`、`dtlastpmt_581D`、`dtlastpmtallstes_3545839D`、`employedfrom_700D`、`firstnonzeroinstldate_307D`
- `applprev__depth_2`：未识别到时间字段
- `base__depth_base`：`MONTH`、`WEEK_NUM`、`date_decision`
- `credit_bureau_a__depth_1`：`dateofcredend_289D`、`dateofcredend_353D`、`dateofcredstart_181D`、`dateofcredstart_739D`、`dateofrealrepmt_138D`、`lastupdate_1112D`、`lastupdate_388D`、`numberofoverdueinstlmaxdat_148D`、`numberofoverdueinstlmaxdat_641D`、`overdueamountmax2date_1002D`、`overdueamountmax2date_1142D`、`refreshdate_3813885D`
- `credit_bureau_a__depth_2`：未识别到时间字段
- `credit_bureau_b__depth_1`：`contractdate_551D`、`contractmaturitydate_151D`、`lastupdate_260D`
- `credit_bureau_b__depth_2`：`pmts_date_1107D`
- `debitcard__depth_1`：`openingdate_857D`
- `deposit__depth_1`：`contractenddate_991D`、`openingdate_313D`
- `other__depth_1`：未识别到时间字段
- `person__depth_1`：`birth_259D`、`birthdate_87D`、`empl_employedfrom_271D`
- `person__depth_2`：`empls_employedfrom_796D`
- `static__depth_0`：`datefirstoffer_1144D`、`datelastinstal40dpd_247D`、`datelastunpaid_3546854D`、`dtlastpmtallstes_4499206D`、`firstclxcampaign_1125D`、`firstdatedue_489D`、`lastactivateddate_801D`、`lastapplicationdate_877D`、`lastapprdate_640D`、`lastdelinqdate_224D`、`lastrejectdate_50D`、`lastrepayingdate_696D`、`maxdpdinstldate_3546855D`、`payvacationpostpone_4187118D`、`validfrom_1069D`
- `static_cb__depth_0`：`assignmentdate_238D`、`assignmentdate_4527235D`、`assignmentdate_4955616D`、`birthdate_574D`、`dateofbirth_337D`、`dateofbirth_342D`、`responsedate_1012D`、`responsedate_4527233D`、`responsedate_4917613D`
- `tax_registry_a__depth_1`：`recorddate_4527225D`
- `tax_registry_b__depth_1`：`deductiondate_4917603D`
- `tax_registry_c__depth_1`：`processingdate_168D`

## 4. 字段定义文件

- `feature_definitions.csv`包含465条字段定义。
- 字段列为`Variable`和`Description`。
- 两列均无缺失值。

## 5. 本步骤边界

- 未检查`case_id`唯一性和重复客户；
- 未检查时间范围和周度样本；
- 未统计target-event rate；
- 未设计Train、Validation和Final OOT；
- 未拼表、清洗、构造特征或训练模型。

以上内容留待Step 2及后续步骤。