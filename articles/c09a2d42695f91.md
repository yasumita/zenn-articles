---
title: "Zotero の自分用メモ"
emoji: "🌟"
type: "tech"
topics: []
published: false
---

# 設定
## General
- Item Pane Header: `Title, Creator, Year` (好みで)
- Automatically take snapshots when creating items from web pages: `false`
- File Renaming
    - Customize Filename Format...: `{{ citationKey }}`
- Automatically tag items with keywords and subhect headings: `false`

## Sync
ログインする
- Sync automatically: `true`
- Sync full-text content: `true`
- Sync attachment files in My Library using ...: `false`
- Sync attachment files in group libraries using Zotero storage: `false`

## Export
- Item Format: `Better BibLaTeX`
- Advanced
    - Link Attachment Base Directory: 任意のディレクトリ
 
## Advanced
- Allow other applications on this computer to communicate with Zotero

# ショートカット
翻訳元: https://www.zotero.org/support/kb/keyboard_shortcuts

## Zotero ライブラリにアイテムを追加
| Function | Windows/Linux | Mac OS |
| --- | --- | --- |
| Zoteroに保存 | `Ctrl`+`Shift`+S | `Cmd`+`Shift`+S |
| 手動で新しいアイテムを作成 | `Ctrl`+`Shift`+N | `Cmd`+`Shift`+N |
| 新しいメモを作成 | `Ctrl`+`Shift`+O | `Cmd`+`Shift`+O |
| インポート | `Ctrl`+`Shift`+I | `Cmd`+`Shift`+I |
| クリップボードからインポート | `Ctrl`+`Shift`+`Alt`+I | `Cmd`+`Option`+`Shift`+I |

## アイテムの編集（infoタブ）
| Function | Windows/Linux | Mac OS |
| --- | --- | --- |
| 作成者の編集時に別の作成者を追加 | `Shift`+`Enter` | `Shift`+`Enter` |
| 要約または追加フィールドを保存           | `Shift`+`Enter` | `Shift`+`Enter` |


## アイテムとコレクションの削除

| 関数 | My Libraryから | Collectionから |
| --- | --- | --- |
| ゴミ箱に移動 | `Del` | `Shift`\+ `Del`/ `Cmd`+`Delete` |
| 確認なしでゴミ箱に移動 | `Shift`\+ `Del`/ `Cmd`+`Delete` | (なし) |
| コレクションから削除 | (なし) | `Del` （最上位項目のみ） |
| コレクションを削除する（アイテムは保持） | `Del` |
| コレクションを削除してアイテムをゴミ箱に移動  | `Shift`\+ `Del`/ `Cmd`+`Delete` |


## 引用と参考文献の作成（クイックコピー）

| 関数  | Windows/Linux | Mac OS |
| ----------------------- | -------------------- | ----------------- |
| 選択したアイテムの引用をクリップボードにコピー | `Ctrl`\+ `Shift`+A   | `Cmd`\+ `Shift`+A |
| 選択した項目をクリップボードにコピー      | `Ctrl`\+ `Shift`+C   | `Cmd`\+ `Shift`+C |


## Zotero ペイン間の移動

| 関数 | Windows/Linux | Mac OS |
| --- | --- | --- |
| フォーカスライブラリ（左）ペイン | `Ctrl`\+ `Shift`+L | `Cmd`\+ `Shift`+L |
| ペインとフィールドを移動 | `Tab`/ `Shift`+`Tab` | `Tab` |
| 情報/メモ/タグ/関連タブを移動| `→`および`←`（または`Ctrl`\+ `Tab`/ `Ctrl`\+ `Shift`+`Tab`または`Ctrl`\+ `PgUp`/ `Ctrl`\+ `PgDn`） | `Ctrl`\+ `PgUp`/ `Ctrl`\+ `PgDn` |
| クイック検索 | `Ctrl`\+ `Shift`+K / `Ctrl`+F | `Cmd`\+ `Shift`+K / `Cmd`+F |
| アイテムが属するコレクションを検索/強調表示 | `Ctrl`（Windows）または`Alt`（Linux）を押し続ける | `Option`を押し続ける |

## タグ

| 関数 | Windows/Linux | Mac OS |
| --- | --- | --- |
| タグセレクターを切り替え | `Ctrl`\+ `Shift`+T | `Cmd`\+ `Shift`+T |
| アイテムに色付きタグを割り当て | 1〜6のキー | 1〜6のキー |

## フィード

| 関数 | Windows/Linux | Mac OS |
| --- | --- | --- |
| すべてのフィード項目を既読/未読としてマーク | `Ctrl`\+ `Shift`+R | `Cmd`\+ `Shift`+R |
| フィードを既読/未読としてマーク | `Ctrl`\+ `Shift`+\` | `Cmd`\+ `Shift`+\` |

## その他

| 関数 | Windows/Linux | Mac OS |
| --- | --- | --- |
| コレクションまたはアイテムリストを展開/折りたたむ | `+`/`-` | `+`/`-` |
| アイテムが含まれるすべてのコレクションを強調表示 | `Ctrl` （Windows）または`Alt`（Linux）を押し続ける | `Option`を押し続ける |
| アイテムをカウント（結果は右側のペインに表示される） | `Ctrl`+A | `Cmd`+A |
| コレクション名の編集 (左ペイン) | `F2` | (なし) |

## Zotero Connector
- Mac: `Ctrl`+`Cmd`+Z

# プラグイン
- Attanger
    - Root Directory: Link Attachment Base Directoryと同じ
    - Destination Path
        - Root Directory:  Link Attachment Base Directoryと同じ
        - Subfolder: `{{ citationKey }}`
    - Automatically delete empty folders after moving: `true`
- Better Bibtex for Zotero
    - Citation key formula: `auth.lower + year + veryshorttitle.lower`
        - 記法は[ドキュメント](https://retorque.re/zotero-better-bibtex/citing/index.html)を参考
    - Export -> Fields
        - Fields to omit from export: `abstract, langid, keywords, file, urldate, primaryclass`

# tips
## ランダムに論文を１つ選ぶ
ツール→開発者→Run JavaScript
以下を実行
```JavaScript
var ZoteroPane = Zotero.getActiveZoteroPane();
var currentCollection = ZoteroPane.getSelectedCollection();
var items = currentCollection.getChildItems();
var i = Math.floor(Math.random() * items.length);
var item = items[i]
item._displayTitle
```

# 参考
https://note.com/sdeso/n/n013952313c1b
https://qiita.com/Yarakashi_Kikohshi/items/8f720643543ba175f7cc