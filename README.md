# oss-github-dwh

[`oss-github-data`](../oss-github-data) に蓄積された GitHub の raw データ（Parquet）をもとに、
dbt + DuckDB でデータモデリングを行う演習用リポジトリ。

## セットアップ

2 つのリポジトリを同じ親ディレクトリに clone する。

```
work/
├── oss-github-data/   # raw（Actions が毎日更新）
└── oss-github-dwh/    # ← ここ
```

```sh
uv sync
cd ../oss-github-data && git pull && cd -   # 最新の raw を取得
uv run dbt debug
uv run dbt build
```

- raw の場所は `RAW_DATA_PATH`（既定 `../oss-github-data/data/raw`）
- `warehouse.duckdb` は raw から毎回再構築できるため Git 管理しない
- 中身の確認: `uv run dbt show --inline "select * from {{ source('github_raw', 'issues') }} limit 10"`
  または `duckdb warehouse.duckdb`（DuckDB CLI）

## 構成

| パス | 用途 |
|---|---|
| `models/sources.yml` | raw の source 定義（提供済み） |
| `models/staging/` `intermediate/` `marts/` | 演習でモデルを追加する場所 |
| `docs/adr/` | モデリング上の設計判断の記録 |
| `.github/workflows/ci.yml` | PR / push / 毎日 で `dbt build` |

raw の仕様（テーブル、取得方法、既知の制約）は `oss-github-data/docs/adr/` を参照。
