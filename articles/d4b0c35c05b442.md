---
title: "Pythonについての自分用メモ"
emoji: "⚙️"
type: "tech"
topics:
  - "python"
published: true
published_at: "2024-09-02 23:26"
---

# はじめに

Pythonに関するメモ
自分用・随時更新

こっちの方がいいかも: https://zenn.dev/tabayashi/articles/52389e0d6c353a

# 仮想環境

uvを使用

インストール: https://docs.astral.sh/uv/getting-started/installation/#installation-methods

## Pythonのバージョン管理
- `uv python install`: Pythonをバージョンを指定してインストール
- `uv python list`: 利用可能なPythonのバージョンを表示
- `uv python find`: インストールしたPythonのバージョンを見つける
- `uv python pin`: 今のプロジェクトで使用するPythonのバージョンを固定
- `uv python uninstall`: Pythonのバージョンを指定してアンインストール

## スクリプトを実行
`example.py` など
- `uv run`: スクリプトを実行
- `uv add --script`: スクリプトへの依存関係を追加
- `uv remove --script`: スクリプトの依存関係を削除

## プロジェクト
`pyproject.toml` など
- `uv init`: Pythonプロジェクトを作成
- `uv add`: プロジェクトに依存関係を追加
    - `uv add -r requirements.txt`: `requirements.txt`からインストール
    - `uv add <package> --dev`: 開発用のパッケージを追加
    - GitやURL、local wheelのパスなどをインストールすることも可能
- `uv remove`: プロジェクトの依存関係を削除
- `uv sync`: 環境とプロジェクトの依存関係を同期
- `uv lock`: プロジェクトの依存関係をlockfileに作成
- `uv run`: プロジェクト環境でコマンドを実行
- `uv tree`: プロジェクトの依存関係の木を表示

## ツール
ruffやblackなど
- `uvx / uv tool run`: 一時的な環境でツールを実行
- `uv tool install`: ツールをインストール
- `uv tool uninstall`: ツールをアンインストール
- `uv tool list`: インストールしたツールを表示
- `uv tool update-shell`: 実行可能なツールを含むようにシェルを更新（？）
- 以下のように指定するとモジュールをimportできる
    ```
    [tool.hatch.build.targets.wheel]
    packages = ["src/my_project", "src"]
    ```
## ユーティリティ
- `uv cache clean`: キャッシュを削除
- `uv cache prune`: 期限切れのキャッシュを削除
- `uv cache dir`: キャッシュのディレクトリのパスを表示
- `uv tool dir`: uvのツールのディレクトリのパスを表示
- `uv python dir`: インストールされたバージョンのPythonのディレクトリのパスを表示
- `uv self update`: uvを最新版に更新

## PyTorch関連
https://docs.astral.sh/uv/guides/integration/pytorch/

## 大体の流れ
```bash
uv init my-project -p <version>  # バージョン指定は任意
cd my-project

uv sync  # pyproject.tomlをもとにパッケージをインストール
. .venv/bin/activate  # 仮想環境の有効化
uv python pin <version>  # Pythonのバージョンを変更
uv add <package>  # パッケージを追加
uv run main.py  # 仮想環境で実行
```

# VSCode 拡張機能

- autoDocstring
- Black Formatter
- Flake8
- isort
- Jupyter
- Pylance
- Python
- Python debugger

# Jupyter

## 最初

- (poetry init したあと)`uv add ipykernel`
- コマンドパレットから`Jupyter: Select Interpreter to Start Jupyter Server`

## ipynb -> py

```bash
jupyter nbconvert --to python XXX.ipynb
```

# API キー

1. ターミナルで以下を実行: `echo "export API_KEY='yourkey'" >> ~/.bashrc` (`API_KEY`や`yourkey`は変更する)
2. シェルを更新: `source ~/.bashrc`
3. 確認: `echo $API_KEY`

これで以下のようにするとキーを読み込める

```python
import os
import openai

openai.api_key = os.environ["API_KEY"]
```

# Ruff
## インストール
```bash
uv add --dev ruff
```
## 設定
```yaml:pyproject.toml
...

[tool.ruff]
line-length = 79
target-version = "py312"

[tool.ruff.format]
docstring-code-format = true
indent-style = "space"

[tool.ruff.lint]
extend-select = []
ignore = []
```
