# Data

原始 Home Credit 数据约 26GB，不提交到 Git。Notebook 通过环境变量 `HOME_CREDIT_DATA_ROOT` 定位数据。

Windows PowerShell 示例：

```powershell
$env:HOME_CREDIT_DATA_ROOT = "D:\Home_Credit_datasets"
```

Linux / macOS 示例：

```bash
export HOME_CREDIT_DATA_ROOT=/path/to/Home_Credit_datasets
```

预期目录至少包含：

```text
<HOME_CREDIT_DATA_ROOT>/
├── feature_definitions.csv
└── parquet_files/
    ├── train/
    │   └── train_base.parquet
    └── test/
        └── test_base.parquet
```

完整文件结构见 `metadata/file_inventory.csv` 与 `metadata/table_catalog.md`。如果本地目录布局不同，请只修改环境变量或 Notebook 顶部的配置单元，不要把绝对路径提交到仓库。
