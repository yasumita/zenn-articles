---
title: "Transformer Dissection (EMNLP-IJCNLP 2019)"
emoji: "✂️"
type: "idea"
topics:
  - "論文メモ"
published: true
published_at: "2026-07-17 17:33"
---

# [Transformer Dissection: An Unified Understanding for Transformer's Attention via the Lens of Kernel](https://aclanthology.org/D19-1443) (EMNLP-IJCNLP 2019)
Author: Yao-Hung Hubert Tsai, Shaojie Bai, Makoto Yamada, Louis-Philippe Morency, Ruslan Salakhutdinov 
## メモ
- 概要: Transformerの注意機構をカーネルの観点から再定式化し、より広い空間上でAttentionを設計
- Attentionとカーネルは関連づけられる
	- どちらも同時でかつ順序によらずに入力を処理する
	- 入力間の類似度を計算している
- カーネルに基づいたAttentionの定式化 
    $$\text{Attention}\left(x_q; M\left(x_q, S_{\mathbf{x}_k}\right)\right) = \sum_{x_k \in M\left(x_q, S_{\mathbf{x}_k}\right)} \frac{k(x_q, x_k)}{\sum_{x_k^\prime \in M(x_q, S_{\mathbf{x}_k})} k(x_q, x_k^\prime)} v(x_k) = \mathbb{E}_{p(x_k \mid x_q)} \left[v\left(x_k\right)\right]$$
	- $x_q$: クエリ
	- ${S}_{\mathbf{x}_k}$: キーの集合
	- $M(x_q, {S}_{\mathbf{x}_k})$: 集合をフィルターする関数で、$x_q$から見える要素を持つ集合を含む
	- $k\left(\cdot,\cdot\right)$: 非負カーネル関数
		- これが指数カーネルのときはlinear smootherがsoftmax attention
	- $v\left(\cdot\right)$: バリュー関数
	- $p\left(x_k\mid x_q\right)$: カーネル平滑化によるlinear smoother
    $$p\left(x_k\mid x_q\right)=\frac{k({x}_q, {x}_k)}{\sum_{{x}_k^{\prime} \in M({x}_q, {S}_{\mathbf{x}_k})} k({x}_q, {x}_k^\prime)}$$
- Attentionの設計を理解するために以下を調べる
	- カーネル特徴空間$x_q,x_k\in\mathscr{X}$
	- 集合をフィルターする関数$M(x_q, {S}_{\mathbf{x}_k})$
	- カーネルの構築と位置埋め込みの役割$k\left(\cdot,\cdot\right)$
	- $v\left(\cdot\right)$: バリュー関数
- カーネル特徴空間$x_q,x_k\in\mathscr{X}$: Transformerの設計の最初のステップ
	- [Transformer](https://proceedings.neurips.cc/paper_files/paper/2017/hash/3f5ee243547dee91fbd053c1c4a845aa-Abstract.html): $\mathscr{X}=\left(\mathscr{F}\times\mathscr{T}\right)$
		- $\mathscr{F}$: 位置でない特徴空間＝単語埋め込み
		- $\mathscr{T}$: 位置埋め込み空間＝位置埋め込み
	- Image Transformer: $\mathscr{X}=\left(\mathscr{F}\times\mathscr{H}\times\mathscr{W}\right)$
		- $\mathscr{F}$: 位置でない特徴空間＝画像ピクセル
		- $\mathscr{H}$: 縦の位置埋め込み空間＝ピクセルの縦の位置
		- $\mathscr{W}$: 横の位置埋め込み空間＝ピクセルの横の位置
	- Set Transformer: $\mathscr{X}=\mathscr{F}$
		- $\mathscr{F}$: 位置でない特徴空間＝集合内のデータ
	- Non-Local Neural Networks:  $\mathscr{X}=\mathscr{F}$
		- $\mathscr{F}$: 位置でない特徴空間＝画像内のピクセル
- 集合をフィルターする関数$M(x_q, {S}_{\mathbf{x}_k})$
	- Transformer:
		- Encoder Self-Attention ($\mathbf{x}_q, \mathbf{x}_k$がともに言語A)
        $$S_1=M(x_q, S_{\mathbf{x}_k})=S_{\mathbf{x}_k}$$
		- Encoder-Decoder Attention ($\mathbf{x}_q$が言語B、$\mathbf{x}_k$が言語A)
        $$S_2=M({x}_q, {S}_{\mathbf{x}_k})=S_{\mathbf{x}_k}$$
		- Decoder Self-Attention ($\mathbf{x}_q, \mathbf{x}_k$がともに言語B): デコードされたトークンが将来のトークンを見るのを防ぐ
        $$S_3=M({x}_q, {S}_{\mathbf{x}_k})\subset S_{\mathbf{x}_k}$$
	- [Transformer-XL](https://aclanthology.org/P19-1285/): 記憶領域を追加することで、性能は良くなるが計算量は増える
		- Encoder Self-Attention ($\mathbf{x}_q, \mathbf{x}_k$がともに言語A)
        $$M({x}_q, {S}_{\mathbf{x}_k})=S_1+S_\text{memory}$$
		- Encoder-Decoder Attention ($\mathbf{x}_q$が言語B、$\mathbf{x}_k$が言語A)
        $$M({x}_q, {S}_{\mathbf{x}_k})=S_2+S_\text{memory}$$
		- Decoder Self-Attention  ($\mathbf{x}_q, \mathbf{x}_k$がともに言語B)
        $$M({x}_q, {S}_{\mathbf{x}_k})=S_3+S_\text{memory}$$
	- [Sparse Transformer](http://arxiv.org/abs/1904.10509): キーの部分サンプリングをすることで、性能と計算量の両方が良くなるが、サンプリングがランダムでないの慎重に決める必要
		- Encoder Self-Attention ($\mathbf{x}_q, \mathbf{x}_k$がともに言語A)
        $$M({x}_q, {S}_{\mathbf{x}_k})\subset S_1$$
		- Encoder-Decoder Attention ($\mathbf{x}_q$が言語B、$\mathbf{x}_k$が言語A)
        $$M({x}_q, {S}_{\mathbf{x}_k})\subset S_2$$
        - Decoder Self-Attention  ($\mathbf{x}_q, \mathbf{x}_k$がともに言語B)
        $$M({x}_q, {S}_{\mathbf{x}_k})\subset S_3$$
- カーネルの構築$k\left(\cdot,\cdot\right)$: データの類似度を測るのに重要
	- Transformer:
		- 非対称な指数カーネル$$k_\text{exp}\left(x_q, x_k\right) = \exp\left(\frac{\langle x_q W_q, x_k W_k \rangle}{\sqrt{d_k}}\right)$$
		- 絶対位置埋め込み: 時間空間と非時間空間の直和$$k(x_q, x_k) := k_\text{exp}\left(f_q+t_q, f_k+t_k\right)\quad \left(\mathscr{X}=\left(\mathscr{F}\oplus\mathscr{T}\right)\right)$$
	- Transformer-XL:
		- 非対称な指数カーネル
		- 相対位置埋め込みに変更: 位置埋め込みの各項を重みづけ$$\begin{align*}k(x_q, x_k) &:= k_\text{exp}\left(f_q, f_k\right)\cdot k_{f_q}\left(t_q, t_k\right)\\k_{f_q}\left(t_q, t_k\right)&=c_1\sin{\frac{t_q-t_k}{\text{constant}}}+c_2\sin{\frac{t_q-t_k}{\text{constant}}}+c_3\sin{\frac{t_q-t_k}{\text{constant}}}\\\end{align*}$$
			- $c_i$は$f_q$から推定
	- 提案手法
		- 半正定値カーネル: 対称な指数カーネル, 同時空間$\mathscr{X}:=\left(\mathscr{F}\times\mathscr{T}\right)$上での自然なカーネル構築$$\begin{align*}k(x_q, x_k) &:= k_F\left(f_q, f_k\right) \cdot k_T\left(t_q, t_k\right)\\\text{with}\quad k_F\left(f_q, f_k\right) &= \exp\left(\frac{\langle f_q W_F, f_k W_F \rangle}{\sqrt{d_k}}\right)\\\text{and}\quad k_T\left(t_q, t_k\right) &= \exp\left(\frac{\langle t_q W_T, t_k W_T \rangle}{\sqrt{d_k}}\right)\end{align*}$$
- バリュー関数$v\left(\cdot\right)$: データから伝えられるメッセージを決定する
	- Transformer: 位置特徴量も付属$$v(x_k)=v((f_k,t_k)):= (f_k+t_k)W_k$$
	- Transformer-XL: 位置特徴量はなし$$v(x_k)=v((f_k,t_k)):= f_kW_k$$
	- 意味$\mathscr{F}$と時間$\mathscr{T}$の同時特徴空間$\mathscr{X}:=\left(\mathscr{F}\times\mathscr{T}\right)$では、ほとんどの場合は意味のメッセージしか必要ない
	- 2つの異なる特徴の同時空間$\mathscr{X}:=\left(\mathscr{F}^1\times\mathscr{F}^2\right)$では、非線形な組み合わせのメッセージを考える
- 実験
	- 機械翻訳・系列予測
	- カーネル関数に位置埋め込みをどう組み込むか？![](https://static.zenn.studio/user-upload/fb252ecc30d9-20260717.png)
		- 積カーネルの形で組み込むのが良い
	- 注意機構に入れるカーネルはどの形のものが良いか？![](https://static.zenn.studio/user-upload/29fa3e01a819-20260717.png)
		- 無限特徴次元の形(指数カーネルやRBFカーネル)が一番良い結果
		- 対称な形のカーネルは非対称なカーネルと同等の性能
	- 注意機構が入力に関して順序にとらわれないという例外はあるか？もしそうなら、位置埋め込みの役割を軽視していいか？![](https://static.zenn.studio/user-upload/686c6a35bde1-20260717.png)
		- デコーダのself-attentionは順序に影響を受ける
		- 注意機構に位置埋め込みを組み合わせることで性能は上がる
	- バリュー関数に位置埋め込みは必要か？![](https://static.zenn.studio/user-upload/ba24851044f6-20260717.png)
		- あってもなくても性能は変わらない

## 参考資料
スライド: https://www.dropbox.com/s/w9ch3n3cxen6oom/slide.pdf