---
title: "備忘録: Obsidianの初期設定・メモ"
emoji: "⚙️"
type: "tech"
topics:
  - "obsidian"
published: true
published_at: "2024-09-01 23:00"
---

# はじめに

Vault・端末が異なるとObsidianの環境設定が共有できないのでまとめる
主に自分用メモ・随時更新

# 設定

## オプション

### Obsidianについて

#### アプリ

- 言語: `日本語`

#### 高度な設定

- 起動に時間がかかった場合に通知: `true`

### ファイルとリンク

- ファイル削除の確認: `false`
- 内部リンクを毎回更新する: `true`
- 新規ノートの作成場所: `現在のファイルと同じフォルダ`
- 新規添付ファイルの作成場所: `現在のファイルと同じフォルダ`

### 外観

- テーマ: 好きなものを選ぶ

#### フォント

- インターフェースフォント (必要なら): `メイリオ`
- テキストフォント (必要なら): `メイリオ`
- モノスペースフォント (必要なら): `Source Code Pro`
- フォントサイズのクイック調整: `true`

#### 高度な設定

- ウィンドウフレームのスタイル: `Obsidianフレーム`

### ホットキー

以下を追加

|      機能       | ショートカット |
| :-----------: | :-----: |
|   上の行と入れ替え    | `Alt+↑` |
|   下の行と入れ替え    | `Alt+↓` |
| 右のサイドバーを開閉 | `Ctrl+Shift+R` |
| 左のサイドバーを開閉 | `Ctrl+Shift+L` |
| 現在のファイルを検索し置換 | `Ctrl+H` |
| ライブプレビュー/ソースモードを切り替える |`Alt+S`|

## コアプラグイン

以下を有効化

### アウトゴーイングリンク

### アウトライン

### キャンバス

### クイックスイッチャー

### グラフビュー

### コマンドパレット

### タグペイン

### デイリーノート

- 新規ファイルの場所: `日記`

### テンプレート

- テンプレートフォルダの場所: `templates`

### バックリンク

### ファイルエクスプローラ

### ファイルリカバリー

### ブックマーク

### プロパティビュー

### ページプレビュー

### ワードカウント

### 検索

## コミュニティプラグイン

入れすぎると重くなるので注意

- プラグインの更新を自動的に確認: `true`

### Annotator

PDFにannotationをつける。
新規ファイルのプロパティに以下を書き込み、markdownファイルを開き直す。
```
annotation-target: {title}.pdf
```

### Dataview

メモをデータベース的に扱える

### Find orphaned files and broken links

空のファイル・壊れたリンク・孤立したファイルなどを見つける。
コマンドパレットで呼び出す。

### Google Calendar

Google Calendar と連携

- 初期設定: [ここ](https://yukigasai.github.io/obsidian-google-calendar/Setup)に従う
- 2回目から
    1. https://console.cloud.google.com/ にアクセス
    2. 前に作成したプロジェクトを開く→「APIとサービス」
    3. クライアントIDとクライアントシークレットを、それぞれClientIdとClientSecretにコピーしてログイン

### Google Tasks

Google Todo と連携

- 連携はGoogle Calendarと同様

### Image Toolkit

画像表示を強化

### Kanban

Kanban用プラグイン
期限なしタスクの管理に使用

### Keyboard Analyzer

ホットキーの設定を表示 (`Keyboard Analyzer: Open Shortcuts View`)

### Linter

linter

- Lint on save: `true`

### Open In New Tab

常に新しいタブで開く

### Outliner

箇条書き機能を強化

### Quick Explorer

エクスプローラの強化、現在開いているファイルのパスを確認できる

### Quick LaTeX

数式がかける
- `align`ブロック: `Alt+Shift+A`
- `cases`ブロック: `Alt+Shift+C`
- `pmatrix`ブロック: `Alt+Shift+M`
- インラインの数式 (要設定): `Ctrl+M`
- ブロックの数式 (要設定): `Ctrl+Shift+M`

以下設定
- Shortcut for inline and block math: `true`
- Auto append "\limits" after "\sum": `false`
- Auto enclose expression after superscript/subscript with {}: `false`
- Custom Shorthand Parameter
    ```
    bi:::\binom{#cursor}{#tab};
    sq:::\sqrt{};
    bb:::\mathbb{};
    bf:::\mathbf{};
    te:::\text{};
    inf:::\infty;
    cd:::\cdot;
    qu:::\quad;
    ti:::\times;
    @a:::\alpha;
    @b:::\beta;
    @g:::\gamma;
    @G:::\Gamma;
    @d:::\delta;
    @D:::\Delta;
    @e:::\epsilon;
    @z:::\zeta;
    @e:::\eta;
    @t:::\theta;
    @T:::\Theta;
    @i:::\iota;
    @k:::\kappa;
    @l:::\lambda;
    @L:::\Lambda;
    @m:::\mu;
    @n:::\nu;
    @x:::\xi;
    @X:::\Xi;
    @p:::\pi;
    @P:::\Pi;
    @r:::\rho;
    @s:::\sigma;
    @S:::\Sigma;
    @t:::\tau;
    @u:::\upsilon;
    @U:::\Upsilon;
    @p:::\phi;
    @P:::\Phi;
    @c:::\chi;
    @p:::\psi;
    @P:::\Psi;
    @o:::\omega;
    @O:::\Omega
    ```

### Quick Switcher++

ファイル移動のプラグイン
デフォルトのQuick Switcherは`Ctrl+O`で開ける

### Recent Files

最近開いたファイルの一覧を表示

### Remotely Save

Onedriveなどのクラウドサービスにデータを保存して同期

- Choose A Remote Service: `OneDrive for Personal`
- Change The Remote Base Directory: `obsidian`
    - 大文字と小文字が区別される？ため
- Schedule For Auto Run: `every 5 minutes`
- Run Once On Start Up Automatically: `1 second`
- Sync On Save: `Enable`
- Sync Bookmarks: `Enable`
- Regex Of Paths To Allow (markdownファイルだけ同期したいとき): `\.md$`

### Thino

Twitterっぽいメモ。タブを固定しておくと良い。

### Zotero Integration

Zoteroと連携

- PDF Utility: `Download`
- Opan the created or updated note(s) after import: `true`
- Which notes to open after import: `All imported notes`
- Import Formats
    - Name: `Create Memo` (任意)
    - Output Path: `{{citekey}}/{{citekey}}.md` (任意)
    - Image Output Path: `{{citekey}}/img` (任意)
    - Image Base Name: `image`
    - Template File: `template.md`
    - Bilbiography Style: `BibTeX generic citation style`

```Markdown:template.md
---
tags:
  - "{{allTags|replace(',','')}}"
  - "Y{{date|format('YYYY')}}"
URL: "{{url}}"
PDF: "[[{{citekey}}.pdf]]"
translation: "al-{{citekey}}.pdf"
dateread: "{{importDate | format('YYYY-MM-DD')}}"
---
> [!info]
> **Title**: {{title}}
> **Authors**: {{authors}} {%- if itemType == "journalArticle" %}
> **Venue**: {{publicationTitle}} {{date | format("YYYY")}} {%- endif %} {%- if itemType == "conferencePaper" %}
> **Venue**: {{conferenceName}} {%- endif %} {%- if itemType == "preprint" %}
> **Venue**: {{repository}} {{date | format("YYYY")}} {%- endif %}

> [!abstract]
> {{abstractNote}}

```

- 詳細: https://medium.com/@alexandraphelan/an-updated-academic-workflow-zotero-obsidian-cffef080addd
- 参考: https://qiita.com/quekto_/items/7aa314bf5fbd764fedc4

# チートシート
## ホットキー

|        機能         |     ショートカット      |
| :---------------: | :--------------: |
|        戻る         | `Ctrl+Alt+←` |
|        進む         | `Ctrl+Alt+→` |
|  カーソル下のリンクを右側に開く  | `Ctrl+Alt+Enter` |
| カーソル下のリンクを新規タブに開く |   `Ctrl+Enter`   |
|    現在のファイルを保存     |     `Ctrl+S`     |
|     新規ノートを作成      |     `Ctrl+N`     |
|    コマンドパレットを開く    |     `Ctrl+P`     |
|   クイックスイッチャーを開く   |     `Ctrl+O`     |
|    すべてのファイルを検索    |  `Ctrl+Shift+F`  |
|     グラフビューを開く     |     `Ctrl+G`     |
|    現在のファイルを検索     |     `Ctrl+F`     |
| 編集とプレビューモードを切り替える |     `Ctrl+E`     |
|    横方向にペインを分割     |     `Ctrl+\`     |
|       設定を開く       |     `Ctrl+,`     |

|      機能       | ショートカット  |
| :-----------: | :------: |
| 選択範囲のボールドを切替  | `Ctrl+B` |
| 選択範囲のイタリックを切替 | `Ctrl+I` |
|   外部リンクを挿入    | `Ctrl+K` |
|     インデント     | `Ctrl+]` |
|    アンインデント    | `Ctrl+[` |
| チェックボックスのトグル  | `Ctrl+L` |

# タブ配置

これは好み

以下を右側に追加
- Google Calendar: `Google Calendar: Open gcal Schedule View`
- Google Tasks: 左側のリボンから`Google Tasks`

# tips

- `Ctrl`を押しながら開くと新しいタブで開く

## 一括変換

https://wineroses.hatenablog.com/entry/2023/08/26/211101

# 参考文献
https://zenn.dev/hk_ilohas/articles/obsidian-plugins-2023
https://qiita.com/Kei_Adachi/items/1f5d01230334574f2e26
https://necco.inc/note/49799