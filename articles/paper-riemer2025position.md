---
title: "Theory of Mind Benchmarks are Broken for LLMs (ICML 2025)"
emoji: "💔"
type: "idea"
topics:
  - "論文メモ"
published: true
published_at: "2026-09-04 19:48"
---

# [Position: Theory of Mind Benchmarks are Broken for Large Language Models](https://proceedings.mlr.press/v267/riemer25a.html) (ICML 2025 position)
Authors: Matthew Riemer, Zahra Ashktorab, Djallel Bouneffouf, Payel Das, Miao Liu, Justin D. Weisz, Murray Campbell

- LLM のToMベンチマークは他者の行動を予測する能力しか測っておらず、予測を自分の行動選択に活かす能力を測っていないため不十分である、と主張する position paper
- 背景・既存手法の問題点
	- LLM の ToM 評価の多くは人間向けの評価から着想を得ており、人間では「予測と自分の意思決定の一貫性」があるが、LLM ではこの一貫性が成り立たない
	- 既存のベンチマークは、文字通りのToMのみを測定しており、機能的なToMを測定しておらず、他のエージェントの行動に基づいて適切な行動を選択する能力を評価できていない
		- 例えば、常にグーを出す相手と100 回じゃんけんをした場合、LLM は 3 手をほぼ均等に出してしまう（＝Nash 均衡解）。相手の次の手はほぼ正確に予測できているのに、その予測が自分の行動に反映されていない。![](https://static.zenn.studio/user-upload/910f0eb4be86-20260904.png)
	- なお実際に「心の理論」モデルが明示的に存在するかは重要ではなく、「心の理論」の能力を記述するには解く問題に焦点を当てることが重要（取り組む方法とは切り分けるべき）としている
- 問題設定
	- 複数エージェント間の相互作用を部分観測可能確率ゲーム (POSG) $\langle \mathcal{I},\mathcal{S},\mathcal{A},\mathcal{T}, \mathcal{R},\mathcal{X},O,T\rangle$ として定式化
		- $i\in\mathcal{I}$はエージェント
		- $s\in\mathcal{S}$は状態
		- $a\in\mathcal{A}=\times_i\mathcal{A}^i$はエージェント$i$の共同の行動
		- $t\in\mathcal{T}\mid\mathcal{T}:\mathcal{S}\times\mathcal{A}\mapsto\mathcal{S}$はエージェント間の共同の行動に基づく状態遷移関数
		- $r\in\mathcal{R}\mid\mathcal{R}:\times_{i\in\mathcal{I}}\mathcal{R}^i$は、報酬関数$\mathcal{R}^i:\mathcal{S}\times\mathcal{A}\mapsto\mathbb{R}$に基づく共同報酬
		- $\mathcal{X}=\times_{i}\mathcal{X}^{i}$は、各エージェントの観測集合$\mathcal{X}^i$の結合集合
		- $O:\mathcal{S}\mapsto\mathcal{X}$は、状態に基づいてエージェントの観測値を生成する関数
		- $T$ は終了までの相互作用の時間
	- エージェント $i$ は、自身の相互作用の履歴 $h^i_t=\left\{x^i_1,a^i_1,r^i_1,\dots,x^i_t\right\}$ に基づき方策 $\pi^i(h^i_t)$ から行動を選ぶ。他エージェントの方策 $\boldsymbol{\pi}^{-i}$ が与えられたときの最適方策は以下 $$\pi^{i^*}=\arg\max_{\pi^i}\mathbb{E}\left[\sum_{t=1}^T r^i_t \mid s_1,\boldsymbol{\pi}^{-i}\right]$$
	- ただし、LLM を評価するので、観測・行動・報酬はすべてテキストとして履歴に埋め込まれ、モデルの重みは更新せず in-context での適応のみを扱う。
- 二つの ToM の定義
    - **Literal ToM（他者の行動を予測する能力） 損失**：他者の方策 $\boldsymbol{\pi}^{-i}$ が生成する行動列$\boldsymbol{a}^{-i}_1,\dots,\boldsymbol{a}^{-i}_T$と、その近似モデル $\hat{\boldsymbol{\pi}}^{-i}$ が生成する行動列$\hat{\boldsymbol{a}}^{-i}_1,\dots,\hat{\boldsymbol{a}}^{-i}_T$の距離 $$\mathcal{L}_{\text{Literal}}(s_1,\hat{\boldsymbol{\pi}}^{-i},\boldsymbol{\pi}^{-i},T)=\mathbb{D}\big(\phi(\boldsymbol{a}^{-i}_1,\dots,\boldsymbol{a}^{-i}_T),\ \phi(\hat{\boldsymbol{a}}^{-i}_1,\dots,\hat{\boldsymbol{a}}^{-i}_T)\big)$$
        - $\mathbb{D}$ は距離関数
        - $\phi$ は行動に対する抽象化写像（恒等写像、意図・信念・感情などの潜在変数への写像など）
    - **Functional ToM リグレット**：他者の方策$\boldsymbol{\pi}^{-i}$ に対する最適な応答 $\pi^{i^*}$ を取ったときの報酬$r^{i^{*}}_{1},...,r^{i^{*}}_{T}$と、エージェントが取った方策$\pi^{i}$の報酬$r^{i}_{1},...,r^{i}_{T}$の差 $$\Delta_{\text{Functional}}(s_1,\pi^i,\pi^{-i},T)=\sum_{t=1}^T (r^{i^*}_t-r^i_t)$$
    - literal ToM の予測を信じて行動価値を最大化する方策のリグレットを $\Delta_{\text{ToM}}$ とする。これは「予測が正しいと仮定して忠実に従った場合に達成しうる最良のリグレット」であり、literal と functional の差を見るのに用いる
    - Functional ToM リグレットを用いて、課題の「面白さ」の指標を定義:$$\mathbb{E}_{s,\boldsymbol{\pi}'^{-i},\boldsymbol{\pi}''^{-i}}[\Delta_{\text{Functional}}(s,\pi'^*,\boldsymbol{\pi}''^{-i},T)/T]$$
    	- 他エージェント方策の分布$p(\boldsymbol{\pi}^{-i})$ と初期状態分布 $p(s_1)$ を含む
    	- 「ある相手への最適応答を別の相手に使ったときの損失」を指す。相手ごとの適応が本当に必要かを表す。グーしか出さない相手とのじゃんけんでは 1 ステップあたり 1.0 
- 実験設定
	- ゲーム: じゃんけん (RPS)、Iterated Battle of Sexes (IBS)、Iterated Prisoner's Dilemma (IPD) の 3 ゲームを、$T=100$ ラウンド
	- 相手は Simple Action (常に同じ手を出す単一行動方策) またはtit-for-tat (こちらの行動によって反応が変わる)
	- 行動名は事前知識の混入を避けるため中立記号（$J, F, B$）を使うが、実際の行動名（Rock/Paper/Scissors など）や無意味語との比較も行う。
	- 評価は ToM %（literal 予測の行動の一致率）、$\Delta_{\text{Functional}}/T$、$\Delta_{\text{ToM}}/T$ の 3 つ。
	- 比較対象は RMax（楽観的 Q 学習による探索）に基づく表形式モデルと、その頻度カウント版 ToM モデル。
	- モデルは LLAMA-2 系列、Falcon 40B、Mixtral 8x7B Instruct、LLAMA-3 70B Instruct、Mistral Large 2、DeepSeek-R1-Distill-Qwen-32B。
	- プロンプト戦略
		- 汎用的な手法: LM（各行動のトークン確率を直接比較）、QA（質問に対する答えを生成）、Chain-of-Thought (CoT)
		- 文脈内学習: CoT 3-shot、Plans+Insights (計画や洞察についても生成)、Reflexion (フィードバックを記憶)
		- ToMを明示的に考慮: Social prompting（自分の literal ToM 予測を条件として与える）、System 2 Attention (S2A)、Oracle（相手の実際の行動を入力）など。
- 結果
    - 予測はできてもその予測に沿った行動を取っていない: LM は literal ToM の一致率が 90% 台だが、functional リグレットは表形式モデルより大きく、$\Delta_{\text{ToM}}$ と $\Delta_{\text{Functional}}$ の間に一貫した差が残る。![](https://static.zenn.studio/user-upload/4e0412f570a7-20260904.png)
    - 長いコンテキストを扱う推論は難しい: literal ToM の予測を明示的に入力として与えてもまだ難しい (Social prompting)。Oracle prompting（相手の実際の行動と利得表を与える）でも、Tabularに及ばない。![](https://static.zenn.studio/user-upload/825aa31df3ec-20260904.png)
    - 履歴と利得表を除いた場合は、RPS と IBS では履歴が重要で利得表はあまり寄与せず、IPD では両方を同時に与えると性能が悪化し、どちらか一方のみの方が良い。S2A による要約も改善に寄与していない。
    - functional ToM と literal ToM が互いを含意しない: 推論特化モデル DeepSeek-R1-Distill-Qwen-32B は、Single Actionに対してはtabular以上の性能を示すが、その literal ToM の一致率は低く、$\Delta_{\text{ToM}}$ は実際の行動より悪い。tit-for-tat 相手では性能が安定しない。![](https://static.zenn.studio/user-upload/7d5372fa2811-20260904.png)
    - 行動名の帰納バイアス：実際のゲームの行動名を使うと相互作用の初期には性能が良いことがあるが、長期的には収束を妨げる場合がある。
- Alternative Views
	- ToM ではなくゲーム理論を採用すべき→ 人間や最適でないエージェントと関わる実運用では、期待値を最適化する（＝関係が協調的な）ToMよりも最悪ケースを最適化する（＝関係が敵対的な）ゲーム理論の方が良い
	- 洞察を提供する用途には literal ToM で十分→ AI エージェントに意思決定をどこまで移譲させるかは functional ToM で判断すべきだが、行動の権限を与えない場合では literal ToMでよい。
	- function ToM は fine-tuning で向上する→ in-context 学習ではあまり改善していないが強化学習ベースのfine-tuningなら解決できるはず
	- predict-then-optimize との関連: 予測誤差は下流の意思決定に対して一様に影響を与えるわけではない（？）
- 感想
    - 理性的に考えれば、じゃんけんのようなゲームで推測しているのは相手の信念というよりもパターンなので、論理的に正しい予測を選択するのは当然だし、ToMはあまり関係ない気がする
    - タイトルに「ベンチマーク」を含んでいる以上は、ベンチマークを直接調べるか、実験設定と比較するかはしてほしかった