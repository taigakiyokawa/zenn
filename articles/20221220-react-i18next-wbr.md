---
title: "react-i18next で日本語の改行箇所を制御したい時は、設定で wbr タグを使えるようにしよう"
emoji: "🐷"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["i18n", "reacti18next", "react", "html", "frontend"]
published: false
---

この記事は [Money Forward Engineering 2 Advent Calendar 2022](https://qiita.com/advent-calendar/2022/moneyforward) の 20 日目の投稿です 🎄
19 日目は [keyskey](https://qiita.com/Keyskey) さんによる『[Kotlin × jOOQ × KMS で実現する実践的なデータ暗号化](https://qiita.com/Keyskey/items/324d38a310d631025003)』でした。

---

`react-i18next` を使用し i18n に対応した文章の言語切替が可能なサイトにおいて、日本語の改行箇所を制御したい時の設定方法についての紹介です。

## 🙈 TL;DR

- `wbr` タグでテキストの任意の箇所で改行を指定できる
- `wbr` を `word-break: keep-all;` と `overflow-wrap: break-word;` と組み合わせることで表示領域に対して改行したい chunk で折り返すことができる
- `react-i18next` の設定ファイルで `i18next.options.react` の **`transKeepBasicHtmlNodesFor` に `wbr` を追加する**ことで、Trans コンポーネントで文中の `<wbr/>` を HTML タグとして保持することができる
- `wbr` による改行制御はやや過剰なのでごく限られたケースにのみ使うのが良さそう

実装例 👇

@[codesandbox](https://codesandbox.io/embed/great-carlos-cp07dz?fontsize=14&hidenavigation=1&theme=dark)

---

こんにちは、マネーフォワードのエンジニアの [taigakiyokawa](https://twitter.com/taigakiyokawa) です。[1 枚目の 11 日目にも書きました](https://zenn.dev/moneyforward/articles/20221211-fragment-colocation)が、2 枚目のアドベントカレンダーが空いていたので急遽参戦してみました。
先日ついに M-1 グランプリ 2022 が幕を閉じました。ウエストランドさん優勝おめでとうございます 🎉 今年も全組最高に面白かったです。特に決勝 1st Round の[ヨネダ 2000](https://youtube.com/watch?v=m9WzUSyOZYE&si=EnSIkaIECMiOmarE) と敗者復活戦の[ヤーレンズ](https://youtube.com/watch?v=XSQ_5e90ZTY&si=EnSIkaIECMiOmarE)が最高でした。

さて、今回は `react-i18next` を使用した i18n 対応によって定数管理された日本語の文章の改行位置を制御する方法について紹介していきます。

## 🐚 日本語の改行を良い感じにしたい

例えば レスポンシブな Web サイトを考慮する時、PC では 1 行で表示しているものをモバイルでは 2 行にしたいといった文字組みを変化させたいケースが言語問わずよくあります。
この時、css によって特に工夫もせずに表示領域内で折り返すようにすることが可能ですが、日本語の場合は英語とは異なり、文中の各単語や文節をスペースで区切らないため、適切な chunk で機械的に改行することが難しいです。そのため、単語の途中や中途半端な区切りでの改行などが発生し読みづらくなることがよくあります。記事の本文や長文では比較的気にならないことが多いですが、見出しやキャッチコピーなどの目立つ文章では中途半端な箇所で折り返されていると非常に気になります。これを単語ごとや丁度良い感じの chunk で改行を制御したいと思います。

### 日本語をキレイに改行する方法: `word-break: keep-all;` + `overflow-wrap: break-word;` + `<wbr>`

`word-break: keep-all;` で日本語テキストの自動改行を禁止して `<wbr>` で任意の改行箇所を指定、ただし指定した改行箇所までの chunk が表示領域を越える場合は自動的に改行が行われるように `overflow-wrap: break-word;` を使う、という合わせ技です。

以下の記事を参考にしています 👇
https://qiita.com/tamanyan/items/e37e76b7743c59235995#%E8%A7%A3%E6%B1%BA%E7%AD%962-word-break-keep-all--overflow-wrap-break-word--wbr

#### 各要素の解説

- `word-break: keep-all;`
  - [word-break - CSS: カスケーディングスタイルシート | MDN](https://developer.mozilla.org/ja/docs/Web/CSS/word-break)
    > CJK テキストの改行を許可しません。 CJK 以外のテキストについては normal と同じ挙動となります。
- `overflow-wrap: break-word;`
  - [overflow-wrap - CSS: カスケーディングスタイルシート | MDN](https://developer.mozilla.org/ja/docs/Web/CSS/overflow-wrap)
    > `anywhere` の値と同様に、行内にその他の分割可能な位置がない場合、通常は分割可能でない単語が任意の場所で分割されますが、コンテンツの最小固有寸法を計算する時に、単語分割によって導入された折り返し可能位置が考慮されません。
- `<wbr>`
  - [<wbr> - HTML: HyperText Markup Language | MDN](https://developer.mozilla.org/ja/docs/Web/HTML/Element/wbr)
    > **HTML の `<wbr>` 要素**は、改行可能位置 — テキスト内でブラウザーが任意で改行してよい位置を表しますが、この改行規則は必要のない場合は改行を行いません。

## 🌎 一方で文章を定数管理して、 i18n 対応もしたい

日本語の改行が `<wbr>` で改行箇所を指定することで任意の折返しができることが分かった一方で、文章の文字列情報そのものは json 等で管理して、 i18n (internationalization) 対応ができるようにしたいというケースもあると思います。具体的には React 向けの i18n フレームワークである [`react-i18next`](https://react.i18next.com/) を使った場合を想定しています。この場合、インラインタグである `<wbr>` は使用できないように見えます。

そこで `react-i18next` で i18n 対応をしつつ日本語の文章は改行箇所を制御できるようにする方法について紹介していきます。つまりここから本題です。

## 🌟 `react-i18next` のセットアップ

`react-i18next` のセットアップをしていきます。

https://react.i18next.com/guides/quick-start#configure-i18next

```ts:i18n/config.ts
import i18n from "i18next";
import { initReactI18next } from "react-i18next";

const resources = {
  ja: {
    translation: {
      hello: "こんにちは、私は taigakiyokawa です。",
    },
  },
  en: {
    translation: {
      hello: "Hello, I'm taigakiyokawa.",
    },
  },
} as const;

i18n.use(initReactI18next).init({
  resources,
  lng: "ja",
  interpolation: {
    escapeValue: false, // react already safes from xss
  },
});

export default i18n;
```

## 🐕 `react-i18next` の使い方

### `useTranslation` hooks で使う

`useTranslation` の返り値である `t` 関数を呼び出すことで使用することができます。通常のケースの場合はこちらの手法を使いますが今回は使用しません。

```tsx:App.tsx
import "./i18n/config";
import { useTranslation } from "react-i18next";

export default function App() {
  const { t } = useTranslation();

  return (
    <div className="App">
      <h1 className="header">
        {t("hello")}
      </h1>
    </div>
  );
}
```

### `Trans` コンポーネントで使う

`Trans` は、文中のインラインタグ (`<strong>` や `<a>` など) の補間などを行うコンポーネントです。基本的には上記の `useTranslation` で事足りますが、今回は文中に改行可能位置を指定したいのでこちらを使用します。

```tsx:App.tsx
import { Trans } from "react-i18next";
import "./i18n/config";

export default function App() {
  return (
    <div className="App">
      <h1 className="header">
        <Trans i18nKey={"hello"} />
      </h1>
    </div>
  );
}
```

例えば resources で管理している文章の中に `<br/>` と記述すると、そのまま br タグとして認識されて改行されます。

```diff ts:i18n/config.ts
const resources = {
  ja: {
    translation: {
-      hello: "こんにちは、私は taigakiyokawa です。"
+      hello: "こんにちは、<br/>私は taigakiyokawa です。"
    },
  },
```

![Comparison between the sentence without br and the one with br](https://storage.googleapis.com/zenn-user-upload/554a80aebbf0-20221220.png)

https://react.i18next.com/latest/trans-component

## 🐟 `react-i18next` で `wbr` タグを使う

### `transKeepBasicHtmlNodesFor` オプションに `wbr` を追加

`react-i18next` のオプション [`transKeepBasicHtmlNodesFor`](https://react.i18next.com/latest/trans-component#usage-with-simple-html-elements-like-less-than-br-greater-than-and-others-v10.4.0) によって、保持したい DOM ノードの種類を指定することができます。つまりこのオプションに `wbr` を指定してあげることで、文中の `<wbr/>` をそのままノードとして保持してくれるようになります。

```diff ts:i18n/config.ts
 i18n.use(initReactI18next).init({
  ...
+ react: {
+   transKeepBasicHtmlNodesFor: ["wbr"],
+ },
 });
```

:::message
デフォルトで `['br', 'strong', 'i', 'p']` が指定されているため、 `['wbr']` のみだと `<br/>` などが保持されなくなってしまうのでご注意ください。
:::

### 定数管理している文章の任意の位置に `<wbr/>` を入れる

以下のような文章を `wbr` あり/なしで比較してみます。

```diff ts:i18n/config.ts
const resources = {
  ja: {
    translation: {
-     oppositeOfPorkIsSalmon: "ブタの逆はシャケだぜ。ブタはゴロゴロした生活だがシャケは流れに逆らって川をのぼるッ！"
+     oppositeOfPorkIsSalmon: "ブタの逆は<wbr/>シャケだぜ。<wbr/>ブタはゴロゴロした生活だが<wbr/>シャケは流れに逆らって<wbr/>川をのぼるッ！"
    },
  },
```

```tsx:App.tsx
import { Trans } from "react-i18next";
import "./i18n/config";

export default function App() {
  return (
    <div className="App">
      <h1 className="header">
        <Trans i18nKey={"oppositeOfPorkIsSalmon"} />
      </h1>
    </div>
  );
}
```

![Comparison between the sentence without wbr and the one with wbr](https://storage.googleapis.com/zenn-user-upload/26c5d81ce402-20221220.png)

`wbr` が適用されていることが確認できます。中途半端な箇所で折り返されてしまっている前者に比べて後者の方がキレイに改行されていて、スッキリしていますね。

ただ、ここまで書いておいてなんですが、やはり `wbr` による改行制御はやや過剰なので、よほど改行位置が気になる箇所やキレイに目立たせたい見出しなど、ごく限られたケースにのみ使用することをおすすめします。

## 🍅 まとめ

- `wbr` タグでテキストの任意の箇所で改行を指定できる
- `wbr` を `word-break: keep-all;` と `overflow-wrap: break-word;` と組み合わせることで表示領域に対して改行したい chunk で折り返すことができる
- `react-i18next` の設定ファイルで `i18next.options.react` の **`transKeepBasicHtmlNodesFor` に `wbr` を追加する**ことで、Trans コンポーネントで文中の `<wbr/>` を HTML タグとして保持することができる
- `wbr` による改行制御はやや過剰なので、ごく限られたケースにのみ使うのが良さそう

---

最後までお読みいただきありがとうございました。明日 12 日目は[ちーず](https://qiita.com/cheez921)さんです！お楽しみに〜 👋🏻 🎅🏻
https://qiita.com/advent-calendar/2022/moneyforward

---

## 🔗 参考リンク

- [Introduction - i18next documentation](https://www.i18next.com/)
- [Trans Component - react-i18next documentation](https://react.i18next.com/latest/trans-component#i18next-options)
- [Web ブラウザの日本語改行問題 -改行を実現する HTML/CSS-（1） - Qiita](https://qiita.com/tamanyan/items/e37e76b7743c59235995)
- [word-break - CSS: カスケーディングスタイルシート | MDN](https://developer.mozilla.org/ja/docs/Web/CSS/word-break)
- [overflow-wrap - CSS: カスケーディングスタイルシート | MDN](https://developer.mozilla.org/ja/docs/Web/CSS/overflow-wrap)
- [<wbr> - HTML: HyperText Markup Language | MDN](https://developer.mozilla.org/ja/docs/Web/HTML/Element/wbr)
