---
title: "Go Conference 2023 Online の Web サイトにおける工夫ポイント"
emoji: "🎺"
published: true
publication_name: "gophersjp"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["nextjs", "githubpages", "zod", "webp", "frontend"]
---

Go Conference 2023 Online の Web サイト開発チームの一員として携わらせていただいたので、使用した技術や工夫したポイントを書いていこうと思います。

ざっくり言うと Next.js の Page Router で開発して GitHub Pages でホストした静的サイトなので、特殊な技術を使っているとかではないです。また、 Go Conference の話ですが Go 言語は出てきません。

## TL;DR

Go Conference 2023 Online の Web サイト開発において、自分は主に以下の点を工夫しました。

- 🎉 [`react-rewards` の紙吹雪を `setInterval` で繰り返す](https://zenn.dev/taigakiyokawa/articles/20221201-react-rewards-interval)
- 🌏 [`react-i18next` で `<wbr>` を使って日本語の改行箇所を制御する](https://zenn.dev/moneyforward/articles/20221220-react-i18next-wbr)
- ⚡️ [Sessionize](https://sessionize.com/) の API レスポンスは [`zod`](https://zod.dev/) でバリデーションして型安全に扱う
- 📸 メインビジュアルを [WebP](https://developers.google.com/speed/webp?hl=ja) に変換して軽量化する
- 🖼️ PC とモバイルで表示する画像を切替えたい時は `<picture>` タグを使って、描画領域の確保は親要素に `aspect-ratio` を使う
- 📆 タイムテーブルは CSS Grid の `grid-template-colums` を media query で切り替えて、レスポンシブに列数を変える

また、ソースコードは全て公開されています 👇

https://github.com/gocon/2023

出来上がった Web サイトはこちらです 👇

https://gocon.jp/2023

## Web サイト開発メンバー

一緒に Go Conference 2023 Online の Web サイトを開発したメンバーの紹介です。皆さんのおかげで楽しく開発できました。ありがとうございました！

- [maito](https://twitter.com/it_guitar) さん
- [snaka](https://twitter.com/snaka) さん
- [tottie](https://twitter.com/tottie_designer) さん

## 主な使用技術

- `node`: 20.2.0
- `typescript`: 4.8.2
- `react`: 18.2.0
- `next`: 13.4.1
- `@mui/material`: 5.10.7
- `react-i18next`: 11.18.6
- `react-rewards`: 2.0.4
- `zod`: 3.21.4

今回のサイトは Next.js の Page Router を使って構成し、ビルドした静的サイトを GitHub Pages にホストしています。

サイト全体のページ構成は以下のとおりです。

```zsh
gocon.jp/2023/
├── index ... トップページ
├── sessions
│   ├── index ... セッション一覧ページ
│   └── [id] ... 各セッションとスピーカーの紹介ページ
├── timetable ... タイムテーブルページ
├── floor_guide ... オンライン会場の案内、各ブースの紹介ページ
├── sponsors
│   └── [plan]
│       └── [id] ... Platinum "Go"ld, "Go"ld スポンサーの紹介ページ
└── staff ... スタッフ一覧ページ
```

また、セッション情報やスピーカー情報の管理には [Sessionize](https://sessionize.com/) というサービスを利用しています。
Web サイトはビルド時に `getStaticProps` を使って Sessionize の提供する API にリクエストして、各種情報の取得を行っています。

## 工夫したポイント

### 🎉 `react-rewards` の紙吹雪を `setInterval` で繰り返す

トップページのメインビジュアルに合わせて、紙吹雪を良い感じに飛ばしたいと思ったので色々検討しました。

その結果、最も扱いやすかった `react-rewards` を setInterval で繰り返す処理を実装しました。

![Go Conference 2023 Online の Webサイトトップで紙吹雪が繰り返されている様子](https://storage.googleapis.com/zenn-user-upload/bb1a5ca04562-20230610.webp)

10 周年をお祝いする楽しげな雰囲気が演出できて良かったと思います。

実装方法や注意点の詳細は以下の記事にあります。

https://zenn.dev/taigakiyokawa/articles/20221201-react-rewards-interval

ただ一方で、この実装から約半年経って業務でアクセシビリティを勉強したことで、この実装が WCAG 2.2 の「[2.2.2: Pause, Stop, Hide](https://www.w3.org/TR/WCAG22/#pause-stop-hide)」(動き続けるものは停止か非表示ができるようにする) という達成基準を満たしていなさそうなので、めちゃくちゃアンチパターンな実装だったなと気づきました。

アクセシビリティの基礎知識については、デジタル庁が公開している[ウェブアクセシビリティ導入ガイドブック](https://www.digital.go.jp/resources/introduction-to-web-accessibility-guidebook/)が分かりやすいのでこれをひと通り読むのがおすすめです。

### 🌏 `react-i18next` で `<wbr>` を使って日本語の改行箇所を制御する

Go Conference の Web サイトでは `react-i18next` を使った internationalization (i18n) 対応を行っており、ヘッダーの「English」あるいは「日本語」と書かれたボタンから日英の言語切替ができるようになっています。
英文は各単語をスペースで区切るため、画面幅による不自然な折返しが発生しづらい一方で、日本語はこの改行箇所の制御が難しい問題があり、特にトップページで大きく書かれた文章で違和感が起きやすくなります。

そこで、 `react-i18next` で `<wbr>` タグを使用できるように設定して、日本語の文章で改行したい箇所に `<wbr>` を入れることであらゆる画面幅でなるべく自然な改行ができるようにしました。

実装方法の詳細は以下の記事にあります。

https://zenn.dev/moneyforward/articles/20221220-react-i18next-wbr

この設定は頻繁に使用すると開発がしづらくなるので、見出しなどの不自然な改行位置が目立つ文章以外ではなるべく使わない方が良さそうです。

### ⚡️ Zod で Sessionize API のレスポンスをバリデーションして型安全に扱う

今回 Go Conference では [Sessionize](https://sessionize.com/) というサービスを使用して CfP やセッション情報の管理などを行いました。

[Sessionize には API エンドポイントを作成して JSON や HTML を返す機能](https://sessionize.com/playbook/api)があり、 この API にリクエストした情報をもとに Web サイト上にセッション情報やタイムテーブルを掲載しました。

しかし、 Sessionize には OpenAPI Schema などの Schema 定義が見当たらなかったため、 [`zod`](https://zod.dev/) というスキーマバリデーションライブラリを使用してクライアント側で Sessionize API のスキーマを定義してレスポンスを型安全に使用できるようにしました。

https://github.com/GoCon/2023/blob/a0dbd9e12f9d13e3637355d3a405083aea39e432/src/modules/sessionize/schema.ts#L3-L75

`zod` は今回のサイト実装で初めて使ってみたのですが、公式ドキュメントを読むだけでひと通り実装できて非常に使いやすくて良かったです。型がなくて辛い時にはこれを使ってなんとかしてみるのがおすすめです。

### 📸 メインビジュアルを WebP に変換して軽量化

[ウェブ用の画像形式  |  WebP  |  Google for Developers](https://developers.google.com/speed/webp?hl=ja) より

> WebP は、最新の画像形式で、ウェブ上の画像に対して優れたロスレスかつロッシーな圧縮を実現します。WebP を使用すると、ウェブマスターやウェブ デベロッパーは、小さくてリッチな画像を作成してウェブの高速化を実現できます。

WebP は Google が開発している画像形式で、 JPEG や PNG の画質を維持しつつファイルサイズを 20 ~ 30%程度圧縮することができます。ちなみに「ウェブピー」ではなく「ウェッピー」と読むみたいです。

なお、現在では PC / モバイル全てのモダンブラウザで `.webp` 形式の画像を扱うことができます。

Next.js では、 `next/image` で扱う画像を [Image Optimization](https://nextjs.org/docs/pages/building-your-application/optimizing/images) 機能によって自動的に WebP 形式や AVIF 形式に変換してくれたりするのですが、 Static Generation なサイトでは Image Optimization は使用できないため、今回は Google が提供している [`cwebp`](https://developers.google.com/speed/webp/docs/cwebp?hl=ja) というコマンドラインツールを使用して手作業で `.png` を `.webp` 形式に変換しました。

```zsh
cwebp ./public/main-visual/pc.png -o ./public/main-visual/pc.webp
```

その結果、以下のようにメインビジュアルのファイルサイズを大きく減らすことができました。

| Dimensions \ Format | `.png` | `.webp`   |
| :------------------ | :----- | :-------- |
| PC (2880 × 1690)    | 2.6MB  | **294KB** |
| Mobile (748 × 1114) | 804KB  | **117KB** |

今後関わるサイトの画像を全て WebP にしたいくらいには WebP いいぞって感じになりました。ぜひ使ってみてください。

Google 発祥という意味では Go 言語も WebP も親戚みたいなものですね。 (頑張って Go と結びつける)

### 🖼️ `<picture>` タグでレスポンシブに画像を切り替える

今回のサイトでは PC とモバイルの画面幅によってメインビジュアルのソースを切り替える処理を行っていました。当初は viewport の幅が一定以上か否かを判定するカスタムフックを使ったフラグで切り替えを行っていたのですが、ビルド時点では viewport を測ることができないため、初期描画で一瞬レイアウトシフトが発生してしまいました。[レスポンシブ画像 - ウェブ開発を学ぶ | MDN](https://developer.mozilla.org/ja/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images) によると、今回のようにさまざまなレイアウトで異なるトリミング画像を提供したい場合の一般的な問題は「アートディレクション問題」と呼ぶらしいです。

この問題は [`<picture>`](https://developer.mozilla.org/ja/docs/Web/HTML/Element/picture) タグを使って、以下のように PC とモバイルの画像を指定することで解決できます。

```tsx
<picture>
  <source srcSet="./main-visual/mobile.webp" media="(max-width: 599.95px)" />
  <img
    src="./main-visual/pc.webp"
    alt="Go Conference 2023 Online at Friday, June 2"
  />
</picture>
```

しかし、例えば画像自体には具体的な `width` や `height` を指定せずに、画面幅いっぱいで高さは画像によって自動的に決まるように表示したい場合、`<picture>` タグによる画像の切り替えとは別問題として、描画領域の幅と高さが確保されずにレイアウトシフトが起きてしまいます。

そこで、親の div 要素に [`aspect-ratio`](https://developer.mozilla.org/ja/docs/Web/CSS/aspect-ratio) で画像のアスペクト比を指定することで、描画前に事前に高さを確保してレイアウトシフトを防ぎました。同時に `<img>` 要素には以下のプロパティを指定して、表示が崩れないようにしました。

- `width: 100%` ... 親要素いっぱいに画像を表示する
- `hegiht: auto` ... 高さは自動で決める
- `vertical-align: middle` ... インライン要素である `<img>` にデフォルトで設定されている `vertical-align: baseline` を打ち消して、下部分の余分な余白をなくす
- [`object-fit: contain`](https://developer.mozilla.org/ja/docs/Web/CSS/object-fit) ... 画像のアスペクト比を維持する

その結果、以下のようにしてレスポンシブでレイアウトシフトのない画像切り替えを実現しました。

https://github.com/GoCon/2023/blob/a0dbd9e12f9d13e3637355d3a405083aea39e432/src/components/organisms/MainVisual/index.tsx#L64-L85

![画面幅を狭めていって一定の幅未満になるとモバイル用の画像に切り替わる様子](https://storage.googleapis.com/zenn-user-upload/a34e3e94fd60-20230610.webp)

### 📆 CSS Grid でレスポンシブなタイムテーブル

タイムテーブル画面は長らく Sessionize API による HTML を返していて、独自実装は開催ギリギリまで出来上がっていなかったので関係者の皆さんには非常にご不便おかけしました 🙇‍♂️

タイムテーブルの実装は、時間順に並べたオブジェクトの配列を map して CSS Grid レイアウトで並べていくという処理を行いました。

Room A と B という 2 列のタイムテーブルは、 PC などの画面幅が広い場合は問題なく表示できますが、モバイルなどの画面幅が狭い場合には 2 列表示を収めようとすると窮屈になります。というか表示内容的に収まりきらなかったです。

自分自身はじめてタイムテーブル的な UI でレスポンシブデザインが求められるものを実装したので、これをどう実現しようか悩んだのですが、結果カスタムフックで PC 以上の画面幅かどうかを返す boolean によるデータ表示の切り替えと、 media query による CSS Grid レイアウトの [`grid-template-columns`](https://developer.mozilla.org/ja/docs/Web/CSS/grid-template-columns) の切り替えを行うことで、画面幅が一定未満狭くなったら 1 列表示にするという処理を実現しました。

https://github.com/GoCon/2023/blob/a0dbd9e12f9d13e3637355d3a405083aea39e432/src/pages/timetable.tsx#L164

![PC相当の画面幅を狭めていって、一定の幅未満になったら2列のタイムテーブルが1列表示に切り替わる様子](https://storage.googleapis.com/zenn-user-upload/7f3bc8a75b1a-20230610.webp)

また、片方の Room の情報のみが表示されるようになるので、タイムテーブル上にルームの切り替えボタンを設置して、 今表示しているのが Room B か否かを切り替える boolean を `useState` で用意して列に表示する情報を切り替えるという処理を入れました。

![モバイル相当の画面幅で1列表示になっているタイムテーブルで、 Room A の情報と Room B の情報をタイムテーブル上部のボタンを使って切り替える様子](https://storage.googleapis.com/zenn-user-upload/be8b18a06537-20230610.webp)

あとから知ったのですが、テーブルの表示領域に対して画面幅が狭い場合の対処法は [`overflow-x: scroll`](https://developer.mozilla.org/ja/docs/Web/CSS/overflow-x) とかで横スクロールさせるのが一般的みたいです。

## まとめ

Go Conference 2023 Online の Web サイト開発において、自分は主に以下の点を工夫しました。

- 🎉 [`react-rewards` の紙吹雪を `setInterval` で繰り返す](https://zenn.dev/taigakiyokawa/articles/20221201-react-rewards-interval)
- 🌏 [`react-i18next` で `<wbr>` を使って日本語の改行箇所を制御する](https://zenn.dev/moneyforward/articles/20221220-react-i18next-wbr)
- ⚡️ [Sessionize](https://sessionize.com/) の API レスポンスは [`zod`](https://zod.dev/) でバリデーションして型安全に扱う
- 📸 メインビジュアルを [WebP](https://developers.google.com/speed/webp?hl=ja) に変換して軽量化する
- 🖼️ PC とモバイルで表示する画像を切替えたい時は `<picture>` タグを使って、描画領域の確保は親要素に `aspect-ratio` を使う
- 📆 タイムテーブルは CSS Grid の `grid-template-colums` を media query で切り替えて、レスポンシブに列数を変える

また、ソースコードは全て公開されています 👇

https://github.com/gocon/2023

出来上がった Web サイトはこちらです 👇

https://gocon.jp/2023

## 関連 URL

- [Go Conference 2023](https://gocon.jp/2023/)
- [GoCon/2023 | GitHub](https://github.com/gocon/2023)
- [Sessionize](https://sessionize.com/)
- [`react-rewards` の紙吹雪を `setInterval` で繰り返す](https://zenn.dev/taigakiyokawa/articles/20221201-react-rewards-interval)
- [Web Content Accessibility Guidelines (WCAG) 2.2](https://www.w3.org/TR/WCAG22/#pause-stop-hide)
- [ウェブアクセシビリティ導入ガイドブック｜デジタル庁](https://www.digital.go.jp/resources/introduction-to-web-accessibility-guidebook/)
- [react-i18next で日本語の改行箇所を制御したい時は、設定で wbr タグを使えるようにしよう](https://zenn.dev/moneyforward/articles/20221220-react-i18next-wbr)
- [API: Access your data from code - Sessionize Playbook](https://sessionize.com/playbook/api)
- [Zod | Documentation](https://zod.dev/)
- [ウェブ用の画像形式  |  WebP  |  Google for Developers](https://developers.google.com/speed/webp?hl=ja)
- [Optimizing: Images | Next.js](https://nextjs.org/docs/pages/building-your-application/optimizing/images)
- [cwebp  |  WebP  |  Google for Developers](https://developers.google.com/speed/webp/docs/cwebp?hl=ja)
- [レスポンシブ画像 - ウェブ開発を学ぶ | MDN](https://developer.mozilla.org/ja/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images)
- [`<picture>`: 画像要素 - HTML: HyperText Markup Language | MDN](https://developer.mozilla.org/ja/docs/Web/HTML/Element/picture)
- [aspect-ratio - CSS: カスケーディングスタイルシート | MDN](https://developer.mozilla.org/ja/docs/Web/CSS/aspect-ratio)
- [object-fit - CSS: カスケーディングスタイルシート | MDN](https://developer.mozilla.org/ja/docs/Web/CSS/object-fit)
- [grid-template-columns - CSS: カスケーディングスタイルシート | MDN](https://developer.mozilla.org/ja/docs/Web/CSS/grid-template-columns)
