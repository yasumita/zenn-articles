---
title: "Marpの自分用メモ"
emoji: "📊"
type: "tech"
topics:
  - "marp"
published: true
published_at: "2024-09-02 23:32"
---

# 準備
- VSCodeに[Marp for VS Code](https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode)を追加
- VSCodeの設定 -> Markdown > marp: Themes にCSSファイルのパス or URLを追加

# 使い方
- mdファイルを作成してプレビューアイコンを押す
# 記法
- ページ区切りは`---`
- 見出しでページを区切るときは`<!-- headingDivider: 1 -->`

# ディレクティブ
| 名前                 | 説明                            |
| ------------------ | ----------------------------- |
| marp               | marpを有効化するか                   |
| theme              | テーマの指定                        |
| paginate           | スライドにページ番号を表示するか              |
| header             | ヘッダーの内容を指定                    |
| footer             | フッターの内容を指定                    |
| class              | スライドの`<section>`要素のHTMLクラスを指定 |
| backgroundColor    | background-colorのスタイル         |
| backgroundImage    | background-imageのスタイル         |
| backgroundPosition | background-positionのスタイル      |
| backgroundRepeat   | background-repeatのスタイル        |
| backgroundSize     | background-sizeのスタイル          |
| color              | colorのスタイル                    |
| math               | 数式の環境 (katex/mathjax)         |

## 一部のスライドに適用

`<!-- xxx -->` の形式で、各スライドの頭に設定を記述

|_を頭に|効果範囲|
|---|---|
|つける場合|「対象のスライドのみ」に設定を適用|
|つけない場合|「そのスライド以降の全て」に設定を適用|

# Tips

## 文字の中央寄せ・右寄せ
```
<div style="text-align:center">中央寄せにしたい文章</div>
```
## 文字のサイズ
```
<span style="font-size: **px;">文字のサイズ</span>
```
## 画像のサイズと表示位置
```
![height:*** weight:*** center](hoge.jpg)
```
## 画像の配置方向を縦に
```
![vertical](hoge.jpg)
```
# 参照
https://zenn.dev/hellorusk/articles/4edf3920dd1a35
https://zenn.dev/cota_hu/books/marp-beginner-advanced/viewer/create-2
https://qiita.com/tomo_makes/items/aafae4021986553ae1d8