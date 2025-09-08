---
title: 'フロントエンドカンファレンス北海道2025 オンライン参加レポート（鮮度優先）'
emoji: '🦊'
type: 'idea' # tech: 技術記事 / idea: アイデア
topics: ['frontend', 'html', 'accessibility', 'ui']
published: true
---

フロントエンドカンファレンス北海道2025で視聴したトークごとの個人的なメモと感想をまとめたもの。全部ではなく視聴したトークのみ。オンライン参加なので北海道の美味しい食べ物情報はありません。[リアルタイムで書いていたスクラップ](https://zenn.dev/taigakiyokawa/scraps/e7b468bb38fcc8)をちょっと加筆した程度なので、読み物としてはだいぶ雑。

## イベント概要

https://www.frontend-conf.jp/

https://fortee.jp/frontend-conf-hokkaido-2025/timetable

- 開催日時：2025 年 9 月 6 日（土）10:00AM–6:00PM
- 場所：[エア・ウォーターの森](https://airwater-souen.jp/)（札幌市 桑園）
  - オンライン配信：[YouTube](https://www.youtube.com/@fec-hokkaido)（限定公開のためアカウントのリンクのみ添付）
- イベントハッシュタグ：[#frontendo](https://x.com/hashtag/frontendo?src=hashtag_click&f=live)
  - [#フロントエンドチョットデキルルーム](https://x.com/hashtag/%E3%83%95%E3%83%AD%E3%83%B3%E3%83%88%E3%82%A8%E3%83%B3%E3%83%89%E3%83%81%E3%83%A7%E3%83%83%E3%83%88%E3%83%87%E3%82%AD%E3%83%AB%E3%83%AB%E3%83%BC%E3%83%A0?src=hashtag_click&f=live)
  - [#ガウディルーム](https://x.com/hashtag/%E3%82%AC%E3%82%A6%E3%83%87%E3%82%A3%E3%83%AB%E3%83%BC%E3%83%A0?src=hashtag_click&f=live)


## HTMLの品質ってなんだっけ？ -“HTMLクライテリア”の設計と実践

登壇者：うな [@unachang113](https://x.com/unachang113) さん（LayerX）

https://fortee.jp/frontend-conf-hokkaido-2025/proposal/776f1c2e-0005-4223-95a5-7f63144fe25c

@[speakerdeck](73acd2288583413887334bceee9b5742)

https://developers.prtimes.jp/2025/02/05/prtimes_html_criteria/

https://developers.prtimes.jp/2025/07/23/html-kaitai-shinsho-rindokukai/

:::details 視聴中のメモ

- 前職PR TIMESの活動についての発表
- きっかけ
  - 建前：HTMLの品質に対する共通認識を作る
  - 本音：アクセシビリティ対応の土台を作りたい
- ソフトウェア品質の8つの特性にHTMLの品質を担保
  - 機能適合性
  - 性能効率性
  - 互換性
  - 信頼性
  - セキュリティ
  - 保守性
  - 移植性
- HTMLだけの話だと狭すぎる…? → 迷走 → アドバイザーに相談
- カテゴリ
  - ユーザー体験を支える品質
    - UIコンポーネント
    - アクセシビリティ
    - ライブラリの選定
  - 成長できるチーム
    - HTMLの仕様理解
    - セマンティックなHTMLを書くことの意義の理解
    - より良い仕様に落とす努力
- Webフロントエンド版DXクライテリアをベースに
- HTML解体新書の輪読会を実施

:::

### 感想

- 計測して課題を分析、改善に繋げるサイクルを回せているのが良い
- 「アクセシビリティ」と言われるより「HTML」と言われた方がエンジニアはモチベーション高く取り組んでくれそう？分からない
- Webフロントエンド版DXクライテリアは結構幅広く活用できそう、今回はこれをベースにHTML向けに細分化した取り組みのひとつの事例と言えそう
- 「チームが理解できているか」「努力できているか」など、プロダクトというモノではなくヒトに向けられた指標なのも興味深い。モノが対象ならAIで効率よくチェックする道もありそうだけど、ヒトや組織を測るのはどうしようかな。
- HTML解体新書とか、フロントエンド系の輪読会うちもやりたい

## Designing on The Web

登壇者：saku [@sakupi01](https://twitter.com/sakupi01) さん（Cybozu）

https://fortee.jp/frontend-conf-hokkaido-2025/proposal/8fac5516-1a36-4d89-984f-152610d08015

https://sakupi01.github.io/slides/ja/2025_09_06_designing-on-the-web/

:::details 視聴中のメモ

- HTMLは不明なものを全て無視することで互換性を保っている
- Web Designは未知なるものとコラボレーションをしながらデザインしている
- CHSS
- UA StyleSheet
- User StyleSheet
- CSS
- !importantは詳細度バトルに勝つためではなく、UA/Userのスタイルを守るため
- **Authorはデザインをコントロールできない** → CSSは宣言的な言語
  - width: auto → コンテンツの幅によって自動的に…
  - text-wrap: pretty → なんかいい感じにして
- （裏のルームの発表と並行して視聴していたのであまりメモとれていない...）

:::

### 感想

- Webデザインの根幹の思想について、そもそもコントローラブルなものではないというのはとても納得
  - あくまで我々は我々のコンテンツを閲覧するのにオススメなデザインを「宣言」しているだけに過ぎず、どう閲覧されるのかは制御できないし、しようと思わない方が良い
- フロントエンドエンジニアよりもWebデザインを作るデザイナー、特に紙媒体とかグラフィック出身のデザイナーにこそ理解してもらいたい内容だけど、このWebの歴史の話そのまま言ってもあまりピンとこないかも？ブラウザの仕組みだとかその辺から説明が必要そう
- 数十分のプレゼンテーションよりも、書籍とかでじっくり読みたい感じの内容だった


## スクリーンリーダーと仲良くなるためのマークアップ

登壇者：ただ [@taketada323](https://twitter.com/taketada323) さん（Fusic）

https://fortee.jp/frontend-conf-hokkaido-2025/proposal/4a579929-627c-4d5d-aed8-e63d2812a880

:::details 視聴中のメモ

- アクセシビリティ対応の一部として捉えず、セマンティックなHTMLを目指す
- 読み上げられないボタン
- 代替テキスト
- カルーセル
- エラーメッセージ
- lang=”ja”
- トースト：ARIAライブリージョン、Notification API（ユーザー許可が必要）
- それぞれのDOMに役割を持たせる
- アクセシビリティオブジェクトモデルに従って要素を割り当てる
- 意図した順番に読み上げられるように設計する
- （裏のルームの発表と並行して視聴していたのであまりメモとれていない...）

:::

### 感想

- アクセシビリティ初心者向けにおすすめしたい内容
- HTMLクライテリアの発表でもそうだったけど、あんましアクセシビリティ色強くしない方が聞き入れやすいのかな
  - 「アクセシビリティを考えるのはデザイナーの仕事」といった風に自分の仕事であると捉えない偏見があるエンジニアが多い？分からない
- トーストをNotification APIにしてしまおうという発想は初めて聞いて新鮮だった。確かにアリかも。
- それぞれのDOMに役割を持たせる、は良い表現だなと思った。セマンティックなHTMLって要は全ての要素の意味がきちんと説明できることであって、フロントエンドエンジニアはその説明責任があるよなと。

## LT: エラーとアクセシビリティ

登壇者：たじまん [@schktjm](https://twitter.com/schktjm) さん（SmartHR）

https://fortee.jp/frontend-conf-hokkaido-2025/proposal/4cac6d40-7324-458d-bfc8-f84fc62b5612

@[speakerdeck](63c06596deed4635b2a7f8703b46784e)

https://docs.google.com/presentation/d/1MEKUvB8UtsHyalEsEF0JQMKcOSe1F8BOg1kTbp29Q0A/edit?slide=id.p#slide=id.p

:::details 視聴中のメモ

- ケース1：Submitボタンを押下した後にトーストメッセージを表示
- 時間経過で消える　→ 2.2.1 不適合、Webコンテンツに制限時間がかけられていないこと
- 入力フォームから離れた位置にある → 3.3.1 エラーの特定に不適合
- flashメッセージをやめよう！
  - SmartHRでは2022に非推奨 → 2025年6月に完全削除
- ケース2：入力フォーム → ダイアログが開く → ダイアログにエラーが表示
  - エラーは特定できる？ → ダイアログをやめよう！
  - ケース2.1：ダイアログをやめて、エラーをフォームの近くに記述
    - これでもダメではないが、エラー箇所を辿るのが大変
  - → 入力欄のトップにまとめがあると良い
- なんとなくダメそうではなく「何がダメなのか」「誰が困るのか」を具体化する

:::

### 感想

- FlashMessage（トースト）やめられたの羨ましい〜〜〜〜〜〜〜〜〜〜〜〜〜〜
  - 非推奨を宣言した2022年から2025年6月の完全削除までの3年間に想像を絶する苦労があったに違いない...
  - うちだと何年かかるんだろう...考えたくもない......
- ダイアログもやめよう！本当にそう
- トップにエラーのサマリがあると嬉しい、は私も過去プロダクトチームにアドバイスした内容だったので「正解」って言われてて安心した
  - 私の場合、「なんで」ってところの説明に説得力を持たせられてたかは微妙。こういう利用状況があって、ひとつひとつ辿るのが大変なユーザーがいるんだよということまで言えたら完璧だった。
- 「この達成基準に適合しません」と「こういうユーザーが困ります」をセットで説明できるようになりたい

## LT: 「待たせ上手」なスケルトンスクリーン、そのUXの裏側

登壇者：朴木 優斗 [@dachscafe](https://twitter.com/dachscafe) さん（チームラボ）

https://fortee.jp/frontend-conf-hokkaido-2025/proposal/dd5b541a-7d69-40f9-bda3-178701c06ab0

@[speakerdeck](b3fea5485935460aa521ba4e2acc8669)

:::details 視聴中のメモ

- Shimmer 煌めき
- スケルトンは早く感じさせるというかは待ってる感を減らす
- 能動的待機：意識を待ち時間からコンテンツにして負荷を減らす
- 描画後と同じ構造にする
- 馴染みのない場面で使わない
- 数秒から10秒程度が向いている、長すぎる場合はプログレスバーを出す、短すぎるところでもチラつくので良くない
- 上手く取り入れて待たせ上手に

:::

### 感想

- あの「グラデーションがぐわんぐわんする」スケルトンスクリーンってShimmerって言うんだ！知らなかったので「グラデーションがぐわんぐわんするやつ」って毎回説明していた
- どういう使い方をすると良いのか、どういう場面に適しているのか簡潔で分かりやすかった。弊社のガイドラインにも組み込みたい。

## AIエージェントによるWebアクセシビリティ試験の自動化 〜Gaudiyが実践するAI活用開発〜

登壇者：南悠輝 kotori [@maminami_minami](https://x.com/maminami_minami) さん（Gaudiy）

https://fortee.jp/frontend-conf-hokkaido-2025/proposal/34995dad-b5b6-4ced-bd07-1e169829d50f

@[speakerdeck](9cb3f46237bc47358f1200dbf9065b18)

:::details 視聴中のメモ

- スポンサーセッション
- 話すこと
  - 自動化手法
  - Custom MCPサーバー
- 早急にアクセシビリティ対応が必要に → 早く、正確に、楽したい
- AI以外のツール
  - eslint-plugin-jsx-a11y
    - polymorphicPropName settings
      - as のやつをどのHTML要素と解釈するか
    - component settings
      - Image → img
    - label-has-associated-control
      - controlComponents option
        - 制御コンポーネントの設定、labelを必須に
  - Storybook Test runner + axe-playwright
    - ハマりポイント：モーダルダイアログだとstorybook-rootにいないことがあるのでチェック対象に含まれるようにする
  - @storybook/addon-a11y
  - axe DevTools
- AIエージェント
  - 試験
  - レポート
- 最初：Claude Desktop
  - Playwright MCP
    - ace-coreを無理やり注入する必要があり、断念
  - a11yチェック MCP
    - 野良のツールのみ
    - いい感じには見えるが…
    - コンテキスト制限、インタラクション未対応、認証未対応、レスポンシブ確認できない
- Claude Code + Custom MCP Server
  - Claude COde: アクセシビリティテストの実行と結果の分析
  - Custom MCP
    - Playwright
    - axe-core
    - カスタム評価モジュール
  - Playwright MCP: アクセシビリティ問題点の調査
  - コンテキスト節約
    - axe-coreの生データは肥大化する
    - 冗長なHTMLデータは返さない
    - 並列化
- 評価モジュール
  - WCAG各基準に特化した評価ロジックを実装
  - axe-coreだけでは課題、誤検出が多い
  - キーボード操作
  - AIによるサイクルを回して誤検知の原因を減らしてチェックロジックを実装
- [宣伝] 余熱NIGHT 非公式感想戦&LT会

https://gaudiy.connpass.com/event/366599/


:::


### 感想

- たくさんのツールを破綻なく仕組みとして設計できていそうですごい
- コンテキスト問題はなんか考えなくて済むような方向に進化しないかな〜
- eslint-plugin-jsx-a11y ちゃんと使わなきゃな〜Polymorphic向けの設定とかしんどそう
- Playwrightちゃんと使わなきゃな〜
- axe-coreだけでは不適切な場面で独自評価ロジック実装は、たしかにそうすれば良いのかと納得
- 評価ロジックの実装にもAI使っていく姿勢は確かにそれはそう
- できれば実践した実際の結果とかどう改善に繋がっていったとかの運用サイクルの話も聞きたかった


## <UserCard data={クソデカ複雑Object} />問題を考える

登壇者：どうけ [@doke](https://twitter.com/doke) さん（Studio, カンファレンスロゴ作成）

https://fortee.jp/frontend-conf-hokkaido-2025/proposal/3867fb3c-22df-461f-b7d6-e717a9b17db0

https://docs.google.com/presentation/d/1C_nZ2IEh9tlT9Mc5qKdsp-40qklzM0yc3t0qCpMUQEs/edit?slide=id.p#slide=id.p


:::details 視聴中のメモ

- 事前収録
- コンポーネントの責務とデータの関連
- コンポーネントの肥大化は「責務」の混在が原因
- 単一のViewは単一のデータ表示を
- ドメインモデルとViewモデルを分ける
- データの加工は事前に行う
- Viewモデル
  - 見た目に必要なものだけをPropsで受け取り表示するコンポーネント
- 複雑なViewモデル：フラット vs 構造化
- コンポーネント分割
  - バケツリレー
  - renderProps
  - Contextで分割するのはUIライブラリのようにカプセル化できない限りおすすめしない
- UIデザインとデータ
  - フロントエンドのデザインはUIデザインに、設計自体に依存関係がある
  - UIデザインの論理に矛盾や複雑性があると、フロントエンドも複雑にならざるを得ない
    - 設計時点で話し合おう

:::

### 感想

- Viewモデルの考え方は確かに導入できると良さそう
- UIライブラリの設計においてはまた違ったアプローチが必要になりそう
- フロントエンドのデザインはUIデザインに依存する。ほんとにそう。UIデザインの構造とフロントエンドの構造の一致はかなり諦めている節はあるので、見直せると良いかも。
- UIデザイナーとしっかり話し合おう。ほんとそう。

## Testing Trophyは叫ばない

登壇者：Toms [@toms74209200](https://twitter.com/toms74209200) さん（アジェンダ）

https://fortee.jp/frontend-conf-hokkaido-2025/proposal/c4845087-a276-447f-84f8-6799b75a3fb8

@[speakerdeck](0e1b875c8f6e4751abc646c61cb4b970)

https://zenn.dev/toms74209200/articles/frontend-test-architecture

:::details 視聴中のメモ

- （裏の発表が早めに終わったので途中から）
- 関数、UI、E2Eに分けて考える
  - 何をテストすべきか：出力、状態、コミュニケーション
  - 関数：出力
  - UI：状態（GOOD）、コミュニケーション（OK）
  - E2E：状態（GOOD）、コミュニケーション（OK）
- テストサイズ
  - 関数：Small
  - UI: Small, Medium
  - E2E: Medium, Large
- 叫ぶアーキテクチャ
- （配信が止まってしまった）

:::

### 感想

- 断片的にしか視聴できていないので後で資料とかアーカイブとかじっくり見返したい...
- Testing Trophyの新解釈？フロントエンドのテストアーキテクチャの再考のような発表内容だったのかな → Testing Trophyの問題点を踏まえて、他の観点を持ってフロントエンドのテストアーキテクチャを考えてみようという話っぽかった
- 分類、テスト対象、サイズ、それぞれシンプルで分かりやすそう
- テストサイズによる名前付け、良さそう

## Viteのプラグインを作ると内部をイメージできるようになる

登壇者：ssssota [@ssssotaro](https://twitter.com/ssssotaro) さん

https://fortee.jp/frontend-conf-hokkaido-2025/proposal/7ff3a9e9-675f-411e-9481-442b87f6a546

@[stackblitz](https://stackblitz.com/edit/frontendo1?embed=1&file=vite.config.ts)

@[stackblitz](https://stackblitz.com/edit/frontendo2?embed=1&file=vite.config.ts)

（[資料公開予定はない](https://x.com/ssssotaro/status/1964198676147978426)代わりに、登壇中に作ったデモを共有してくださってました。）

:::details 視聴中のメモ

- デモ1: 存在しないモジュールをimportしたい on React
  - https://stackblitz.com/ で作っていく
  - vite.config.ts に作成する
  - loadメソッドでexport default
  - ViteのPluginでimportはできたが、TSではエラーになる → 型定義ファイルを作成して解決
- デモ2: デフォルトで読み込めないファイルをimportしたい on Svelte
  - jsonl を読み込ませる
  - transform()で.jsonlファイルをパースする
- resolveId, load, transform
  - ライフサイクルフック
  - resolveId: そのモジュールならここにあるよ
  - load: これ読み込んだらいいよ
  - transform: こういうJSコードだと解釈すればいいよ
- [Unplugin](https://github.com/unjs/unplugin)でWebpack向けに今回の知識が使える

:::

### 感想

- Plugin作成デモを通して、ViteでJavaScriptを実行するまでに挟まれる処理の解説。すごく分かりやすかった。
- [stackblitz](https://stackblitz.com/)ってこんな便利なのね。使ったことなかった。
- デモしながら解説しながらをスムーズにこなしててすごかった。アドリブでSvelteで書き出すの相当登壇慣れしてそう。
- よく分からないけどVite PluginでStyle Dictionaryみたいなの自作できそう。Style Dictionaryで足りてるけど。

## RSCの時代にReactとフレームワークの境界を探る

登壇者：uhyo [@uhyo_](https://twitter.com/uhyo_) さん（カオナビ）

https://fortee.jp/frontend-conf-hokkaido-2025/proposal/3ebf1951-dd48-4f1b-a0c7-9d2753291af4

@[speakerdeck](f9507e215f7f433d9ff957bbbfad771c)

:::details 視聴中のメモ

- RSC
- RSCはReact本体の機能だからこそ複数のフレームワークでサポートされている
- フレームワークはRSCの何をサポートしているのか
  - importで繋がったReactプログラムをサーバー側用とクライアント側用に分けて別々でビルドする
  - RSC対応の根本はバンドラのプラグインとして実装
- Reactフレームワークの構造
  - フレームワークの個性
  - バンドラプラグイン
  - バンドラの機能
  - Reactの機能
- [@vitejs/plugin-rsc](https://www.npmjs.com/package/@vitejs/plugin-rsc)
  - Wakuも使っているVite公式プラグイン
- ReactはRSC規約を定義し、フレームワークはそれを実装する（Web標準とブラウザの関係性に似ている）

:::

### 感想

- Reactが思ったよりも広範にフレームワーク化されることを意図して標準化や規約の策定を意識している方向性なのが分かった。
- React本体とフレームワークの関係性はもうWebブラウザにおけるECMAScriptのような関係性に近いというのが面白かった。ESはJSランタイムが必要なのと違ってReactはフレームワークなしでも動くという点がややこしい。
- 本質的にはバンドラのプラグインでサポートしているというのはなるほど
- React本体がより低レイヤー的な、標準化とかそういう振る舞いになっていくことでフレームワークの責務が重すぎるなあ、という印象。メンテナンスが大変そうすぎる


## Progressive Web Apps（PWA）は夢を見るか、夢になるか

登壇者：さざんか [@sasanqua_dev](https://twitter.com/sasanqua_dev) さん（関東の大学生、初北海道、初登壇）

https://fortee.jp/frontend-conf-hokkaido-2025/proposal/b769a8b3-ddc4-43c5-88a1-70c0b2f03a16

@[speakerdeck](db1a68c0947a46449d71712f1dc15508)

:::details 視聴中のメモ

- PWAの例：Qiita
- ブックマークとの違い
  - アイコンの参照元が違う（PWAはManifetsで定義されたもの）
  - app_idが指定される
  - 独立ウィンドウで立ち上がる
- PWAの仕組み
  - マニフェスト
    - [Web App Manifest](https://developer.mozilla.org/ja/docs/Web/Progressive_web_apps/Manifest) を定義するとインストールできる
  - サービスワーカー
    - Service Workerでキャッシュを利用することでオフラインでも動作する（Network Independent）
- キャッシュ戦略いくつか組める
  - ベストプラクティスは「キャッシュ更新付きキャッシュ優先」
- 既存のNext.jsプロジェクトにPWAを組み込む
  - [next-pwa](https://www.npmjs.com/package/next-pwa)を導入する
  - マニフェストを設定する
  - _document.tsxを設定する
- 「PWA」としての機能があるわけではなく、従来のWebアプリよりも優れた体験を提供するための手法
- Webだけでクロスプラとフォームアプリケーションが実装できるという夢

:::


### 感想

- PWAは手軽に実装できるが、やはり問題はプラットフォーマーの利益にならないこととインストールの障壁（App Storeに載せられない）
  - それこそEUとかデジタル庁とか公的機関が推進させていってOSネイティブの機能にアクセスできるAPIを生やしていってもらうしかないかも？分からない
- Google Play Storeには載せられるらしいというのは知らなかった。WebアプリのAndroid向け展開はこれで事足りそう？分からない

## ブラウザは「フロントエンド」を何から守っているのか？

登壇者：canalun [@i_am_canalun](https://twitter.com/i_am_canalun) さん（Flatt Security）

https://fortee.jp/frontend-conf-hokkaido-2025/proposal/7c5857c0-1350-4d80-ad56-d72d2753a634

https://docs.google.com/presentation/d/1SRlqYR7m4a9JcN9GblnByeQP7Mmwwoe8zTlQQDhqMJc/edit?slide=id.g37c40f6947a_0_63#slide=id.g37c40f6947a_0_63

:::details 視聴中のメモ

- インターネットは開発者もユーザーも攻撃されるのが怖い
- ブラウザが守ってくれている、本当なのか？
- ブラウザが守ってくれている「フロントエンド」の攻撃経路
  - オリジン完結：悪いページが直接攻撃
  - オリジン横断：良いページに間接的に攻撃
- Line of Death：信頼できる/できないの境界線
  - LoDだけではUI Spoofingは防げない
- ブラウザ頑張ってる
- UIの重なりを利用した攻撃が多い
- フィンガープリンティング、匿名で識別
  - 匿名なら追跡されて良いかというとそうではない、再識別やマイノリティの特定など問題が
- FirefoxのRFP
  - とにかく機能を削ぎ落とす
- HSTS Fingerprinting
  - httpsのON/OFFでバイナリを形成して識別
  - Braveは対策済み（Chromeも？）
- Same Origin Policy
- XS-leaks: 自サイトから他ドメインを攻撃する手段のひとつ
- COOPでWindow取得を防ぐ
- SOPでも意図的に防いでいない部分はある、COOPなどで防ぐ

:::

### 感想

- 分からん世界すぎた。知らないことの博覧会で面白かった。結局インターネット怖い。
- 「UIの重なりを使用した攻撃が多い」ということは、サイト自身がポップオーバーとかダイアログとかを減らしてなるべく重ならないUIデザインにすることがセキュリティ対策として一定の有効さを帯びてくるのかも？
- HSTS Fingerprinting面白すぎた。こんなんでもバイナリ作れるのか...
- 最新の自衛手段をもう少し勉強しようと思った。

## AI時代のUIはどこへ行く？

登壇者：Yusuke Wada  [@yusukebe](https://twitter.com/yusukebe) さん（Cloudflare developer advocate, Honoの作者）

https://fortee.jp/frontend-conf-hokkaido-2025/proposal/3228c84e-6748-4abb-ab6a-649375f0117a

@[speakerdeck](ef6c6884f07d42219038397393cea061)

:::details 視聴中のメモ

- AIとどうインタラクションしているか
  - チャット
- AI時代の前はWebページの閲覧
- Webページ作らなくて良くなるの？
- 本当にチャットでいいのか
  - チャットじゃなくてUIも欲しいシチュエーションはある（例：マップでの道順表示）
- Kent C. Dodds氏も同じような考え（今回の発表のベース）

https://www.epicai.pro/the-future-of-ai-interaction-beyond-just-text-w22ps

- UIは必要で、やり方が変わってくる
- AIとUIの関係性の分類
- 1. UIの中でAIを使う
  - 例：スプレッドシート + Gemini
- 2. AIがUIを作る
  - 例：ChatGPTのグラフ機能
  - ClaudeのArtifactsが面白い
    - Claudeにアイディアを伝えるだけでアプリ、ゲームが作れる
    - コードを見ると、React + Tailwind CSSで作ってる
    - ギャラリーに公開できる、API呼び出しのコスト負担はユーザー
- 3. AIがUIを受け取る
  - MCPからUIを受け取る
  - [MCP-UI](https://mcpui.dev/)
  - [New Content Type for "UI" by kentcdodds](https://github.com/modelcontextprotocol/modelcontextprotocol/discussions/1146)
    - サーバーSDKとクライアントSDKを提供
      - サーバーSDK
        - HTMLを直接送る
        - 外部URL（iframeURL）を送る
      - クライアントSDK
        - UIを描画する
    - デモ
      - ラーメンAPI https://github.com/yusukebe/ramen-api
      - ラーメン屋の一覧を取得、UIで表示、選択すると詳細を表示
      - 行き方を地図で描画

:::

### 感想

- パターン分けとそれぞれのアプローチの違いの説明が分かりやすかった
- Kent氏フロントエンドテストの人だと思ってたらいつの間にかAIどっぷりの人になっていた
- [MCP-UI](https://mcpui.dev/) すごおお
- やっぱ自分で作って試してみないとなあという気になった

## LT: すべてのinputに可視ラベルがあれば

登壇者：maiha [@mf_dew2](https://twitter.com/mt_dew2) さん（SmartHR）

https://fortee.jp/frontend-conf-hokkaido-2025/proposal/b3238100-2f91-47ce-b489-8bbc4c32287c

https://blog.mtdew2.com/lt-tsuketai-visible-label/

:::details 視聴中のメモ

- なぜ正しくラベルを設定することが大事か
  - ユーザーが入力に迷うことを防ぐ：ユーザビリティ
  - スクリーンリーダーなどにも情報を伝える：アクセシビリティ
  - 誤入力・意図しない情報の入力を防ぐ：セキュリティ
- ラベルがないがちな要素
  - 第一位：selectを使ったプルダウンメニュー
    - ラベルをつけて改善
    - ドロップダウンメニューに置き換える
- アクセシブルネーム漏れるあるある
  - 分割されたinput
    - 各インプットに設定する
    - なるべく使わない
- 本題！アクセシブルネームめっちゃつけるの難しかったUI
  - （時間の関係でスキップ）

:::

### 感想

- selectがラベルないがちなのは本当にそう
- 分割されたinputにも全部アクセシブルネームが必要、考えるのもコストかかるからなるべくやめようというのは確かに
- ここから本題！がめっちゃ聴きたかった
  - スライド読んだ感じは主に分割input系の名前の付け方難しい的な感じだった
  - 開始日/終了日に可視ラベル、個別でみたら必要なんだろうけど全体で見たら既にラベルがついているし...分からない

## LT: 「手軽で便利」に潜む罠。 Popover API を WCAG 2.2の視点で安全に使うには

登壇者：Taito [@taitotnk](https://twitter.com/taitotnk) さん（メディア企業のウェブエンジニア）

https://fortee.jp/frontend-conf-hokkaido-2025/proposal/c18c61b5-3685-4044-a30d-0f90faa3fd23

@[speakerdeck](b72ae33b58184486aabf31b2ee16d31c)

:::details 視聴中のメモ

- Popover APIのアクセシビリティ考慮できていますか？
  - escキーで閉じる
  - 表示/非表示をUAに伝える
  - フォーカス
- 1.3.2 意味のあるシーケンス
  - popoverのトリガーとコンテンツの間に関連しないコンテンツを記述している、これでも動作するが、読み上げ順序がおかしくなるリスク
- 2.4.11 隠されないフォーカス（最低限）
  - popover="manual"は自前で閉じる機能を実装する必要がある
- 4.1.2 name, role, value
  - popoverは対象のroleに影響しない → 適切なセマンティクスは実装者が考慮する

:::

### 感想

- Popoverだけで考慮が全て済むわけじゃないのは本当にそう
- Popoverに限らず最近のモダンなAPIとかHTML要素はどこまで担保してくれてどこまでは実装者の責任なのか、実装者はきちんと境界を理解しないといけない


## LT: iPhone Eye Tracking機能から学ぶやさしいアクセシビリティ

登壇者：Kaito Fujimura [@fujiyamaorange](https://twitter.com/fujiyamaorange) さん（マネーフォワード）

https://fortee.jp/frontend-conf-hokkaido-2025/proposal/f556053a-05f2-436b-a2d2-bd687ae2d47d

@[speakerdeck](2697cd6277d646a786a1d9abe20ad71b)

:::details 視聴中のメモ

- iOS 18からiPhone Eye Trackingが利用可能に
  - AIを使った視線操作のアクセシビリティ機能
- 操作に工夫が必要
  - スクロール操作ができない
  - スライダーUIも厳しい、アナログ的な操作
- Eye Tracking視点から考える実装
  - 要素の間隔を広げる
  - スクロール操作に対する工夫
- Eye Trackingの未来
  - アクセシビリティに限らず、ヘッドマウント型デバイスの台頭により今後も勢いは止まらない
  - 標準化されてほしい

:::

### 感想

- 知らない世界で面白かった。考慮点自体は細かいポインタ操作が困難な利用状況向けの考慮に近そう
- また新しい操作形態が出てきた！と思ったけど結局モバイルで使いやすいように作るのとあまり変わらないかも？いやでもスクロール操作とかスライダー操作とかはモバイルデバイスが得意な領域なのでまた別の考慮が必要になってきそう
- あるいは今後の標準化や機能拡張の次第ではスクロールとかはいい感じに他の操作と共存できる仕組みになりそう


## LT: おじいちゃんに優しいUIを作ってみた

登壇者：しょうた＠なつみかん [@nano72mkn](https://twitter.com/nano72mkn) さん（スターフェスティバル）

https://fortee.jp/frontend-conf-hokkaido-2025/proposal/84db23d7-e1ed-4111-988f-5aa893e3b0b7

@[speakerdeck](ba4ffdf620004cc494055ff6ba97fe64)

:::details 視聴中のメモ


- 視覚能力の低下
  - 老眼
  - コントラスト感度
  - 走査速度
- 運動コントロール
  - 手先の器用さ
  - 手と目の協調能力
- 認知能力
  - 短期記憶（ワーキングメモリ）
- フォントサイズ
  - 16px以上（[Health Literacy Online](https://odphp.health.gov/healthliteracyonline)基準）
- コントラスト
- タッチ領域
- システムフォントサイズを大きくしていたケース
  - 拡大の上限を設定する
  - レイアウトを切り替える

:::

### 感想

- おじいちゃん関係なく使いやすいUIの話だ
- と思ったらシステムフォントを拡大しているケースは確かにおじいちゃんというかシニア向けは特に気をつけたいポイント
  - 上限を設けるのはどうなんだろうか、フォントサイズが大きくなりすぎているのが意図せずなのか意図的なのかは利用者によりけりな気もする
  - レイアウトを切り替えたりきちんとテストして確認したりしよう、はほんとそう
- フォントサイズWCAGではっきりしてほしい〜ってずっと思ってたけど[Health Literacy Online](https://odphp.health.gov/healthliteracyonline)（HLO）なるもので定められていることを知らなかった。今後これ使っていこう。
  - というかHLO自体知らなかった。あとでちゃんと読んでおこう。


## 全体の感想

アクセシビリティの話題が盛りだくさんで面白かった。それだけコミュニティの関心が高まっているのか、単に興味のあるトークだけ聞いた私に偏りがあるだけか。はっきりとアクセシビリティの話題と示されていないトークでもWCAGについて言及があったり、フロントエンドエンジニアとして当然考慮すべきことだというのがより一層強まっている感じがしてとても良い。

テンポよく密度高くどんどん次のトークが始まるので、当初目星をつけていたトーク以外も気がついたら視聴していたし、どれも面白かった。

また、タイムテーブルは意図的なのか偶然なのか、トーク間で「前のトークで話してたあれのことだ！」みたいな連続性が度々あって良かった。WAI-ARIAとかフォームのエラーメッセージの説明があるトークの後に、エラーメッセージについてのより実践的なLTがあったり。Vite Pluginの実践デモがあった後に、RSCの話でVite Pluginの実装を見たり。カンファレンス全体のストーリー性みたいなのが感じられて聞きやすかったし楽しかった。

オンラインは多少時間がズレたり一部トラブルはあったものの、基本快適に視聴できた。むしろオンラインの方がルームの行き来が無くてブラウザタブを切り替えるだけだったので楽かも。（それはそれとして次は普通に北海道行きたい。）
