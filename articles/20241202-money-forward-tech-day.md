---
title: "アクセシビリティとモーショングラフィックの両立を目指した、Money Forward Tech Day 2024のWebサイト開発"
emoji: "🌏"
published: false
publication_name: "moneyforward"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["accessibility", "i18n", "rive", "react", "contest2024"]
---

この記事は、[Money Forward Engineers Advent Calendar 2024](https://adventar.org/calendars/9988) 1日目の投稿です 🎄

---

2024年9月20日に開催されたマネーフォワード初の自社テックカンファレンス[Money Forward Tech Day 2024](https://techday.moneyforward-dev.jp/2024/)において、スピーカーとしての登壇のほか、Webサイトの開発およびグラフィック以外のUIデザインも担当しました。このWebサイトは、社外の不特定多数の方からの閲覧が想定されることに加えて、当社が今年3月に公開した[アクセシビリティステートメント](https://corp.moneyforward.com/accessibility/)に自分自身も携わっていたこともあり、はじめからアクセシビリティを高く保つことを目指しました。一方で、ブランドデザインとしてモーショングラフィックにトライしたいという要望もあり、これらの両立にチャレンジしました。

本記事では、カンファレンスWebサイトのアクセシビリティとモーショングラフィックに関する設計や実装における知見と、細かい工夫ポイントなどを紹介していきます。

実際に出来上がったWebサイトはこちらです👇

https://techday.moneyforward-dev.jp/2024/

サイトの公開自体は8月で、その辺りからずっと記事を書こうとは思っていたのですが、気がついたらアドベントカレンダーの季節になってしまいました 🎅🏻

## はじめにまとめ

今回、マネーフォワード初の自社テックカンファレンスのWebサイトをデザイン・開発するにあたり、主に以下のことに取り組みました。

- アクセシビリティとモーショングラフィックを両立させるために、グラフィックとコンテンツ背景と前景に分けたシンプルなレイアウトにして、モーションの一時停止をどこからでもできるようにしました
- アクセシビリティを高く保つために、モーダルダイアログや16px未満のフォントサイズの使用を避けたり、英語対応や色の使い方、キーボード操作などにも考慮しました
- モーショングラフィックの実行・制御には[Rive](https://rive.app/)を使用し、モーションの一時停止や、Intersection Observer APIと組み合わせたセクションごとのシーン遷移を実現しました

---

## 想定する読者

- Webアクセシビリティに興味がある方
- Riveを使ったモーショングラフィックの実装に興味がある方
- Webサイトのデザインや開発に興味がある方

## 使用技術

今回のWebサイトはNext.jsのApp Routerを使って構築し、Static ExportsしたファイルをGitHub Pagesで公開しています。デザインはFigmaで作成し、スタイリングにはZero runtime CSS-in-JSの[Panda CSS](https://panda-css.com/)を採用しています。モーショングラフィックの実行・制御には[Rive](https://rive.app/)というツールを使用しています。

:::details 主な使用技術のバージョン

- `node`: 20.15.0
- `typescript`: 5.5.2
- `react`: 18.3.1
- `next`: 14.1.2
- `@pandacss/dev`: 0.41.0
- `@rive-app/react-canvas`: 4.13.3
- `react-i18next`: 14.1.2
- `storybook`: 8.1.10

:::

## アクセシビリティ

Webサイトのアクセシビリティを高く保つために、デザインや実装において主に以下のことを取り組みました。それぞれ簡単に説明していきます。

- シングルカラムでレイアウトする
- モーショングラフィックとコンテンツを分ける
- モーショングラフィックを一時停止できるようにする
- モーダルダイアログを使わない
- 英語でも閲覧できるようにする
- その他（16px未満のフォントサイズを使わない、フォントサイズの指定には相対値remを使う、色だけで区別しない、オレンジ色のコントラスト比をがんばる、セマンティックにマークアップする、キーボードで操作できるようにする）

### シングルカラムでレイアウトする

まずはサイト全体のレイアウトをシンプルにすることを意識しました。いくつかのフローティングコンテンツを除いて、基本は全て画面中央にシングルカラムで配置しています。書籍[『Webアプリケーションアクセシビリティ』](https://webapp-a11y.com/)の第8章にも書かれているように、シングルカラム優先でレイアウトすることで、目線移動の負担を減らしたり、状態把握をしやすくすることに役立ちます。今回は半日のカンファレンスでシングルトラックのみのスケジュールで、コンテンツ量も多くないため実現しやすかったです。

特にタイムテーブルは、よくある時間軸をコンテンツの横に並べるレイアウトをやめ、全ての時間をシングルカラム内に明記することで、目線移動の負担を減らしました。

![タイムテーブルセクションのスクリーンショット](https://storage.googleapis.com/zenn-user-upload/96c9833d2fea-20241202.png)
*各コンテンツの時間帯がそれぞれの枠内に縦並びで配置されている*

また、レイアウトをシンプルにすることで、実装上の余計な負担も減らすことができ、より主要な部分の開発にフォーカスすることができました。

### モーショングラフィックとコンテンツを分ける

もう一つ全体のレイアウトに関しては、モーショングラフィックを背景に、コンテンツを前景に配置することで、互いに干渉しないように切り分けました。全体のダイナミックな演出部分は背景に寄せることで、コンテンツ自体は全て静的な状態で表示しています。この設計が直接何らかのアクセシビリティ向上に繋がるわけではありませんが、コンテンツの読みやすさや、後述するモーショングラフィックの一時停止の仕組みに役立ちます。

また、今回グラフィック部分はモーションデザイナーの方とBXデザイナーの方が担当していたため、役割分担としても合理的な設計になっていたような気がします。

### モーショングラフィックを一時停止できるようにする

ここがモーショングラフィックを採り入れたWebサイトのアクセシビリティにおいて最も重要なポイントです。[Web Content Accessibility Guidelines (WCAG) 2.2 (日本語訳)](https://waic.jp/translations/WCAG22/)の[達成基準 2.2.2 一時停止、停止、非表示](https://waic.jp/translations/WCAG21/Understanding/pause-stop-hide.html)[^1]によると、自動で動き続けるコンテンツはユーザーによって一時停止や非表示できることが求められています。この達成基準は「[非干渉](https://waic.jp/translations/WCAG22/#cc5)」と呼ばれる適合要件に該当し、これを達成していないとそのWebサイトの他のコンテンツやサイト全体へのアクセスを妨げる恐れがあり、他の達成基準を満たしているか否かにかかわらず、必ず満たさなければいけません。

今回は、Riveを使った再生・停止の制御をフローティングボタンで行うことで実現できました。また、前述の通り、モーショングラフィックを背景にコンテンツを前景に置くことで、サイトのどこからでも一時停止ができるようにしたり、モーショングラフィックの一時停止がコンテンツの閲覧に影響を与えないようにしています。

![アクセスセクションのスクリーンショット。青空の背景。右下に一時停止マークのついたボタン。](https://storage.googleapis.com/zenn-user-upload/51842feb9ec6-20241202.png)
*通常時は背景のシーンがセクションごとに切り替わる*

![アクセスセクションのスクリーンショット。宇宙の背景。右下に再生マークのついたボタン。](https://storage.googleapis.com/zenn-user-upload/5f738c484958-20241202.png)
*一時停止することで、背景は止まったままコンテンツが閲覧できる*

[^1]: 解説書の日本語訳はWCAG2.1のものですが、WCAG2.2にも同じ達成基準があります。

### モーダルダイアログを使わない

タイムテーブルから各登壇の情報を閲覧する際に、モーダルダイアログを使わずに、各登壇の詳細情報を別ページにリンクする形で設計しました。モーダルダイアログは、ユーザーを現在のページから離脱させることなく特定の操作に集中させることができる一方、利用状況によっては途端に情報を見失ったり操作の負担が大きくなることが多い要素です。書籍『Webアプリケーションアクセシビリティ』第8章では、「『モーダルダイアログでなければならない』という必然性があるケースでのみ使うべき」とされています。実装においても、フォーカストラップやキーボード操作、見出しの順序など、モーダルダイアログを使うことで考慮すべき点がとても多くなります。

今回はタイムテーブルの各登壇情報を詳細画面へのリンクとして設計することで、一覧と詳細の関係性をシンプルに保ちつつ、詳細情報は単一のページにまとめることで、ユーザーが迷うことなく閲覧できるように設計しました。全体のコンテンツ量が多くないため、詳細情報も一覧にまとめてしまっても良かったかなと思いましたが、カンファレンス終了後に登壇資料を埋め込む予定があったため、詳細画面が分かれていた方が閲覧しやすくなると判断しました。

また、登壇者としては、個別のURLを持つことで自身の登壇情報を共有しやすくなるというメリットもあります。

| タイムテーブル | 詳細画面 |
| --- | --- |
| ![タイムテーブルのいくつかのコンテンツの中で「Money Forward UIの紹介」というタイトルのコンテンツにフォーカスが当たっている](https://storage.googleapis.com/zenn-user-upload/a72a55583c76-20241202.png) | ![「Money Forward UIの紹介」という見出しで、埋め込まれたスライドや登壇内容の説明、登壇者のプロフィールが表示されている](https://storage.googleapis.com/zenn-user-upload/4fd3abc11178-20241202.png) |

### 英語でも閲覧できるようにする

国際化（Internationalization, i18n）対応も非日本語話者がコンテンツにアクセスできるようになるという意味ではアクセシビリティの一環です。当社ではエンジニア組織のグローバル化が進められており、今回のWebサイトを英語でも提供することは必須要件でした。

UIデザインでは、Figma VariablesのModeを使って日本語と英語の切り替えのシミュレーションを行いました。

![Name, en, jpの3列とdate, timeの2行の表。dateのenには「Friday, September 20, 2024」, jpには「2024年9月20日（金）」が入っており、timeのenには「1–6 p.m.」, jpには「13:00–18:00」が入っている。](https://storage.googleapis.com/zenn-user-upload/0181f05abed1-20241202.png)
*完成したサイトには使ってないやつかも。あくまでイメージで。*

実装については、日英のフレーズ切り替え自体はreact-i18nextを使用しましたが、サブパスによる言語切り替えについては、Next.js App RouterのStatic Exportsがi18n機能に対応していなかったため、以下の記事を参考に自前で実装しました。

https://blog.arthur1.dev/entry/2023/06/04/100000

今回サブパスによる言語切り替えとNext.js App RouterのStatic Exportsとの相性の悪さから色々と苦労したので、クエリパラメータで切り替える設計にした方が都合が良かったかもなあと若干反省しています。あるいはそもそも静的サイトでがんばらずに、素直にサーバーを置くべきだったかもしれません。

言語切り替えにおいて、最も苦労するのがレイアウトの変化です。今回の場合は、会場の住所を記載していたため、英語表記では日本語表記の逆順にする必要があります。

| 日本語 | English |
| --- | --- |
| ![東京ポートシティ竹芝 ポートホール, 〒105-7501 東京都港区海岸1-7-1 東京ポートシティ竹芝 オフィスタワー1階](https://storage.googleapis.com/zenn-user-upload/92b481e40efb-20241202.png) | ![Tokyo Portcity Takeshiba Port Hall, Office Tower 1F, Tokyo Portcity Takeshiba 1-7-1, Kaigan, Minato-Ku Tokyo 105-7501](https://storage.googleapis.com/zenn-user-upload/701c3e2837ff-20241202.png) |

文言を分けて、愚直に並び替えています。
```tsx
{lang === 'en' ? (
  <>
    <p>{t('venue.address.bldg')}</p>
    <p>{t('venue.address.city')}</p>
    <p>{t('venue.address.postCode')}</p>
  </>
) : (
  <>
    <p>{t('venue.address.postCode')}</p>
    <p>{t('venue.address.city')}</p>
    <p>{t('venue.address.bldg')}</p>
  </>
)}
```

### その他

- **16px未満のフォントサイズを使わない**：Webブラウザのデフォルトを下回らないように設計
- **フォントサイズの指定には相対値remを使う**：画面拡大時にもレイアウトを保つため
- **色だけで区別しない**：日英どちらがアクティブか下線を引く、各セッションのカテゴリはテキストで明示、外部リンクにはアイコンをつける、など
- **オレンジ色のコントラスト比をがんばる**：BXチームとtoCサービスのデザインチームで調査・検討の末に定義された、コントラスト比を保ちつつブランドイメージとのバランスも取れたオレンジ色（#EC6A00）をプライマリーカラーに使用
- **セマンティックにマークアップする**：見出し、リンク、ボタン、ランドマークロール、など
- **キーボードで操作できるようにする**：ハンバーガーメニューの開閉、初期描画からTabキーでモーション開始、モーションの一時停止ボタンやタイムテーブルの各コンテンツにフォーカス、など

### 検証について

今回のWebサイトでは、WCAG 2.2への厳密な準拠度合いのチェックなどは行っていませんが、最低限の機械チェックはクリアしています。そのほか、Mac VoiceOverによる操作やキーボード操作の手動テストも行っています。また、自薦の結果、アクセシビリティに配慮したWebデザイン ギャラリー・サイト AAA11Y（Accessible Website Gallery）に掲載していただきました！🎉

https://www.aaa11y.com/66d1b5585525ef0847c1c1fd/

## モーショングラフィック

つづいて、Riveを使ったモーショングラフィックの実行と制御について説明していきます。

- Riveとは
- Riveのモーショングラフィックを一時停止する
- RiveとIntersection Observer APIでシーン遷移を制御する

### Riveとは

Riveとは、モーショングラフィックの開発環境から実行環境までを提供するプラットフォームです。Riveを使って、Webやモバイル、ゲームなど様々なプラットフォーム上で動作するモーショングラフィックを作成することができます。軽量なファイルサイズと、[State Machine](https://rive.app/community/doc/state-machine/docwH5zPdh93)と呼ばれる状態制御の仕組みが主な特徴です。Web上ではWebAssembly (WASM) を使って実行しています。

https://rive.app/

日本語での情報については、以下の解説記事が参考になると思います。

https://zenn.dev/shota1995m/articles/shota1995m-rive-app

https://zenn.dev/junni/articles/08e6d4cb67659a

今回のWebサイトでは、モーショングラフィックの作成まではモーションデザイナーの方に担当していただいたので、本記事ではReact（[@rive-app/react-canvas](https://github.com/rive-app/rive-react)）でRiveを実行・制御する話にフォーカスします。

### Riveのモーショングラフィックを実行する

Riveで作成したモーショングラフィックをReactで実行するには、カスタムフック`useRive`を使用します。

公式ドキュメントのcode exampleを見ていただくのが一番わかりやすいかと思います。

@[codesandbox](https://codesandbox.io/embed/9ywsjl?view=preview&module=%2Fsrc%2FApp.js)

今回のWebサイトでは、riveが提供しているLayoutクラスのインスタンスで2つのenum FitとAlignmentを使って背景全面を覆うようなコンポーネントとして定義しています。

```tsx
import { useRive, Layout, Fit, Alignment } from '@rive-app/react-canvas';

export const BackgroundAnimation = () => {
  const { RiveComponent } = useRive({
    src: '/2024/rive/techday2024.riv',
    autoplay: true,
    layout: new Layout({
      fit: Fit.Cover,
      alignment: Alignment.Center,
    }),
  });

  return (
    <div
      // Panda CSSが提供するcss関数でスタイリング
      className={css({
        position: 'fixed',
        inset: 0,
        width: '100%',
        height: '100%',
      })}
    >
      <RiveComponent
        title="Money Forward Tech Day 2024 - Let’s make it!"
        className={css({ width: '100%', height: '100%' })}
      />
    </div>
  );
};
```

`RiveComponent`は`canvas`要素として描画されます。`title`属性を使って、スクリーンリーダーで読み上げたり、ツールチップとして表示するテキストを設定することができます。

### Riveのモーショングラフィックを一時停止する

Riveのモーショングラフィックの再生・一時停止を制御するには、useRiveの戻り値として得られる`rive`インスタンスを使います。`RiveComponent` がモーショングラフィックを描画するコンポーネントで、`rive` はモーショングラフィックそのものの制御を行うインスタンスというイメージです。

`rive.play()` で再生、`rive.pause()` で一時停止ができます。今回はstateと組み合わせて再生と一時停止を切り替えるボタンを設置しています。

```tsx
import { useRive, Layout, Fit, Alignment } from '@rive-app/react-canvas';

export const BackgroundAnimation = () => {
  const { rive, RiveComponent } = useRive({
    ...
  });

  const [isRivePaused, setIsRivePaused] = useState(false);
  const handleClickButton = () => {
    if (isRivePaused) {
      rive && rive.play();
      setIsRivePaused(false);
    } else {
      rive && rive.pause();
      setIsRivePaused(true);
    }
  };

  return (
    <>
      <div>{/* RiveComponent */}</div>
      <button onClick={handleClickButton}>
        {isRivePaused ? '▶️' : '⏸️'}
      </button>
    </>
  );
};
```

### RiveのState MachineとIntersection Observer APIでシーン遷移を制御する

ここが今回のWebサイトのモーショングラフィック実装において最も苦労したポイントです。とはいえ、モーショングラフィックのState Machineがどのように作られているか理解できれば後は単純なので、キャッチアップ不足による部分が大きかったです。今回のようにモーションデザイナーと実装者が別々の場合、モーションデザイナーがState Machineをどのように設計したか十分にコミュニケーションを取って理解しておくとスムーズにいくと思います。

今回のWebサイトではセクションごとにモーショングラフィックのシーンが切り替わるため、これをRiveのState Machineと[Intersection Observer API](https://developer.mozilla.org/ja/docs/Web/API/Intersection_Observer_API)と組み合わせて実現しました。

#### State Machineとは

State Machineは、モーショングラフィックのトランジションロジックを視覚的に設計する方法です。これにより、インタラクティブに変化するモーショングラフィックを作成することができます。State MachineはRive editor上で状態遷移図を作るように定義することができます。また、[Inputs](https://rive.app/community/doc/inputs/docwgNrq7ssz)という入力を受け付ける変数を定義することで、外部からの入力によって状態遷移を制御することができます。

Reactの実行環境では、[`useStateMachineInput`](https://rive.app/community/doc/state-machines/docxeznG7iiK)を使ってState Machineの入力を制御することができます。editorで定義されたState Machineの名前と、Inputsの名前を指定することで、Riveアニメーションの状態遷移を制御することができます。

今回は、'Section Transition'というInputsが定義されており、シーンごとに数値を変えることで、シーンの切り替えを制御しています。

```tsx
const sectionTransitionInput = useStateMachineInput(
  rive, // riveインスタンス
  'State Machine', // State Machineの名前
  'Section Transition', // Inputsの名前
  1, // Inputsの初期値
);

// シーン2に切り替える
sectionTransitionInput && (sectionTransitionInput.value = 2);
```

##### Section Transitionの数値と各シーンの対応表

| 1 | 2 | 3 | 4 | 5 |
| --- | --- | --- | --- | --- |
| ![マネーフォワードのロゴとTECH DAY'24の表記。クリーム色の背景。](https://storage.googleapis.com/zenn-user-upload/86fd025c2655-20241202.png) | ![宇宙空間の中の地球。周りにMoney Forward Tech Day'24, Let's make it!の表記。](https://storage.googleapis.com/zenn-user-upload/212a1f63df07-20241202.png) | ![宇宙空間でより地球にクローズアップしている。UFOと飛行機が飛んでいる。](https://storage.googleapis.com/zenn-user-upload/21fa42240527-20241202.png) | ![青空から日本を見下ろした風景。東京タワーと富士山が見える。鳥が飛んでいる。](https://storage.googleapis.com/zenn-user-upload/b39405e6d273-20241202.png) | ![ビルの屋上からの風景。人がたくさんいる。鳥と風船が飛んでいる。](https://storage.googleapis.com/zenn-user-upload/a1e795c4b797-20241202.png) |

#### Intersection Observer API（交差オブザーバーAPI）とは

あるターゲット要素とビューポートの交差イベントを監視するAPIです。`scroll`イベントを使って監視する方法と比べて、ビューポートサイズの再計算が不要で、スクロールのたびにイベント発火することなく、パフォーマンスに優れています。

今回の実装では、各セクションをターゲットとして、セクションがビューポートのに交差したタイミングで`sectionTransitionInput`を呼び出して、モーショングラフィックのシーンを切り替えるようにしました。

基本的な使い方は以下の記事が参考になります。

https://ics.media/entry/190902/

#### 実装イメージ

State MachineのInputsとIntersection Observer APIを組み合わせて、セクションごとにモーショングラフィックのシーンを切り替える実装イメージは以下のようになります。実際にはファーストビューがスクロールによって変化したりする制御が加わりますが、ここでは簡略化しています。

```tsx
const sectionTransitionInput = useStateMachineInput(
  rive, // riveインスタンス
  'State Machine', // State Machineの名前
  'Section Transition', // Inputsの名前
  1, // Inputsの初期値
);


const sectionIds = useMemo(() => {
  return ['main', 'concept', 'timetable', 'access', 'register'];
}, []);

useEffect(() => {
  const sections = sectionIds.map((id) => {
    return document.getElementById(id);
  });

  if (
    sections.some((section) => {
      return section === null;
    })
  ) {
    return;
  }

  const observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          if (entry.target.id === 'main') {
            sectionTransitionInput && (sectionTransitionInput.value = 2);
          }
          if (entry.target.id === 'concept') {
            sectionTransitionInput && (sectionTransitionInput.value = 3);
          }
          if (entry.target.id === 'timetable') {
            sectionTransitionInput && (sectionTransitionInput.value = 4);
          }
          if (entry.target.id === 'access') {
            sectionTransitionInput && (sectionTransitionInput.value = 5);
          }
          if (entry.target.id === 'register') {
            sectionTransitionInput && (sectionTransitionInput.value = 5);
          }
        }
      });
    },
    { rootMargin: '-30% 0px' },
  );

  sections.forEach((section) => {
    if (section) observer.observe(section);
  });

  return () => {
    sections.forEach((section) => {
      if (section) observer.unobserve(section);
    });
  };
}, [sectionIds, sectionTransitionInput]);
```

なんかもっとスマートな方法がありそうな気がしますが、今回は初挑戦ということで 🙏

## まとめ

今回、マネーフォワード初の自社テックカンファレンスのWebサイトをデザイン・開発するにあたり、主に以下のことに取り組みました。

- アクセシビリティとモーショングラフィックを両立させるために、グラフィックとコンテンツ背景と前景に分けたシンプルなレイアウトにして、モーションの一時停止をどこからでもできるようにしました
- アクセシビリティを高く保つために、モーダルダイアログや16px未満のフォントサイズの使用を避けたり、英語対応や色の使い方、キーボード操作などにも考慮しました
- モーショングラフィックの実行・制御には[Rive](https://rive.app/)を使用し、モーションの一時停止や、Intersection Observer APIと組み合わせたセクションごとのシーン遷移を実現しました

今回のWebサイトはシンプルながら、デザイン面でも実装面でも学びになることが多くて良い機会でした。アクセシビリティを意識したUI設計も、Riveを使ったモーショングラフィックも両方とても面白いので、ぜひチャレンジしてみてください！

## 関連リンク

- [Money Forward Tech Day 2024](https://techday.moneyforward-dev.jp/2024/)
- [アクセシビリティステートメント｜株式会社マネーフォワード](https://corp.moneyforward.com/accessibility/)
- [Rive - Build interactive motion graphics that run anywhere](https://rive.app/)
- [Panda CSS - Build modern websites using build time and type-safe CSS-in-JS](https://panda-css.com/)
- [Webアプリケーションアクセシビリティ](https://webapp-a11y.com/)
- [Web Content Accessibility Guidelines (WCAG) 2.2 (日本語訳)](https://waic.jp/translations/WCAG22/)
- [達成基準 2.2.2: 一時停止、停止、非表示を理解する](https://waic.jp/translations/WCAG21/Understanding/pause-stop-hide.html)
- [達成基準 1.4.4: テキストのサイズ変更を理解する](https://waic.jp/translations/WCAG21/Understanding/resize-text.html)
- [達成基準 1.4.1: 色の使用を理解する](https://waic.jp/translations/WCAG21/Understanding/use-of-color.html)
- [Money Forward Tech Day 2024 - AAA11Y (Accessible Website Gallery)](https://www.aaa11y.com/66d1b5585525ef0847c1c1fd/)
- [State Machine](https://rive.app/community/doc/state-machine/docwH5zPdh93)
- [WebアニメーションはRiveが便利！](https://zenn.dev/shota1995m/articles/shota1995m-rive-app)
- [インタラクティブなアニメーションをRiveで✨](https://zenn.dev/junni/articles/08e6d4cb67659a)
- [Inputs](https://rive.app/community/doc/inputs/docwgNrq7ssz)
- [交差オブザーバー API - Web API | MDN](https://developer.mozilla.org/ja/docs/Web/API/Intersection_Observer_API)

## おまけ

Money Forward Tech Day 2024では、本業であるデザインシステム構築について登壇もしているので、よろしければこちらの登壇資料もご覧ください👇

@[speakerdeck](abc6aab6e99f4a409e59c5aefa5a6c56)
