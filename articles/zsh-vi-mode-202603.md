---
title: "zshのbindkey -vって何？"
emoji: "⌨️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["zsh", "vim", "terminal"]
published: true
---

# vi-modeを知る

最近xとか社内でabbrはいいぞ、aliasはやめてabbrにしましたみたいな話を散々聞いたので自分もやってみようかと。
そんな思いでそもそもabbrってなんだろね、みたいな話を調べてたら以下記事を見つけました。

https://girigiribauer.com/tech/20200420/

fishいいね、abbrとりあえず設定しよになったのですが、こんな文言を見つけてしまいました

> vi モードが分かりやすい

なにこれ...？
研修でzshだとemacsのキーバインド使えるんですよーということは知っていたのですが、まさかviキーバインドに対応しているとは...

しかもzshもviキーバインドに対応してるみたいなんですよね

この記事の中では以下のoh-my-zshのvi-mode pluginが紹介されていたのですが、[他の記事](https://nishikawasasaki.hatenablog.com/entry/20101227/1293459255)によると普通にzsh側でviキーバインドに対応してるみたいですね。

https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/vi-mode

今回はこんな大AI時代ですが、zshのvi-modeについて触りつつ紹介していきたいと思います。

# bindkeyコマンド

[この記事](https://nishikawasasaki.hatenablog.com/entry/20101227/1293459255)によると`bindkey -v`でキーバインドをvi-modeにできるそうな。
というわけで`man bindkey`すると以下の記述が。

```
SYNOPSIS
     See the built-in command description in the appropriate shell manual page.
```

というわけで当然zsh使ってるので`man zsh`します。

```
ZSH(1)                                                General Commands Manual                                               ZSH(1)

NAME
       zsh - the Z shell

OVERVIEW
       Because zsh contains many features, the zsh manual has been split into a number of sections:

       zsh          Zsh overview (this section)
       zshroadmap   Informal introduction to the manual
       zshmisc      Anything not fitting into the other sections
       zshexpn      Zsh command and parameter expansion
       zshparam     Zsh parameters
       zshoptions   Zsh options
       zshbuiltins  Zsh built-in functions
       zshzle       Zsh command line editing
       zshcompwid   Zsh completion widgets
       zshcompsys   Zsh completion system
       zshcompctl   Zsh completion control
       zshmodules   Zsh loadable modules
       zshtcpsys    Zsh built-in TCP functions
       zshzftpsys   Zsh built-in FTP client
       zshcontrib   Additional zsh functions and utilities
       zshall       Meta-man page containing all of the above
```

わかりづらいのですが、keybindはZsh command line editingの機能なので`man zshzle`を見てみます。

bindkeyのオプションについてこんなことが書いてありました。

```
 -v     Selects keymap `viins' for any operations by the current command, and also links`viins' to `main' so that it is selected by default the next time the editor starts.
```

つまり`bindkey -v`は`viins`（vi insert mode）キーマップを`main`にリンクするコマンドということですね。
ちなみにzshの公式ドキュメントでは「vi-mode」という用語は使われておらず、`viins`と`vicmd`という2つのキーマップとして提供されていますが、コミュニティやoh-my-zshでは「vi-mode」と呼ばれることが多いのでここでもそれに倣います。

ここまででviキーバインドの入力ができるようになったので実際にどのような操作ができるのかを見ていきます。

# できる操作

`man zshzle`の「STANDARD WIDGETS」セクションにはvi commandモード（`vicmd`）で使えるキーバインドが記載されています。
触ってみてビックリなんですが、本当にviのキーバインドで動くんですよね。

使えるキーバインドを以下表にまとめてみました。
(原文は英語なのでclaude君に訳してもらってます)

## 移動（Movement）— 24個

vimの移動操作がそのまま使えます。zsh独自の操作は多分なさそう。

:::details 全リスト
| キー | ウィジェット名 | 説明 |
| ----------------- | ----------------------------- | ---------------------------------------- |
| `B` | vi-backward-blank-word | 前の空白区切り単語の先頭に移動 |
| `gE` | vi-backward-blank-word-end | 前の空白区切り単語の末尾に移動 |
| `^H` / `h` / `^?` | vi-backward-char | 左に1文字移動（行をまたがない） |
| `b` | vi-backward-word | 前の単語の先頭に移動（vi式） |
| `ge` | vi-backward-word-end | 前の単語の末尾に移動 |
| `$` | vi-end-of-line | 行末に移動 |
| `W` | vi-forward-blank-word | 次の空白区切り単語の先頭に移動 |
| `E` | vi-forward-blank-word-end | 次の空白区切り単語の末尾に移動 |
| `space` / `l` | vi-forward-char | 右に1文字移動 |
| `f` | vi-find-next-char | 行内で次の指定文字に移動 |
| `t` | vi-find-next-char-skip | 行内で次の指定文字の手前に移動 |
| `F` | vi-find-prev-char | 行内で前の指定文字に移動 |
| `T` | vi-find-prev-char-skip | 行内で前の指定文字の手前に移動 |
| `^` | vi-first-non-blank | 行頭の非空白文字に移動 |
| `w` | vi-forward-word | 次の単語の先頭に移動（vi式） |
| `e` | vi-forward-word-end | 次の単語の末尾に移動 |
| `\|` | vi-goto-column | 指定カラムに移動 |
| `` ` `` | vi-goto-mark | 指定マークに移動 |
| `'` | vi-goto-mark-line | 指定マークのある行頭に移動 |
| `;` | vi-repeat-find | 直前のf/t検索を繰り返し |
| `,` | vi-rev-repeat-find | 直前のf/t検索を逆方向に繰り返し |
| `%` | vi-match-bracket | 対応する括弧に移動 |
| `m` | vi-set-mark | マークを設定 |
| `0` | vi-digit-or-beginning-of-line | 行頭に移動（数値引数の先頭なら数字入力） |
:::

## 履歴（History Control）— 12個

このセクションはvimと同じキーが使われていますが、全て履歴操作に置き換わっています。(historyという概念自体がvimにはないので当然ではありますが...)

vimでは`j`/`k`は行移動、`/`/`?`はバッファ内テキスト検索、`G`は行番号ジャンプですが、zshではこれらが全て履歴操作に置き換わっています。

`j`,`k`は行の上下動と前後の履歴への移動, `+`,`-`も似た挙動ですが、こちらはそれに加えて行頭に移動します。
`^N(ctrl+N)`,`^P(ctrl+P)`もまた似ていますが、こちらは行の上下動はなく前後の履歴への移動のみをする形。
`gg`,`G`は通常vimと同様に先頭と末尾への移動、履歴番号を指定しての移動ですが、流石に使い所はなさそうか。

あとは検索系ですが、これはvimと同じ感覚で使えて、`/`,`?`は前後方向の検索ができ、`n`,`N`は繰り返し検索ができます。

:::details 全リスト
| キー | ウィジェット名 | 説明 |
| ---- | ------------------------------ | -------------------------------------- |
| `gg` | beginning-of-buffer-or-history | バッファの先頭、または履歴の最初に移動 |
| `j` | down-line-or-history | 下の行、または次の履歴に移動 |
| `+` | vi-down-line-or-history | 次の履歴に移動し行頭の非空白文字へ |
| `^N` | down-history | 次の履歴に移動 |
| `G` | vi-fetch-history | 指定番号の履歴行に移動 |
| `/` | vi-history-search-backward | 後方の履歴検索 |
| `?` | vi-history-search-forward | 前方の履歴検索 |
| `n` | vi-repeat-search | 直前の履歴検索を繰り返し |
| `N` | vi-rev-repeat-search | 直前の履歴検索を逆方向に繰り返し |
| `k` | up-line-or-history | 上の行、または前の履歴に移動 |
| `-` | vi-up-line-or-history | 前の履歴に移動し行頭の非空白文字へ |
| `^P` | up-history | 前の履歴に移動 |
:::

## テキスト編集（Modifying Text）— 30個

vimとほぼ同じです。zsh独自の操作はありません。

:::details 全リスト
| キー | ウィジェット名 | 説明 |
| ---- | ----------------------- | ---------------------------------------- |
| `A` | vi-add-eol | 行末からinsertモードに入る |
| `a` | vi-add-next | カーソルの後からinsertモードに入る |
| `X` | vi-backward-delete-char | カーソルの前の文字を削除 |
| `c` | vi-change | 移動範囲を削除してinsertモードに入る |
| `C` | vi-change-eol | カーソルから行末まで削除してinsertモード |
| `S` | vi-change-whole-line | 行全体を削除してinsertモード |
| `d` | vi-delete | 移動範囲を削除（`dd`で行全体） |
| `x` | vi-delete-char | カーソル位置の文字を削除 |
| `gu` | vi-down-case | 移動範囲を小文字に変換 |
| `>` | vi-indent | インデント |
| `i` | vi-insert | insertモードに入る |
| `I` | vi-insert-bol | 行頭の非空白文字でinsertモードに入る |
| `J` | vi-join | 現在行と次の行を結合 |
| `D` | vi-kill-eol | カーソルから行末まで削除 |
| `O` | vi-open-line-above | 上に新しい行を開いてinsertモード |
| `o` | vi-open-line-below | 下に新しい行を開いてinsertモード |
| `g~` | vi-oper-swap-case | 移動範囲の大文字/小文字を反転 |
| `P` | vi-put-before | カーソルの前にペースト |
| `p` | vi-put-after | カーソルの後にペースト |
| `R` | vi-replace | 上書きモードに入る |
| `.` | vi-repeat-change | 直前の編集操作を繰り返し |
| `r` | vi-replace-chars | カーソル位置の文字を指定文字に置換 |
| `s` | vi-substitute | カーソル位置の文字を削除してinsertモード |
| `~` | vi-swap-case | カーソル位置の大文字/小文字を切り替え |
| `<` | vi-unindent | アンインデント |
| `gU` | vi-up-case | 移動範囲を大文字に変換 |
| `y` | vi-yank | 移動範囲をコピー |
| `Y` | vi-yank-whole-line | 行全体をコピー |
| `"` | vi-set-buffer | 名前付きバッファを指定 |
| `u` | undo | 操作の取り消し |
| `^R` | redo | undoの取り消し |
:::

## 引数（Arguments）— 1個

vimと同じです。

:::details 全リスト
| キー | ウィジェット名 | 説明 |
| ----- | -------------- | -------------- |
| `1-9` | digit-argument | 数値引数の入力 |
:::

## 補完（Completion）— 2個

全てzsh独自です。

list-choicesはコマンドを途中まで打って、=を入力すると補完候補を一覧表示します。
とはいえ、最近はauto complete等流行ってるのであまり使い所はない気がします。

list-expandは`echo *.md`のようなコマンド実行時に実際に`*.md`がどう展開されるかを表示することができます。
これもいまいち使い所ないかな...

| キー       | ウィジェット名 | 説明               |
| ---------- | -------------- | ------------------ |
| `^D` / `=` | list-choices   | 補完候補を一覧表示 |
| `^G`       | list-expand    | 展開候補を一覧表示 |

## その他（Miscellaneous）— 8個

vimと同じキーが使われていますが、シェルならではの挙動に変わっているものが多いです。
v, Vでビジュアルモード入れるのは嬉しいですね。
他は正直私には使い道があまり思いつきませんでした。（clear-screenはそれなりに使いますね）

:::details 全リスト
| キー | ウィジェット名 | 説明 |
| ----------- | -------------------- | --------------------------------- |
| `^J` / `^M` | accept-line | コマンドを実行 |
| `^L` | clear-screen | 画面をクリアして再描画 |
| `:` | execute-named-cmd | コマンド名を入力して実行 |
| `#` | pound-insert | 行頭に`#`をつけてコメント化し実行 |
| `^R` | redisplay | 表示を再描画（vicmdキーマップでは`redo`） |
| `v` | visual-mode | ビジュアルモードに入る |
| `V` | visual-line-mode | 行単位のビジュアルモードに入る |
| `ga` | what-cursor-position | カーソル位置の文字情報を表示 |
:::

## テキストオブジェクト（Text Objects）— 6個

viopp/visualキーマップのバインドです。`d`/`c`/`y`の後やvisualモード中に使えます。

`select-a-shell-word`（`aa`）/ `select-in-shell-word`（`ia`）はvimには存在しないzsh独自のテキストオブジェクトです。シェル引数（クォートされた文字列を含むケースは`aa`、含まないケースは`ia`）を単位として選択できます。

vimだと`i{`(ブロックの中身を選択)や`a{`(ブロック全体を括弧含めて選択)といったテキストオブジェクトがありますが、zshではこれらがないですね。残念。

:::details 全リスト
| キー | ウィジェット名 | 説明 |
| ---- | -------------------- | ------------------------------------ |
| `aW` | select-a-blank-word | 空白区切り単語+隣接空白を選択 |
| `aa` | select-a-shell-word | シェル引数全体を選択（引用符含む） |
| `aw` | select-a-word | 単語+隣接空白を選択（vi式） |
| `iW` | select-in-blank-word | 空白区切り単語を選択 |
| `ia` | select-in-shell-word | シェル引数の中身を選択（引用符除く） |
| `iw` | select-in-word | 単語を選択（vi式） |
:::

# まとめ

このようにzshのvi-modeはvimと似たキーバインドで操作ができます。
履歴系だったり、visualモードの挙動だったりがzsh独自のものもありますが、それ以外は普段からvim使ってる人間としてはかなり直感的に使えて嬉しいと思いました。
