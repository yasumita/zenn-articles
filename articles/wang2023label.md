---
title: "Label Words are Anchors (EMNLP 2023)"
emoji: "⚓"
type: "idea"
topics:
  - "論文メモ"
published: true
published_at: "2026-08-25 12:05"
---

# [Label Words are Anchors: An Information Flow Perspective for Understanding In-Context Learning](https://aclanthology.org/2023.emnlp-main.609) (EMNLP 2023)
Authors: Lean Wang, Lei Li, Damai Dai, Deli Chen, Hao Zhou, Fandong Meng, Jie Zhou, Xu Sun

- 大規模言語モデル (LLM) の文脈内学習 (In-context learning) の新しい解釈可能性を検証
- ICL において、**ラベルの単語は情報を集約したり分散したりするアンカーの役割を果たす**
  1. 浅い層 (入力に近い層) では、ラベルの単語が例の情報を集約している
  2. 深い層 (出力に近い層) では、ラベルの単語から情報を取り出し最終的な予測を形成する![](https://static.zenn.studio/user-upload/762660acbe8b-20260825.png)
- 実験設定
  - モデル: GPT2-XL, GPT-J
  - タスク: 感情分析・質問タイプ分類・トピック分類・感情分類
  - ラベルの各種類につき 1 つずつ例をサンプリングしてプロンプトを作成![](https://static.zenn.studio/user-upload/c15a9631e968-20260825.png)
    - プロンプトは以下の 3 つから構成される
      1. ラベル($C$ 個)の単語それぞれの位置 $p_1,\ldots, p_C$
      2. ターゲットの位置 $q$ (入力の最後の単語)
      3. テキスト部分: ラベルの単語の前にある単語列
  - 単語間での attention の相互作用を調べるために、attention 行列に対する顕著性スコア行列を定める$$I_l = \left| \sum_h A_{h,l} \odot \frac{\partial \mathcal{L}(x)}{\partial A_{h,l}} \right|$$
    - $I_l(i,j)$: $l$層目における$j$番目の単語から$i$番目の単語への情報の流れの重要度
    - $A_{h,l}$: 第$l$層の$h$個目の head の attention 行列
      - Multi head なので attention 行列が h 個ある
      - Multi head は各特徴量をより小さい次元のベクトルへ分割し、それぞれの分割グループで Attention を適用して最後に結合する
    - $x$: 入力 (単語)
    - $\mathcal{L}(x)$: タスクの損失関数 (分類問題のクロスエントロピー誤差など)
  - 以下の 3 つそれぞれについての情報の流れの平均重要度を指標とする
    1. $S_{wp}$: テキスト部分からラベルの単語 (集約の強さ)$$\begin{align*}S_{wp} &= \frac{\sum_{(i,j) \in \mathcal{C}_{wp}} I_l(i,j)}{|\mathcal{C}_{wp}|}, \\\mathcal{C}_{wp} &= \{(p_k, j) : k \in [1, C], j < p_k\}.\end{align*}$$
    2. $S_{pq}$: ラベルの単語からターゲットの位置 (情報の抽出の強さ)$$\begin{align*}S_{pq} &= \frac{\sum_{(i,j) \in \mathcal{C}_{pq}} I_l(i,j)}{|\mathcal{C}_{pq}|}, \\\mathcal{C}_{pq} &= \{(q, p_k) : k \in [1, C]\}.\end{align*}$$
    3. $S_{ww}$: $S_{wp}$と$S_{pq}$での影響を除いた全単語間 (ベンチマーク)$$\begin{align*}S_{pq} &= \frac{\sum_{(i,j) \in \mathcal{C}_{pq}} I_l(i,j)}{|\mathcal{C}_{pq}|}, \\\mathcal{C}_{pq} &= \{(q, p_k) : k \in [1, C]\}.\end{align*}$$
  - 結果
    ![](https://static.zenn.studio/user-upload/43d1226b5944-20260825.png)
    1. $S_{wp}$: 浅い層で高く、深い層で低い → 浅い層で集約されている
    2. $S_{pq}$: 浅い層で低く、深い層で高い → 深い層で情報が抽出されている
    3. $S_{wp}$と$S_{pq}$がほとんどで$S_{ww}$を超えており、ラベルの単語が重要である
- 操作
  - ある層で、テキスト部分からラベルの単語への情報の集約の経路を断つ
    - 具体的には$A_l(p,i)\ (i<p)$を 0 とする
    - $A_l$: 第$l$層の attention 行列、$p$: ラベルの単語の位置、$i$: ラベルの前の単語の位置
  - 指標
    - Label Loyalty: 出力ラベルの一貫性を測る
    - Word Loyalty: 上位 5 つの予測単語を比較する Jaccard 係数
      - |A and B| / |A or B| で 0 から 1 を取る
    - 低いほど、操作が予測に与える影響が大きい
  - 3 種類の経路の断ち方: First(最初の 5 層で断つ)、Last(最後の 5 層で断つ)、Random(ラベル以外の単語をランダムに断つ)
  - 結果![](https://static.zenn.studio/user-upload/fba0a9ae5b80-20260825.png)
    - 最初の 5 層で経路を断ったとき、モデルの振る舞いが大きく変わった<!--水色-->→**浅い層が情報の集約において重要**
- モデルの最終的な予測と、ターゲットの位置のラベルの単語についての attention の分布 $(A_l(q,p_1),\ldots,A_l(q,p_C))$ の相関を調べる
  - $A_l$: 第$l$層の attention 行列の和
  - $q$: ターゲットの位置
  - $p_1,\ldots,p_C$: ラベルの単語の位置
  - ここでは$(A_l(q,p_1),\ldots,A_l(q,p_C))$を分類の予測として、実際の出力との AUC-ROC を計算する
    - 一対他にしている（多分）(ラベルの割合のバランスが悪いかもしれないため)
    - AUC-ROC が高いほど強い相関
    - 最初の$l$層までの累積効果を$R_l$とおく:$$R_l = \frac{\sum_{i=1}^l (\text{AUCROC}_i - 0.5)}{\sum_{i=1}^N (\text{AUCROC}_i - 0.5)}.$$
  - 結果
    - 浅い層(1-20)では累積効果が小さい
    - 深い層(30-48)の AUC-ROC が 0.8 に近づいている
      - ターゲットの位置の attention の分布と最終的な予測には強い相関
  - **モデルは深い層でラベルの単語の情報を抽出し、最終的な予測を形成**![](https://static.zenn.studio/user-upload/1a9ddf8a7f88-20260825.png)
- 考察
  - 実験を通して 2 つの仮説が検証された
    1. 浅い層では、ラベルの単語が情報を集約している
    2. 深い層では、ラベルの単語から情報を取り出している
       以上からラベルの単語は「**アンカー(Anchor)**」とみなせる
- Anchor Re-weighting: ラベルの単語 (Anchor) の attention の値を重み付けし直す手法$$\hat{A}(k,j)=\begin{cases}{\color{red}\exp{\beta_{0}^{i}}}A(k,j)&\text{if $k$=``target position'', $j$=``label position''}\\A(k,j)&\text{otherwise}\end{cases}$$
  - $\boldsymbol{\beta}=\{\beta_0^i\}$は学習パラメータ
  - 各クラスにつき 4 つのサンプルを取り、学習セット$(\boldsymbol{X}_\text{train},\boldsymbol{Y}_\text{train})$とする
  - これらについて ICL を行って得た損失関数を用いて勾配降下法で$\boldsymbol{\beta}$を最適化 $$\boldsymbol{\beta}^* = \arg\min_{\boldsymbol{\beta}} \mathcal{L}(\mathbf{X}_{\text{train}}, \mathbf{Y}_{\text{train}}).$$
  - 導出
    - モデルの最終的な予測と、ターゲットの位置のラベルの単語についての attention の分布に相関があるから、attention 層を分類器$\boldsymbol{f}$とみなす$$\begin{align*}\Pr_f(Y = i \mid X = x) \approx \mathcal{A}(q, p_i) = \frac{\exp(\mathbf{q}_q \mathbf{k}_{p_i}^T / \sqrt{d})}{\sum_{j=1}^N \exp(\mathbf{q}_q \mathbf{k}_j^T / \sqrt{d})}.\end{align*}$$
    - $\hat{\boldsymbol{x}}=\frac{\boldsymbol{q}_q}{\sqrt{d}}, \boldsymbol{\beta}_i=\boldsymbol{k}_{p_i}-\boldsymbol{k}_{p_C}$とするとロジスティック回帰とみなせる$$\log \frac{\Pr_f(Y = i \mid X = x)}{\Pr_f(Y = C \mid X = x)} = \boldsymbol{\beta}_i^T \hat{\mathbf{x}}.$$
    - さらに右辺を学習可能な$\beta_0^i$を用いて$\beta_o^i+\boldsymbol{\beta}_i^\top\hat{\boldsymbol{x}}$とする
      - これは attention の重み$A(q,p_i)$を調整することと等価
      - $\beta_0^i$は attention の head と層に対して一意に設定
      - label の単語をより強く重みづける
    - これを変形すると$\exp(\beta_0^i)A(k,j)$が得られる
  - 結果
    - Anchor Re-weighting により ICL の性能が向上
    - パラメータを学習することで、1-shot でも精度が向上![](https://static.zenn.studio/user-upload/967ff0e876e9-20260825.png)
- Hidden_anchor: ICL で入力する例を圧縮する手法
  1.  ラベルの単語の隠れ状態$\{\{\boldsymbol{h}_l^i\}_{i=1}^C\}_{l=1}^N$を計算してキャッシュ
      - $\boldsymbol{h}_l^i$: $i$番目の単語についての第$l$層の隠れ状態
  2.  $\boldsymbol{h}_l^1,\ldots,\boldsymbol{h}_l^C$とフォーマットの隠れ状態を推論中の各層の最初に連結
      1. 予備実験の結果、フォーマットがないとうまく行かない（関連研究でフォーマットが出力の空間を定めているという指摘） 2.
  - 以下の例では$\boldsymbol{h}_l^\text{Negative},\boldsymbol{h}_l^\text{Positive}, \boldsymbol{h}_l^\text{Review},\ldots,\boldsymbol{h}_l^\text{:}$![](https://static.zenn.studio/user-upload/9bbae052fb1a-20260825.png)
  - 動機:
    - 例からの情報が集約されているラベルの単語に、出力が強く依存
    - GPT は自己回帰型で、情報の集約は後の単語には依存しない
  - 比較手法
    - **Text_anchor**: フォーマットとラベルのテキストを連結
      - ラベルの単語があるだけではだめで集約が必要
    - **Hidden_random**: ラベル以外からランダムに選んだ単語の隠れ状態を>連結
    - **Hidden_random-top**: ラベル以外からランダムに選んだ単語 20 個の中で最も label loyalty が高いものの隠れ状態を連結
  - 結果: 他の手法と比べて、ICL からの精度の低下を小さくしつつ圧縮できた![](https://static.zenn.studio/user-upload/2a5df6d8ffd7-20260825.png)
    - ICL の例が短くなることにより推論が高速化された![](https://static.zenn.studio/user-upload/55380af7b40e-20260825.png)
