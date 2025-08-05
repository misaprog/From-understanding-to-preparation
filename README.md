# From-understanding-to-preparation

# データサイエンス方法論ラボ：理解から準備へ

## 🎯 目標

このラボを完了すると、以下ができるようになります：

- データを理解する  
- 分析や推論に向けてデータを準備する

---

## 🧭 はじめに

このラボでは、データサイエンス方法論の学習を継続し、特に以下の2つのステージに焦点を当てます：

- データ理解（Data Understanding）  
- データ準備（Data Preparation）

---

## 📑 目次

- Recap（前回のおさらい）
- データ理解（Data Understanding）
- データ準備（Data Preparation）

---

## 🔁 Recap（前回のおさらい）

前回のラボ「From Requirements to Collection（要件から収集へ）」では、**ビジネス理解ステージ**で導き出された問い：

> 「与えられたレシピの料理の種類（cuisine）を自動的に判別することは可能か？」

に対するデータがすでに存在することを確認しました。

研究者の **Yong-Yeol Ahn** 氏は、次の3つの異なるウェブサイトから、**数万件の料理レシピ（料理の種類と材料）**をスクレイピングしています：

- （以下、元のウェブサイト名やデータの内容が続く予定）

---

※このドキュメントはIBM Data Science Professional Certificate コースのラボ教材を参考に作成されています。

## 🧪 データの出典と補足情報

より詳しく **Yong-Yeol Ahn 氏と彼の研究**について知りたい場合は、彼の論文  
「Flavor Network and the Principles of Food Pairing（フレーバーネットワークと食材の組み合わせ原理）」を読むことをおすすめします。

このラボで使用するデータは、すでにIBMのサーバーにアップロードされており、すぐに利用できるようになっています。


## 🔍 研究の参考情報とデータの場所

Yong-Yeol Ahn 氏とその研究について詳しく知りたい方は、  
彼の論文「**Flavor Network and the Principles of Food Pairing**（フレーバーネットワークと食材の組み合わせの原則）」を読むことをおすすめします。

この研究では、世界中のさまざまな料理における食材の組み合わせパターンを分析しています。

📄 [論文はこちら（Nature誌）](https://www.nature.com/articles/srep00196)

また、私たちはこのラボで使用するデータをあらかじめ収集し、皆さんがすぐにアクセスできるように **IBMのサーバーに配置**しています。

> ⚠️ **重要な注意事項**

Python のプログラミング方法を理解している必要は **ありません**。  
以下に示すコードは、**データ収集のステージを説明するための例**として使用されており、コードの各行の意味がわからなくても問題ありません。

この認定プログラムを修了することを選択すれば、  
後半で **「Python for Data Science」** という完全な Python プログラミングコースを受講でき、  
Python の使い方を学ぶことができます。

---

## Python バージョンの確認（Jupyter Notebook用）

Jupyter Notebook 上で Python のバージョンを確認するには、以下のようにします。

```python
# ローカル環境で実行している場合に、Pythonのバージョンを表示するコマンド
!python -V

```
---

解説
!（ビックリマーク）は、Jupyter Notebookでシェルコマンド（端末コマンド）を実行するための記号です。

python -V は、Pythonのバージョン情報を表示する標準的なコマンドです。

つまり、このセルを実行すると、インストールされているPythonのバージョンが表示されます。

ローカル環境だけでなく、クラウド上のJupyter環境でも使えます。

---

Pythonバージョンを確認する他の方法として、Pythonコードで直接取得することもできます：

```

import sys
print(sys.version)

```

---

## 必要なライブラリのインポート

このプロジェクトでは、以下の Python ライブラリを使用します。

```python
import pandas as pd  # pandas: データ解析・操作用のライブラリ（DataFrameなどを使う）
pd.set_option('display.max_columns', None)  # 表示される列数の上限を解除（すべての列を表示）

import numpy as np  # numpy: 数値計算ライブラリ（配列処理、数学関数などに利用）

import re  # re: 正規表現を使った文字列検索や置換などに使用

```

補足
pandas は表形式のデータ（CSVなど）の読み込み・加工に使います。

numpy は数値計算を効率的に行うためのライブラリで、機械学習や統計処理にも必須です。

re は、データの中の特定パターンを抽出・加工するための文字列操作ツールです。

pd.set_option('display.max_columns', None) は、Notebook上で列が多いデータでもすべて表示できるようにする設定です。

---

## データの読み込み1

IBMのサーバーからレシピデータをダウンロードし、`pandas` のデータフレームに読み込みます。  
この処理によって、後の分析や前処理がしやすくなります。

---
## データの読み込み2

クラウド環境（例：IBM Skills Network Labs や Google Colab）で実行する場合、以下のコードを使用して IBM のサーバーからレシピデータをダウンロードし、pandasのデータフレームに読み込みます。

```python
recipes = pd.read_csv("https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DS0103EN-SkillsNetwork/labs/Module%202/recipes.csv")

print("Data read into dataframe!")  # 読み込みには約30秒かかります
```
ローカル環境で実行する場合は、あらかじめファイルをダウンロードして同じフォルダに置き、コメントアウトを外して実行してください。

```

# recipes = pd.read_csv("recipes.csv")

```
## データの読み込み（ローカル環境 / 通常のJupyter Notebook向け）

以下のコードは、IBMのクラウドストレージからCSVデータを `pandas` で直接取得し、データフレームに読み込む方法です。

```python
import pandas as pd

url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DS0103EN-SkillsNetwork/labs/Module%202/recipes.csv"

recipes = pd.read_csv(url)

print("Data read into dataframe!")
```
| チェック項目                    | 状況            |
| ------------------------- | ------------- |
| データの読み込み（`read_csv`）      | ✅ 成功          |
| DataFrame の先頭表示（`head()`） | ✅ 確認済み        |
| データの構造確認（`shape`）         | ✅ 行数・列数が確認できた |
| 列名確認（`columns`）           | ✅ 確認済み        |
---
#### 私たちのデータセットは57,691件のレシピで構成されています。各行はレシピを表し、各レシピには対応する料理名と、アーモンドからズッキーニまで384種類の材料がレシピに含まれているかどうかが記録されています。
---
##### 基本的な寿司レシピには、以下の材料が含まれていることはご存知でしょう。

米
醤油
わさび
魚介類/野菜

---
### これらの材料がデータフレームに存在するかどうか確認してみましょう
---
