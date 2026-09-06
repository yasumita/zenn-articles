---
title: "LaTeXについての自分用メモ"
emoji: "⛳"
type: "tech"
topics: []
published: false
---

# LaTeX

## amsthm

### 基本的な使い方

- プリアンブルの`\usepackage{amsmath}` \*\*\*\*の後に`\usepackage{amsthm}`
- 定理のコマンドを指定

```latex
\theoremstyle{定理のスタイル}
\newtheorem{コマンド名}{出力する定理名}
\newtheorem*{コマンド名}{出力する定理名} % 番号無し
```

| 定理スタイル | 詳細                                             |
| ------------ | ------------------------------------------------ |
| plain        | 見出し太字・本文斜体・上下にスペース(デフォルト) |
| definition   | 見出し太字・本文普通・上下にスペース(基本これ)   |
| remark       | 見出し斜体・本文普通                             |

本文中では以下のように(コメントはなくてもよい)

```latex
\begin{thm}[コメント]
  hogehoge
\end{thm}
```

### 定理番号

#### セクション番号を付与

```latex
\newtheorem{コマンド名}{定理名}[付与するセクションの単位]
```

#### 別の定理環境でも連番にする

- セクション番号は先のコマンドに依存

```latex
\newtheorem{元のコマンド名}[先のコマンド名]{定理名}
```

#### 定理番号を先に出力

```latex
\swapnumbers
```

#### **定理の番号を途中からはじめる**

該当する定理が出てくる前に

```latex
\setcounter{コマンド名}{番号}
```

#### **定理の番号様式の変更**

```latex
\renewcommand{\thethm}{\Alph{コマンド名}} % アルファベット
\renewcommand{\thethm}{\Roman{コマンド名}} % ローマ数字
```

### 定理のスタイルを自作

```latex
\newtheoremstyle{}% スタイル名
    {}% 上部スペース
    {}% 下部スペース
    {\normalfont}% 本文フォント(\itshape:斜体，\bfseries:太字．\normalfont:普通
    {12pt}% 1行目のインデント量(\parindent:普通の段落)
    {}% 見出しフォント
    {.}% 見出し後の句読点
    { }% 見出し後のスペース(\newline:改行)
    {\thmname{#1}\thmnumber{ #2}\thmnote{ (#3)}}% 見出しの書式
```

### 環境を自作

例:

```latex
\newenvironment{コマンド名}[引数の数]
{ % はじめ
\begin{tcolorbox}[
    enhanced,
    boxrule=0pt,
    arc=0mm,
    frame hidden,
    borderline west={2pt}{-4pt}{red},
    breakable = true
    ]
    \begin{theorem}[#1]
}
{ % おわり
	\end{theorem}\end{tcolorbox}
}
```

## tcolorbox

### プリアンブル

library を読み込むには

```latex
\tcbuselibrary{名前}
```

### tcolorbox の作成

```latex
\begin{tcolorbox}[
	adjusted title=..., % タイトル
	width=..., % 幅
	height=..., % 高さ
	colframe=..., % boxのフレームの色
  colback=..., % box本体の背景色
  colbacktitle=..., % タイトルの背景色
  coltitle=..., % タイトルの文字色
  coltext=..., % 本文の文字色
	fonttitle=..., % タイトルのフォント
	breakable, % ページまたぎに対応 (breakableを使用)
	enhanced, % skinの変更 (skinsを使用)
	(drop shadow, drop fuzzy shadow, drop lifted shadow), % いずれかで影をつける (enhancedにしてから
	sharp corners(=northwest etc.), arc=...,% 角の変更(変更する角を指定可)
	attach boxed title to (top center(={yshift*=<length>}) etc.), % タイトルの独立 (位置を調整可)(enhancedしてから)
	boxsep=..., top=..., bottom=..., left=..., right=..., % 本文とフレームの間のスペース
	boxrule=..., % 枠線の太さ
	frame hidden, % 枠線を消す (enhancedにしてから)
	borderline (方角)={幅？}{幅？}{色}
]
内容
\tcblower % パートに分割
\end{tcolorbox}
```

### インラインで作成

```latex
\tcbox[box align=高さ(center, base, etc.)]{内容}
```

### オプションを適用

```latex
\tcbset{オプション}
```

### 新しい tcbox

```latex
\newtcbox{\mybox}[1][]{オプション, #1}
```

### 新しい tcolorbox

```latex
\newtcolorbox{mybox}{オプション}
```

## proof

### 基本

```latex
\begin{proof}[<comment>]
	...
\end{proof}
```

### 証明の初め

```latex
\renewcommand{\proofname}{\textbf{証明}}
```

または

```latex
\makeatletter
\renewenvironment{proof}[1][\proofname]{\par
  \pushQED{\qed}%
  \normalfont \topsep6\p@\@plus6\p@\relax
  \trivlist
  \item\relax
  {\bfseries % ここを変えた
  #1\@addpunct{.}}\hspace\labelsep\ignorespaces
}{%
  \popQED\endtrivlist\@endpefalse
}
\makeatother
```

### 証明終わりの記号

```latex
\qedhere
```

