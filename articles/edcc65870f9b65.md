---
title: "An Analysis of BERT's Attention (BlackboxNLP 2019)"
emoji: "🔎"
type: "idea"
topics:
  - "論文メモ"
published: true
published_at: "2026-07-17 17:45"
---

# [What Does BERT Look at? An Analysis of BERT's Attention](https://aclanthology.org/W19-4828) (BlackboxNLP 2019)
Author: Kevin Clark, Urvashi Khandelwal, Omer Levy, Christopher D. Manning

- BERTの注意機構を分析し、構文関係などが現れることを示した。
- この論文では、BERTの注意機構そのものを解析する
  - 注意機構は、どの単語が他の単語にどの程度重みを置くかを明示的に表現するため解釈しやすい
  - 分析対象は、BERT-baseモデルのattention head（12層×12ヘッド）
- 全般的な注意パターンの傾向の観察![](https://static.zenn.studio/user-upload/a054e9a65f77-20260717.png)
  - 多くのheadが、固定長のオフセット（直前直後のトークンなど）や文全体をカバーするなどの一定の傾向を持つ
  - 初期層では`[CLS]`トークン、中間層では`[SEP]`トークン、深い層では句読点へ多くの注意が集中している![](https://static.zenn.studio/user-upload/36dc189b445b-20260717.png)
    - `[SEP]`トークンへの注意は“no-op”（何もしない操作）として機能している
      - `[SEP]`トークンに集まった注意はまた`[SEP]`トークンに注意が移っており集約の意味を果たしていない
      - `[SEP]`トークンへの注意を増減させても勾配が小さい（出力が変化しにくい）![](https://static.zenn.studio/user-upload/b4686a717ae5-20260717.png)
    - 各注意headの注意の分布のエントロピーを調べると、特に下位層では注意の分布が分散しており、入力全体の表現を集約している![](https://static.zenn.studio/user-upload/55ad44426bee-20260717.png)
    - また、同じ層内のhead同士は似たような振る舞いをする
- 言語的現象との対応
  - 各headを、入力単語に対して最も注意の重みを付ける他の単語を出力する分類器とみなして、構文関係を分類する能力を評価した
    - baselineとして、固定長のオフセットをとる方法と比較する（名刺の冠詞は1つ前の単語を予測とするというように）
  - 特定のheadが特定の文法関係に特化して高精度に学習していた![](https://static.zenn.studio/user-upload/efd9648c8d67-20260717.png)
    - 自己教師あり学習の結果これが獲得されているのは高い非自明
    - 直接目的語から動詞、名詞修飾語から名詞、前置詞から前置詞の目的語、代名詞から参照元など
    - 注意重み付けと構文関係の類似性を捉えた例（ただし構文関係全体を捉えたとは言えない）![](https://static.zenn.studio/user-upload/90d9c9fafda7-20260717.png)
  - 共参照解析でも同様![](https://static.zenn.studio/user-upload/fe5d0e5531e4-20260717.png)
    - ある見出し語がそれの先行表現のどの見出し語に最も注意をつけているか
    - ルールベースとほぼ同等の性能
- 注意機構を利用した構文プロービング分類器を作成
  - 注意機構を凍結して注意の値を表すベクトルを入力とする線形分類器を学習
  - 依存構文解析タスクでルールベースのベースラインを超える分類性能![](https://static.zenn.studio/user-upload/a86077047eab-20260717.png)
  - BERTのattentionには構文構造に関する重要な情報が内包されている
- 注意ヘッドをクラスタリング![](https://static.zenn.studio/user-upload/917aa0eca32a-20260717.png)
  - ヘッド間のJensen-Shanonダイバージェンス
  - 注意ヘッドのクラスターがいくつかあり、すでに述べた注意機構の動作パターンに対応している