---
title: "Why Can GPT Learn In-Context? (ACL 2023)"
emoji: "🔫"
type: "idea"
topics:
  - "論文メモ"
published: true
published_at: "2026-08-27 14:49"
---

# [Why Can GPT Learn In-Context? Language Models Secretly Perform Gradient Descent as Meta-Optimizers](https://aclanthology.org/2023.findings-acl.247) (ACL 2023)
Authors: Damai Dai, Yutao Sun, Li Dong, Yaru Hao, Shuming Ma, Zhifang Sui, Furu Wei


- GPT の文脈内学習（in-context learning, ICL）が暗黙的な勾配降下として機能することを理論的・実験的に調べた。![](https://static.zenn.studio/user-upload/09d8a36bffed-20260827.png)
- 問題設定
  - GPT をテキストの分類タスクに適用
    - クエリ入力テキスト$x$と解答候補の集合$Y=\{y_1,y_2,\ldots,y_m\}$が与えれたとき、解答候補$y_i$の条件付き確率$P\left(y_i\mid x\right)$を計算し、確率が最も高い$y_i$を選択する$$\hat{y} = \arg\max_{y_i} P\left(y_i\mid x\right)$$
  - 文脈内学習(ICL) (https://papers.nips.cc/paper/2020/hash/1457c0d6bfcb4967418bfb8ac142f64a-Abstract.html): クエリ入力 $x$と事例の集合 $C = \{(x'_i, y'_i)\}_{i=1}^n$を与えられたときに予測 $\hat{y}$ を求める
    - モデル $\mathcal{M}$ に基づく予測確率は、$\mathcal{M}(I)$を最後のトークン位置での隠れ状態、$\boldsymbol{e}_{y_j}$を$y_j$の単語埋め込みとして、$l_j$が$j$番目の答えに対応するロジットで、$$\begin{align}l_j &= \mathcal{M}(I) \cdot \boldsymbol{e}_{y_j}\\P_\mathcal{M}\left(y_i\mid C, x\right) &= \text{softmax}(l_j)\end{align}$$
    - 実際には、$I$ はクエリと事例を、関数$\mathcal{T}\left(\cdot\right)$を用いてフォーマットしたものを連結して構成される$$\begin{align}\mathcal{T}(x,y)&=\text{Review: } x.\ \text{Sentiment: } y.\\I&=\mathcal{T}(x_1^\prime, y_1^\prime)\mathcal{T}(x_2^\prime, y_2^\prime)\cdots\mathcal{T}(x_n^\prime, y_n^\prime)\mathcal{T}(x, \_)\end{align}$$
    - 線形注意機構: 系列$\left[\boldsymbol{x}_{1}, \ldots, \boldsymbol{x}_{i}, \ldots, \boldsymbol{x}_{T}\right]$について、バリューベクトルの行列${V}=\left[\boldsymbol{v}_{1},\ldots,\boldsymbol{v}_{T}\right]\in\mathbb{R}^{d_\text{out}\times T}$, キーベクトルの行列${K}=\left[\boldsymbol{k}_{1},\ldots,\boldsymbol{k}_{T}\right]\in\mathbb{R}^{d_{\text{out}}\times T}$, クエリベクトル$\boldsymbol{q}\in\mathbb{R}^{d_\text{out}}$として、$$\begin{align}\text{LinearAttn}({V}, K, \boldsymbol{q})&=\sum\limits_{t=1}^{T}\boldsymbol{v}_t\boldsymbol{k}_{t}^{\top}\boldsymbol{q}\\&={V}{K}^{\top}\boldsymbol{q}&\in\mathbb{R}^{d_\text{out}}\end{align}$$
      - $\boldsymbol{v}_{t},\boldsymbol{k}_{t},\boldsymbol{q}$は、入力表現$\boldsymbol{x}\in\mathbb{R}^{d_{\text{in}}}$から、パラメータ$W_{V},W_{K},W_{Q}\in\mathbb{R}^{d_{\text{out}}\times d_{\text{in}}}$を用いて以下のように導出$$\begin{align*}\boldsymbol{v}_{t}&=W_{V}\boldsymbol{x}\\\boldsymbol{k}_{t}&=W_{K}\boldsymbol{x}\\\boldsymbol{q}&=W_{Q}\boldsymbol{x}\\\end{align*}$$
- 先行研究: 線形注意機構は、勾配降下で更新された NN 内の線形層と双対形式をなす (https://proceedings.mlr.press/v162/irie22a.html)
  - 線形注意機構の内部の key-value ペアで、学習データの入力と更新量を格納していると表現できる
  - NN 内の線形層$\mathcal{F}\left(\boldsymbol{x}\right)$は$W_0, \Delta W\in\mathbb{R}^{d_\text{out}\times d_\text{in}}$をパラメータ$W\in\mathbb{R}^{d_\text{out}\times d_\text{in}}$の初期パラメータとパラメータの変化として、$$\mathcal{F}\left(\boldsymbol{x}\right) = W\boldsymbol{x}=(W_0 + \Delta W)\boldsymbol{x}$$
  - $W$の変化は更新に用いた入力$\boldsymbol{x}^{\prime}_{i}\in\mathbb{R}^{\text{in}}$と$\boldsymbol{x}^{\prime}_{i}$に対する誤差$\boldsymbol{e}_{i}\in\mathbb{R}^{d_\text{out}}$を用いて、$$\Delta W=\sum\limits_{i=1}^{n}\boldsymbol{e}_{i}\otimes \boldsymbol{x}^{\prime}_{i}=E{X^{\prime}}^{\top}$$
    - 勾配降下の場合、$\boldsymbol{e}_{i}=-\eta_{i}\left(\nabla_{y}\mathcal{L}\right)_{i}$
    - ここで、$X^{\prime}=\left[\boldsymbol{x}^{\prime}_{1},\ldots,\boldsymbol{x}^{\prime}_{n}\right], E=\left[\boldsymbol{e}_{1},\ldots,\boldsymbol{e}_{n}\right]$
  - これらを組み合わせると線形注意機構の形式になる$$\begin{align*}\mathcal{F}\left(\boldsymbol{x}\right)&=\left(W_0 + \Delta W\right)\boldsymbol{x}\\&=W_0\boldsymbol{x}+E{X^{\prime}}^{\top}\boldsymbol{x}\\&=W_0\boldsymbol{x}+\text{LinearAttn}\left(E, X^{\prime}, \boldsymbol{x}\right)\end{align*}$$
  - 更新に用いる入力$X^\prime=[\boldsymbol{x}^\prime_1, \ldots, \boldsymbol{x}^\prime_T]$をキー、誤差$E=[\boldsymbol{e}_1, \ldots, \boldsymbol{e}_T]$をバリュー、現在の入力$\boldsymbol{x}$をクエリとする線形注意機構に対応する
- 暗黙の Fine-tuning としての ICL の理解
  - ICL における線形注意機構の出力は、$X$をクエリトークンの表現の行列、$X^{\prime}$を事例トークンの表現の行列として、これらを連結した行列$\left[X,X^{\prime}\right]$を入力したとき、$\boldsymbol{q}=W_{Q}\boldsymbol{x}$として、$$\begin{align*}\mathcal{F}_{\text{ICL}}(\boldsymbol{q}) &=\text{LinearAttn}\left(W_{V}\left[X^{\prime}; X\right], W_{K}\left[X^{\prime}; X\right], \boldsymbol{q}\right)\\&= W_{V}\left[X^{\prime}; X\right](W_{K}\left[X^{\prime}; X\right])^{\top}\boldsymbol{q}\\&=W_{V}X(W_{K}X)^{\top}\boldsymbol{q} + W_{V}X^{\prime}(W_{K}X^{\prime})^{\top}\boldsymbol{q}\\&=W_{V}X(W_{K}X)^{\top}\boldsymbol{q} + \text{LinearAttn}\left(W_{V}X^{\prime}, W_{K}X^{\prime}, \boldsymbol{q}\right)\\&= W_{\text{ZSL}}\boldsymbol{q} + \sum_{i}W_{V}\boldsymbol{x}_{i}^{\prime}\left((W_{K}\boldsymbol{x}_{i}^{\prime})^{\top}\boldsymbol{q}\right) \\&= W_{\text{ZSL}}\boldsymbol{q} + \sum_{i} \left(\left(W_{V}\boldsymbol{x}_{i}^{\prime}\right)\otimes\left(W_{K}\boldsymbol{x}_{i}^{\prime}\right)\right)\boldsymbol{q} \\&= \left(W_{\text{ZSL}}+\Delta W_{\text{ICL}}\right)\boldsymbol{q}\end{align*}$$
    - $W_{\text{ZSL}}=W_{V}X(W_{K}X)^{\top}$は zero-shot の入力についての線形注意機構
    - $\Delta W_{\text{ICL}}=\sum_{i} \left(W_{V}\boldsymbol{x}_{i}^{\prime}\right)\otimes\left(W_{K}\boldsymbol{x}_{i}^{\prime}\right)$は$W_{\text{ZSL}}$に影響を与えるパラメータ更新と等価
    - $W_VX^\prime$がメタ勾配とみなせ、線形注意機構によって$\Delta W_\text{ICL}$の計算に用いられる
  - ICL と fine-tuning の比較
    - zero-shot の入力についての線形注意機構のパラメータから、キーとバリューのパラメータのみ更新する fine-tuning の更新式は以下$$\begin{align*}\mathcal{F}_\text{FT}(\boldsymbol{q}_i) &=\left(W_V+\Delta W_V\right)XX^\top\left(W_K+\Delta W_K\right)^\top\boldsymbol{q}_i \\&= (W_\text{ZSL}+\Delta W_\text{FT})\boldsymbol{q}_i\end{align*}$$
    - ただし ICL と条件を揃えるため、以下の制約を課す
      - fine-tuning するのは Linear Self-Attention の$W_K$と$W_V$のみ
        - ICL で使用する事例のみで fine-tuning を行う
        - ICL で与えられう順番と同じ順序で 1 ステップだけ学習
        - ICL と同じテンプレート$\mathcal{T}(x,y)$を使用する
  - ICL は暗黙的勾配降下による更新$\Delta W_\text{ICL}$を、fine-tuning は明示的勾配降下による更新$\Delta W_\text{FT}$を、それぞれ$W_\text{ZSL}$に対して行っている
- 実験
  - モデル: GPT 1.3B, 2.7B
  - 事例は 32 個を使って FT と ICL を比較
  - データセット (いずれも分類タスク): SST2, SST5, MR, Subj, AGNews, CB
  - 指標
    - Rec2FTP (recall to finetuning prediction): **モデルの予測**を見て、ICL がどれだけ FT のふるまいをカバーできているか
    - SimAOU (similarity of the attention output updates): ICL と FT での更新の類似度（具体的には**隠れ状態**のコサイン類似度）
    - SimAM (similarity of the attention map): クエリトークンへの **attention のマップ**（具体的には最後のトークンのソフトマックスの前の attention 重み）の類似度
    - Kendall rank correlation coefficient: クエリの最後のトークンの事例のトークンへの **attention 重み**
  - 結果として ICL と FT は似たふるまいをすることを確認![](https://static.zenn.studio/user-upload/ae88da6229e1-20260827.png)![](https://static.zenn.studio/user-upload/b9b12330f718-20260827.png)![](https://static.zenn.studio/user-upload/f7cd8d973a61-20260827.png)![](https://static.zenn.studio/user-upload/7409173dc1f0-20260827.png)
- attention と勾配降下が dual form をなすことから、momentum ベースの attention を提案![](https://static.zenn.studio/user-upload/b28b7b7e1b8e-20260827.png)
  - メタ勾配を attention の値とみなして、これに momentum を適用![](https://static.zenn.studio/user-upload/f0271f348a10-20260827.png)
  - もとの attention よりも言語モデルと ICL で良い結果![](https://static.zenn.studio/user-upload/93a5b25a1400-20260827.png)
