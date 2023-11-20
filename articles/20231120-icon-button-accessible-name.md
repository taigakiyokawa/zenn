---
title: "アイコンボタンのアクセシブルな名前はボタンが持つべきかアイコンが持つべきか"
emoji: "🍕"
published: true
publication_name: "moneyforward"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["accessibility", "アクセシビリティ", "html", "waiaria", "wcag"]
---

本記事は`<svg>`要素のみを持つ`<button>`要素（アイコンボタン）にアクセシブルな名前（accessible name）を持たせる方法について調査した結果と、WCAG 2.2の[Success Criterion 1.1.1 Non-text Content](https://www.w3.org/TR/WCAG22/#non-text-content)に関する私見をまとめたものです。

## 結論

- アイコンボタンの非テキストコンテンツは装飾ではなく意味を持つ画像なので、ボタンではなくアイコン画像自体にアクセシブルな名前を持たせるべきだと考えます
- 一方で、非テキストコンテンツの捉え方によってはボタンにアクセシブルな名前を持たせても良さそうですが、`<img>`要素や`<svg>`要素など様々な種類のアイコン画像の実装を想定した場合、やはりボタンにアクセシブルな名前を持たせない方針に倒す方がシンプルだと思います
- `<svg>`要素のみを持つ`<button>`要素にアクセシブルな名前を持たせる4つの方法を比較した結果、現状では`<svg>`要素に`role="img"`と`aria-label`を設定する方法が妥当と考えます

もし他の解釈や観点、ご指摘等ございましたらぜひコメントをお願いします。

## 想定する読者

- HTMLとWAI-ARIAの基礎知識がある方
- アクセシビリティに興味がある方
- WCAGについて既に固有の解釈を持つ方（ご意見いただきたいです）
- アクセシビリティの専門家の方（ご意見いただきたいです）

## きっかけ

以下の[記事へのコメント](https://zenn.dev/link/comments/5f316a752db46c)について、SVGアイコンボタンにアクセシブルな名前を付ける方法は記事中の例とコメントの例のどちらがより良いか、あるいは更に良い方法はないか、社内Slackで相談を受けて調査していました。

> 1つめに関しては、wozittoさんと認識はあっていると思うのですが、その上で、「SVGアイコンボタン」の例は誤りではないですが、ベストとも言えないのではないでしょうか？
> SVGは画像なわけですから、以下のコードのように画像の代替テキストを提供するのがセマンティックスからは筋ではあるのかなと思うのです。
>
> ```html
> <button>
>  <svg role="img" width="24" height="24" viewBox="0 0 24 24">
>     <title>閉じる</title>
>     <path d="M0 0h24v24H0z" fill="none"/>
>     <path d="M6 6l12 12M6 18L18 6" stroke="#000" stroke-width="2"/>
>   </svg>
> </button>
> ```

https://zenn.dev/moneyforward/articles/b5c9b060cf9237

## 大前提

- ボタンに可視テキストを持たせられるのであればそれがベスト
- UIの名前や役割はWAI-ARIAで補完するのではなく、なるべくHTMLのセマンティクスを利用すべき（ですが、今回出すパターンはどれもWAI-ARIAによる補完を少なからず行っています）
- アクセシブルな名前は様々なブラウザとスクリーンリーダーの組み合わせで期待通りに読み上げられるべき（ですが、今回は都合上Mac VoiceOverとGoogle Chrome, Safariのみで検証しています）

## SVGアイコンボタンにアクセシブルな名前を持たせる方法

以下のような`<svg>`要素のみを持つ`<button>`要素にアクセシブルな名前（accessible name）を持たせる方法は、主に以下の4パターンあると考えます。

```html
<button type="button">
  <svg width="24" height="24" viewBox="0 0 24 24">
    <path d="M0 0h24v24H0z" fill="none"/>
    <path d="M6 6l12 12M6 18L18 6" stroke="#000" stroke-width="2"/>
  </svg>
</button>
```

1. `<button>`要素に`aria-label`を付与し、`<svg>`要素に`aria-hidden="true"`を付与する
2. `<button>`要素内にVisually Hiddenなクラスを持たせた`<span>`要素などでテキストを入れて、`<svg>`要素に`aria-hidden="true"`を付与する
3. `<svg>`要素に`role="img"`を付与し、`<svg>`要素内に`<title>`要素を入れて代替テキストを設定する
4. `<svg>`要素に`role="img"`と`aria-label`を付与する

各方法のデモは以下の通りです。

@[codepen](https://codepen.io/taigakiyokawa/pen/xxMPLKg?default-tab=html,result)

以下では、サブセクションごとに各方法の特徴や問題点、私見を記述していきます。

### 1. `<button>`要素に`aria-label`を付与し、`<svg>`要素に`aria-hidden="true"`を付与する

調査のきっかけとなった記事で当初紹介されていた方法（現在は4の方法に修正済）です。

```html
<button type="button" aria-label="閉じる">
  <svg aria-hidden="true" width="24" height="24" viewBox="0 0 24 24">
    <path d="M0 0h24v24H0z" fill="none"/>
    <path d="M6 6l12 12M6 18L18 6" stroke="#000" stroke-width="2"/>
  </svg>
</button>
```

:::message
私見：この方法は`<svg>`要素のアイコン画像にアクセシブルな名前を持たせていないため、実装としては非テキストコンテンツ（`<svg>`要素）を装飾とみなしていると解釈します。しかし、視覚的にはこのアイコンボタンが「閉じる」を意味するかどうかはアイコン画像を見て識別することができるため、アイコン画像は装飾ではなく意味を持つ画像として扱うべきだと考えます。

また、アイコン画像が`<img>`要素だった場合、`<img>`要素に`alt`を付与する方法がベストで`<button>`要素に`aria-label`を付与することはないので、アイコン画像が`<img>`か`<svg>`かで実装が分かれてしまうことを避けるためにも、ボタンにアクセシブルな名前を持たせる方法は避けた方が良いと思います。
:::

ちなみに、ZennやSlackなどのSVGアイコンボタンもこの方法を採用しています。

![ZennのハートマークのみのSVGアイコンボタンにフォーカスしてブラウザのDeveloper Toolsで検証した画面で、button要素にaria-label="いいね"が付与されている](https://storage.googleapis.com/zenn-user-upload/48fb2086f1e4-20231118.png)

### 2. `<button>`要素内にVisually Hiddenなクラスを持たせた`<span>`要素などでテキストを入れて、`<svg>`要素に`aria-hidden="true"`を付与する

[Ameba Accessibility Guidelines](https://a11y-guidelines.ameba.design/1/1/1/)などで推奨されている方法です。

```html
<button type="button">
  <span class="visually-hidden">閉じる</span>
  <svg aria-hidden="true" width="24" height="24" viewBox="0 0 24 24">
    <path d="M0 0h24v24H0z" fill="none"/>
    <path d="M6 6l12 12M6 18L18 6" stroke="#000" stroke-width="2"/>
  </svg>
</button>
```

```css
/* Properties are copied from https://tailwindcss.com/docs/screen-readers */
.visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border-width: 0;
}
```

Visually Hiddenとは、`display: none`や`visibility: hidden`などの[Accessibility Object Model (AOM)](https://wicg.github.io/aom/)からも無視されてしまうCSSプロパティを使わずに、要素を視覚的に隠しつつアクセシブルな名前を提供する方法の通称です。

この方法は`aria-label`の使用を避ける方法ですが、`<svg>`要素に`aria-hidden`を使用しているため、WAI-ARIAによる補完が行われています。

Visually Hiddenはブラウザによっては、widthやheightの値によっては要素を無視することがあるそうです。^[[書籍『Webアプリケーションアクセシビリティ―今日から始める現場からの改善』](https://webapp-a11y.com/) 第2章 Webアクセシビリティの基礎 > 2.3 非テキストコンテンツのマシンリーダビリティ > よくある事例を改善する > [事例2の改善]UIにアクセシブルな名前を付与する 内の記述を参照。] ただ、少なくともMac VoiceOverを使用した検証においては、Google ChromeとSafariのどちらも期待通りに読み上げられました。

ちなみに、[SmartHR UI](https://github.com/kufu/smarthr-ui/)では、`Icon`コンポーネントに`alt`を設定すると[`VisuallyHiddenText`](https://github.com/kufu/smarthr-ui/blob/aa0a7353a085b4234a1b19a70328fb8e640e8ce1/src/components/VisuallyHiddenText/VisuallyHiddenText.tsx)という`<span>`タグのコンポーネントとともにアイコンを描画する仕組みを採用しているようです。

:::message
私見：この方法も1と同様に非テキストコンテンツ（`<svg>`要素）を装飾とみなしていると解釈します。この方法は、インラインの`<svg>`要素のために使うよりかは、Ameba Accessibility Guidelinesの例のようにクラスだけ指定されている空の`<span>`要素や、SmartHR UIのように外部のアイコンライブラリ（[react-icons](https://github.com/react-icons/react-icons)など）を使う場合に有用な印象です。

また、好みの問題としてややハック気味に感じるためこの方法はなるべく使いたくはありませんが、SmartHR UIのIconコンポーネントのように、利用者にVisually Hiddenを意識させない仕組みが提供できるとハック感が薄れるので良さそうです。
:::

### 3. `<svg>`要素に`role="img"`を付与し、`<svg>`要素内に`<title>`要素を入れて代替テキストを設定する

調査のきっかけとなった[記事へのコメント](https://zenn.dev/link/comments/5f316a752db46c)で提示されていた方法です。

```html
<button type="button">
  <svg role="img" width="24" height="24" viewBox="0 0 24 24">
    <title>閉じる</title>
    <path d="M0 0h24v24H0z" fill="none"/>
    <path d="M6 6l12 12M6 18L18 6" stroke="#000" stroke-width="2"/>
  </svg>
</button>
```

Accessibility Object Modelにおける`<svg>`要素のRoleプロパティの値はブラウザによって異なるため、`role="img"`を付与することで、Roleプロパティをimgに固定しています。^[前掲書 > 同章 > 同節 > 同項 > [事例1の改善①]画像に代替テキストを付与する 内の記述を参照。]

:::message
私見：`<svg>`要素内に代替テキストを持たせていることから、アイコン画像は単なる装飾ではなく意味を持つ画像であり、代替テキストを持つべきという意図を持っていると解釈します。
:::

この方法は2と同様に`aria-label`の使用を避けることができる方法ですが、`svg`要素に`role='img'`を与えているためWAI-ARIAによる補完は行われています。

また、インタラクティブな要素内の`<svg>`要素の`<title>`要素は、ブラウザとスクリーンリーダーの組み合わせによっては読み上げられないことがあります。（参考：[Contextually Marking up accessible images and SVGs | scottohara.me](https://www.scottohara.me/blog/2019/05/22/contextual-images-svgs-and-a11y.html#:~:text=SVGs%20within%20links%20and%20buttons)）
実際にMac VoiceOverを使用した検証において、Google Chromeでは問題なく読み上げられましたが、Safariでは読み上げられませんでした。

| Google Chrome | Safari |
| ---- | ---- |
| ![Google Chromeで開いているcodepenのデモをMacのVoiceOverで読み上げて、「閉じる, button, group」と出力されている](https://storage.googleapis.com/zenn-user-upload/e09b3d26c219-20231118.png) | ![Safariで開いているcodepenのデモをMacのVoiceOverで読み上げて、「button」とだけ出力されている](https://storage.googleapis.com/zenn-user-upload/778f0bed1fd0-20231118.png) |

### 4. `<svg>`要素に`role="img"`と`aria-label`を付与する

調査の結果、最良だと思った方法。実際に社内での相談においてもこの方法を推奨し、きっかけとなった記事のコメントへの返信もそのように結論づけています。

```html
<button type="button">
  <svg role="img" aria-label="閉じる" width="24" height="24" viewBox="0 0 24 24">
    <path d="M0 0h24v24H0z" fill="none"/>
    <path d="M6 6l12 12M6 18L18 6" stroke="#000" stroke-width="2"/>
  </svg>
</button>
```

この方法は3とは異なり、少なくともMac VoiceOverを使用した検証においては、Google ChromeとSafariのどちらも期待通りに読み上げられました。

:::message
私見：`<svg>`要素は装飾ではなく意味を持つ画像であるという意図は3と同じです。
:::

### どの方法が妥当か

1. `<button>`要素に`aria-label`を付与し、`<svg>`要素に`aria-hidden="true"`を付与する
2. `<button>`要素内にVisually Hiddenなクラスを持たせた`<span>`要素などでテキストを入れて、`<svg>`要素に`aria-hidden="true"`を付与する
3. `<svg>`要素に`role="img"`を付与し、`<svg>`要素内に`<title>`要素を入れて代替テキストを設定する
4. `<svg>`要素に`role="img"`と`aria-label`を付与する

アイコンボタンのアイコンは単なる装飾ではなく意味を持つ画像であると解釈しているため、アイコン画像（`<svg>`要素）自体にアクセシブルな名前を持たせる3か4の方法が良いと考えます。その上で、3の方法では一部のブラウザとスクリーンリーダーの組み合わせによっては読み上げられないことがあるため、現状では4の方法が妥当だと考えます。

しかし、`aria-label`属性の値は自動翻訳が行われないなど別の観点での問題があることは留意する必要があります。

おそらく最もアクセシビリティを高く保つことができる方法は、`aria-label`を付与する方法（1か4）と、`aria-label`以外でアクセシブルな名前を付与する方法（2か3）の両方を組み合わせることですが、1つのUIに同じ名前を2箇所持たせるのは保守性が下がるためあまり実用的ではないと考えます。

---

以上で、SVGアイコンボタンにアクセシブルな名前を持たせる方法についての調査と比較検討は概ね済みましたが、調査していく中でアイコンボタンというUIの解釈についていくつか疑問が出てきたので、以下に記述します。

## アイコンボタンとWCAG 2.2の解釈について

アイコン画像のみを持つボタンUI単体に関連するWeb Content Accessibility Guidelines (WCAG) 2.2の達成基準はいくつか該当するかと思いますが、今回は[Success Criterion 1.1.1 Non-text Content](https://www.w3.org/TR/WCAG22/#non-text-content)とアイコンボタンにまつわる疑問点をまとめていきます。

:::message
WCAGは特定の技術に依存しないように設計されているため、HTMLやWAI-ARIAに当てはめて考えると細部の解釈が曖昧になることが多々あります。その上で、アイコンボタンに対しての疑問を個人的な解釈でまとめていきたいと思います。
:::

### ポイント1：アイコンのみのボタンのアイコンは装飾なのか意味を持つ画像なのか

アイコンボタンのアイコン画像は[Success Criterion 1.1.1 Non-text Content](https://www.w3.org/TR/WCAG22/#non-text-content)の例外のうちの1つである"Decoration, Formatting, Invisible"に相当するか否かについて。

> **Decoration, Formatting, Invisible**
> If non-text content is [pure decoration](https://www.w3.org/TR/WCAG22/#dfn-pure-decoration), is used only for visual formatting, or is not presented to users, then it is implemented in a way that it can be ignored by [assistive technology](https://www.w3.org/TR/WCAG22/#dfn-assistive-technologies).

> **pure decoration**
> serving only an aesthetic purpose, providing no information, and having no functionality

ボタンが持つアイコン画像はそれ自体が情報や機能をユーザーに伝えているため、"providing no information, and having no functionality"には当てはまらないのではないかと考えます。なので、アイコン画像は代替テキストを持つべきだと考えますが、アイコンボタンとしてはもう1つの例外についても考える必要があります。

### ポイント2：非テキストコンテンツの範囲はアイコン画像のみなのかアイコンボタンまで含めるのか

アイコンボタンは1.1.1 Non-text Contentの例外のうち"Controls, Input"に該当するか否かについて。

> Controls, Input
If non-text content is a control or accepts user input, then it has a [name](https://www.w3.org/TR/WCAG22/#dfn-name) that describes its purpose. (Refer to [Success Criterion 4.1.2](https://www.w3.org/TR/WCAG22/#name-role-value) for additional requirements for controls and content that accepts user input.)

この例外のボタンにおける具体例は、WAIの提示する達成方法の1つである[H36: Using alt attributes on images used as submit buttons](https://www.w3.org/WAI/WCAG22/Techniques/html/H36)^[[Understanding Success Criterion 1.1.1: Non-text Content | WAI | W3C](https://www.w3.org/WAI/WCAG22/Understanding/non-text-content.html) > Techniques > Sufficient Techniques > Situation C: If non-text content is a control or accepts user input: に記載]にあるような、[`<input type="image">`](https://developer.mozilla.org/ja/docs/Web/HTML/Element/input/image)などで画像データを直接送信ボタンなどに使用している場合が挙げれられます。

アイコンボタン内のアイコン画像のみを非テキストコンテンツと捉えていましたが、アイコンボタンそのものが非テキストコンテンツと捉えることができる場合、上記の例外に当てはまるのではないかと思いました。いずれにせよ、アイコンボタンにアクセシブルな名前を持たせることに変わりはありませんが、非テキストコンテンツの粒度の解釈によっては、「SVGアイコンボタンにアクセシブルな名前を持たせる方法」のセクションで挙げた方法のうち1と2の方法も妥当になる可能性があります。

ここで、WCAGにおける[content](https://www.w3.org/TR/WCAG22/#dfn-content)と[non-text content](https://www.w3.org/TR/WCAG22/#dfn-non-text-content), [programmatically determined](https://www.w3.org/TR/WCAG22/#dfn-programmatically-determinable) の定義を見てみます。

> **content**
> information and sensory experience to be communicated to the user by means of a [user agent](https://www.w3.org/TR/WCAG22/#dfn-user-agents), including code or markup that defines the content's [structure](https://www.w3.org/TR/WCAG22/#dfn-structure), [presentation](https://www.w3.org/TR/WCAG22/#dfn-presentation), and interactions

> **non-text content**
> any content that is not a sequence of characters that can be [programmatically determined](https://www.w3.org/TR/WCAG22/#dfn-programmatically-determinable) or where the sequence is not expressing something in [human language](https://www.w3.org/TR/WCAG22/#dfn-human-language-s)

> **programmatically determined (programmatically determinable)**
> determined by software from author-supplied data provided in a way that different [user agents](https://www.w3.org/TR/WCAG22/#dfn-user-agents), including assistive technologies, can extract and present this information to users in different modalities
>
> EXAMPLE 1
> Determined in a markup language from elements and attributes that are accessed directly by commonly available assistive technology.
>
> EXAMPLE 2
> Determined from technology-specific data structures in a non-markup language and exposed to assistive technology via an accessibility API that is supported by commonly available assistive technology.

これらの定義から非テキストコンテンツは、知覚的に伝わる情報とも機械的に判断可能な情報とも捉えることができると思います。前者の場合、ユーザーは通常アイコンボタンをアイコンとボタンに分けて認識しないはずなので、アイコンボタン全体が非テキストコンテンツと捉えられますが、後者の場合、ボタンとアイコン画像はそれぞれコンテンツとして区別できる要素なので、アイコン画像のみが非テキストコンテンツと捉えられます。

感覚的にはアイコンボタン全体を非テキストコンテンツとして捉えて、1.1.1の例外のうち"Controls, Input"に該当してボタンにアクセシブルな名前を持たせても良さそうに思いますが、これを許容するとアイコン画像の種類によって実装にブレが生じると思います。

例えば、`<button>`要素の中に`<img>`要素がある場合は、`<img>`要素に`alt`属性を付与する方法がベストであり`<button>`要素自体に名前を設定することはないと思います。しかし、これが`<svg>`や他のアイコン画像を描画する方法の場合は`<button>`に`aria-label`を付与するとしたら、アイコン画像の種類によってアクセシブルな名前をアイコンに持たせるかボタンに持たせるか分かれてしまうので、基本的にはアイコン画像のみを非テキストコンテンツと捉えてアイコンにアクセシブルな名前を持たせる方針に倒す方がシンプルだと思います。

## アイコンボタンのアクセシブルな名前はボタンが持つべきかアイコンが持つべきか

アイコンボタンのアイコン画像は装飾ではなく意味を持つ画像であるため、ボタンではなくアイコン画像にアクセシブルな名前を持たせるべきだと考えます。

また、アイコンに直接名前を設定できない場合でも Visually Hiddenなクラスを持たせた`<span>`要素などでテキストを入れる方法を使うことで、全てのケースにおいて`<button>`要素に`aria-label`を持たせない実装方針に倒す方がシンプルだと思います。

- `<img>`要素 → `alt`属性を付与する
- `<svg>`要素 → `role="img"`と`aria-label`属性を付与する
- 外部のアイコンライブラリなど直接名前をつけられない場合 → Visually Hiddenなクラスを持たせた`<span>`要素などでテキストを入れる

## まとめ

- アイコンボタンの非テキストコンテンツは装飾ではなく意味を持つ画像なので、ボタンではなくアイコン画像自体にアクセシブルな名前を持たせるべきだと考えます
- 一方で、非テキストコンテンツの捉え方によってはボタンにアクセシブルな名前を持たせても良さそうですが、`<img>`要素や`<svg>`要素など様々な種類のアイコン画像の実装を想定した場合、やはりボタンにアクセシブルな名前を持たせない方針に倒す方がシンプルだと思います
- `<svg>`要素のみを持つ`<button>`要素にアクセシブルな名前を持たせる4つの方法を比較した結果、現状では`<svg>`要素に`role="img"`と`aria-label`を設定する方法が妥当と考えます

もし他の解釈や観点、ご指摘等ございましたらぜひコメントをお願いします。

## 関連URL

- [Success Criterion 1.1.1 Non-text Content - Web Content Accessibility Guidelines (WCAG) 2.2](https://www.w3.org/TR/WCAG22/#non-text-content)
- [aria-label で始める、アクセシビリティ改善活動](https://zenn.dev/moneyforward/articles/b5c9b060cf9237)
- [Accessible name for svg element inside button element](https://codepen.io/taigakiyokawa/pen/xxMPLKg?default-tab=html,result)
- [1.1.1 画像に代替テキストを提供する - Ameba Accessibility Guidelines](https://a11y-guidelines.ameba.design/1/1/1/)
- [kufu/smarthr-ui: React components for creating SmartHR applications.](https://github.com/kufu/smarthr-ui)
- [smarthr-ui/src/components/VisuallyHiddenText/VisuallyHiddenText.tsx · kufu/smarthr-ui](https://github.com/kufu/smarthr-ui/blob/master/src/components/VisuallyHiddenText/VisuallyHiddenText.tsx)
- [Screen Readers - Tailwind CSS](https://tailwindcss.com/docs/screen-readers)
- [react-icons/react-icons: svg react icons of popular icon packs](https://github.com/react-icons/react-icons)
- [The Accessibility Object Model (AOM) | aom](https://wicg.github.io/aom/)
- [書籍『Webアプリケーションアクセシビリティ』サポートサイト](https://webapp-a11y.com/)
- [Contextually Marking up accessible images and SVGs | scottohara.me](https://www.scottohara.me/blog/2019/05/22/contextual-images-svgs-and-a11y.html#:~:text=SVGs%20within%20links%20and%20buttons)
- [Understanding Success Criterion 1.1.1: Non-text Content | WAI | W3C](https://www.w3.org/WAI/WCAG22/Understanding/non-text-content.html)
- [H36: Using alt attributes on images used as submit buttons | WAI | W3C](https://www.w3.org/WAI/WCAG22/Techniques/html/H36)
- [<input type="image"> - HTML: ハイパーテキストマークアップ言語 | MDN](https://developer.mozilla.org/ja/docs/Web/HTML/Element/input/image)
