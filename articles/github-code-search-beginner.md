---
title: "github code searchを今更ながら触ってみる"
emoji: "📝"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["github"]
published: true
---

# github code search 使ってますか？

今までの職務上、自分のローカルな環境でのコード検索で十分なケースが多く、あまり github code search を使ったことがありませんでした。

ただ、今回業務の中で複数リポジトリに跨ったレガシーコードを検索したいケースが出てきたので、使い方を備忘録として残します。

## そもそも github code search とは

github code search は github 公式が 2023/05 から一般公開しているコード検索機能です。

https://github.blog/news-insights/product-news/github-code-search-is-generally-available/

以下リンク先に記載がある通り、基本的には以下の場所から利用することができます。

https://docs.github.com/ja/search-github/getting-started-with-searching-on-github/about-searching-on-github#searching-using-a-visual-interface

できることはどれも変わりませんが、検索バーと検索ページは基本的にクエリ一文での検索、高度な検索ページでは GUI ベースで詳細な検索ができるので、何ができるかわからない人はまず高度な検索ページを利用するのが良さそうです。

- GitHub のページ上部にある検索バー
- [検索ページ](https://github.com/search)
- [高度な検索ページ](https://github.com/search/advanced)

## できること

ここからは検索バー、検索ページで利用できるクエリについて触れて行きつつ、どんな検索ができるのかを見て行きます。
内容としては以下ページを参考にしているので、気になる方はそちらをご覧ください。

https://docs.github.com/ja/search-github/github-code-search/understanding-github-code-search-syntax

### 単語検索

一番シンプルな検索は単語ひとつでの検索です。

以下のクエリでは、chakra-ui という単語を含むコード、リポジトリ、issue 等全てを検索対象として検索することができます。

```
chakra-ui
```

![もっともシンプルな検索](/images/github-code-search-beginner/most-simple-search.png)

また、 github code search は複数の単語を指定すると、AND 検索となるため、1 フレーズを表現するためには `"search params"` のようにダブルクオーテーションで囲む必要があります。

### ブール演算子を利用した検索

github code search ではデフォルトの AND 検索に加えて、OR、NOT 検索を利用することができます。

NOT 検索は後述の修飾子を利用した検索で特に有効に利用することができます。

以下のクエリでは、`material-ui` か `chakra-ui` を含むコードで `ThemeProvider` を含むコードを検索することができます。

```
(material-ui OR chakra-ui) ThemeProvider
```

![ブール演算子を利用した検索](/images/github-code-search-beginner/or-search.png)

### 修飾子を限定した検索

github code search では以下の修飾子を利用した検索を行うことができます。
リストアップしてみると以下のように様々な修飾子を利用することができることがわかります。
なお、ここではコード検索用の修飾子についてのみ紹介します。

| 修飾子      | 内容                                           | 具体的な使い方                                          |
| ----------- | ---------------------------------------------- | ------------------------------------------------------- |
| `repo:`     | 特定のリポジトリ内を検索                       | `repo:github/docs`                                      |
| `user:`     | 特定のユーザーのリポジトリ内を検索             | `user:octocat`                                          |
| `org:`      | 特定の組織のリポジトリ内を検索                 | `org:github`                                            |
| `language:` | 特定のプログラミング言語で書かれたコードを検索 | `language:javascript`, `language:python`                |
| `path:`     | 特定のパス内のファイルを検索                   | `path:unit_tests`, `path:"src/main"`                    |
| `symbol:`   | シンボル定義を検索                             | `symbol:WithContext`, `symbol:class:Message`            |
| `content:`  | ファイル内容のみを検索                         | `content:README.md`                                     |
| `is:`       | リポジトリのプロパティでフィルター             | `is:archived`, `is:fork`, `is:vendored`, `is:generated` |

以下のクエリでは、microsoft/TypeScript リポジトリ内の階層を問わずに readme.md ファイルを検索することができます。

```
repo:microsoft/TypeScript path:**/readme.md
```

![修飾子を利用した検索](/images/github-code-search-beginner/decorator-search.png)

ちなみに、こちらでは触れませんが、リポジトリの検索の際にはより細かい検索が可能です。
は以下のページを参考にしてください。

https://docs.github.com/ja/search-github/searching-on-github/searching-for-repositories

### 正規表現

github code search では正規表現を利用することができます。

以下のクエリではハイフン繋ぎの日付のパターンを正規表現で検索することができます。

```
/\d{4}-\d{2}-\d{2}/
```

また、大文字小文字の区別は通常されないため、区別したい場合は正規表現を利用する必要があります。

## 感想

想像していたよりも多機能でかなり使い所はありそうな印象でした。

リポジトリの検索ではさらに細かい条件を指定しての検索ができそうなので、次はその辺りについても調べてみたいです。

## 参考

https://docs.github.com/ja/search-github/github-code-search/understanding-github-code-search-syntax
