---
title: "Function Vectors in Large Language Models (ICLR 2024)"
emoji: "↗️"
type: "idea"
topics:
  - "論文メモ"
published: true
published_at: "2026-11-04 15:08"
---

# [Function Vectors in Large Language Models](https://openreview.net/forum?id=AwyxtyMwaG) (ICLR 2024)
Authors: Eric Todd, Millicent Li, Arnab Sen Sharma, Aaron Mueller, Byron C Wallace, David Bau

- 媒介分析 (causal mediation analysis) によって、ICL のタスクにおいて自己回帰 Transformer 言語モデルの中に少数の attention のヘッドがタスクのコンパクトな表現 (**function vector**; FV) を表している
    - FV は文脈の変化に頑健で、異なる形式・ゼロショット・自然言語での入力でもタスクを実行できる
    - FV は因果関係のある attention のヘッドの出力の合計でなる
        - 因果効果と言っているのは、ラベルをシャッフルした ICL を対照群としている（全体でのラベルの比は変わらない）
        - 各レイヤ・ヘッドについて、プロンプトを入れたときの activation を全プロンプトについてとって平均をとる
            - 因果効果を調べ、全レイヤ・ヘッドを通して上位のものをとってきて輪をとったものを FV とする
            - このヘッドでの最後のトークンでの平均の attention のパターンを見ると、文脈の例のラベルにより重みがかかっている![](https://static.zenn.studio/user-upload/6cd0557447ea-20261104.png)
- 実験として ICL のプロンプトを用意し、それぞれの最後のトークンの activation の平均を各層で取り出し、ゼロショットの時に足してみると（特に中間の層で）精度が上がった![](https://static.zenn.studio/user-upload/55cc4017a0e5-20261104.png)
- 実験では、様々なタスク・モデル・層で中間層の設定で強い因果効果
    - FV を多くの種類の関数を拡張する一般的なメカニズムであるという仮説を立てて実験すると 6B ～ 70B のモデル・40 のタスクで試した
    - タスクは対義語・capitalize・首都・翻訳・過去形変換・複数形変換
    - モデルは GPT-J (6B), GPT-NeoX (20B), Llama 2 (7B, 13B, 70B)
        - それぞれ(layer, head)は(28, 16), (44, 64), (32, 32), (40, 40), (80, 64)
        - 取り出したヘッドの数（おそらく全体で）は 10, 50, 20, 50, 100
    - FV を足したときにどのくらいの正答率になるかを見る (10-shot)![](https://static.zenn.studio/user-upload/82aefc4fe1ab-20261104.png)
        - FV を|L|/3 層目に入れてみるとうまくいく？
- ゼロショットの時に FV を前のレイヤに足すとよくなる![](https://static.zenn.studio/user-upload/c4d442ada56e-20261104.png)
- 自然言語のときも FV を足すとよくなる（つまり入力の形式に対して FV は頑健）![](https://static.zenn.studio/user-upload/7e0941be7a2a-20261104.png)
- FV を直接デコードするとタスクの出力空間がみられる(単数形 → 複数形など)![](https://static.zenn.studio/user-upload/1293a9617778-20261104.png)
- FV の内部を調べると、関数の出力空間がエンコードされているが、この情報だけで FV を再現できるわけではない
    - 上位 k(=100 など)個だけの語彙で FV を構築すると精度が大きく下がる
- FV の意味的なベクトルの構成を調べると、足し合わせることで新たな複雑なタスクを作れる
    - ある国名の列(Italy, Russia, China, Japan, France)について、最初の首都(Rome)を出力＋最後(France)を取り出すー最初(Italy)を取り出す＝最後の首都(Paris)を出力になる![](https://static.zenn.studio/user-upload/fb28df250e23-20261104.png)
- コンパクトで因果関係のある、関数の概要の内部ベクトル表現を明示的に LLM から取り出せるを示した
