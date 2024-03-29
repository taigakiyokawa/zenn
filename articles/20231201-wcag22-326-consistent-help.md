---
title: "WCAG2.2の新達成基準\"3.2.6 Consistent Help\"を理解する"
emoji: "❓"
published: true
published_at: 2023-12-01 09:00
publication_name: "moneyforward"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["accessibility", "アクセシビリティ", "wcag"]
---

この記事は、[Money Forward Engineering 1 Advent Calendar 2023](https://adventar.org/calendars/9141) 1日目の投稿です☝🏻🎅🏻

---

本記事は、2023年10月5日にW3Cの正式な勧告（Recommendation）となった[Web Content Accessibility Guidelines (WCAG) 2.2](https://www.w3.org/TR/WCAG22/)にて、新たに追加されたLevel Aの達成基準（Success Criterion）のひとつである「[3.2.6 Consistent Help](https://www.w3.org/TR/WCAG22/#consistent-help)」の概要と達成方法について、[Understanding Doc](https://www.w3.org/WAI/WCAG22/Understanding/consistent-help.html)をもとに私見をまとめたものです。達成基準自体のより正確な詳細は参照元のドキュメントをご覧ください。

また、WCAG 2.2 Level Aのもうひとつの新達成基準である「[3.3.7 Redundant Entry](https://www.w3.org/TR/WCAG22/#redundant-entry)」についても同様にまとめたので、ご興味がありましたら以下の記事もご覧ください。

https://zenn.dev/taigakiyokawa/articles/20231201-wcag22-337-redundant-entry

## まとめ

- WCAG 2.2 Success Criterion 3.2.6 Consistent Helpとは、複数のWebページが何らかのヘルプの仕組みを提供している場合、その仕組みがページ内で相対的に同じ順序で提供されていることを要求するLevel Aの達成基準です
- この達成基準は、ユーザーがヘルプを見つけやすくすることを目的としており、問題の解決にサポートが必要なユーザーにとって役に立ちます
- この達成基準を満たすためには、グローバルなヘッダーやフッターのようなアプリケーション全体の共通コンポーネントにヘルプの仕組みを配置することで、ページ間での相対的な位置の一貫性が担保しやすくなると考えます
- この達成基準のチェックは自動テストで担保することは不可能なため、目視およびスクリーンリーダーなどの支援技術を使った手動チェックによって、提供されているヘルプの仕組みの相対位置の一貫性を確認する必要があります

## 想定する読者

- アクセシビリティに興味がある方
- WCAG2.2で新しく追加された達成基準「3.2.6 Consistent Help」について大まかに知りたい方
- WCAGやWAIのドキュメント読んだことがない方、あるいは読んだけど理解が難しかった方
- Webアクセシビリティの専門家の方（ぜひご意見・ご指摘いただきたいです）

## 3.2.6 Consistent Help（一貫したヘルプ）とは

WCAG 2.2 Success Criterion 3.2.6 Consistent Helpとは、あるWebページのまとまりの中で、以下のようなヘルプの仕組み（help mechanisms）が提供されている場合、ユーザーによって変更が開始されない限り、そのページ群の他のコンテンツに対して相対的に同じ順序でヘルプの仕組み（help mechanism）を提供することを要求するLevel Aの達成基準です。

### ヘルプの仕組み（Help Mechanisms）の種類

この達成基準におけるヘルプの仕組みとは、以下のようなものを指します。フォーム内の説明テキストやツールチップのようなインターフェースレベルのヘルプは含まれません。

- 有人の問い合わせ先の詳細：電話番号やemailアドレス、営業時間など
- 有人の問い合わせの仕組み：チャットクライアント、ソーシャルメディアのアカウントなど
- ユーザー自身が探すヘルプオプション：FAQやサポートページなど
- 全自動の問い合わせの仕組み：チャットボットなど

### 意図

この達成基準の意図は、ヘルプが利用可能ならばユーザーがそれを見つけやすくすることです。

ヘルプの仕組みを提供することや、常に有人対応できることを要求するものではありません。目的の異なるウェブページの集合（set of Web pages）の中で、それぞれ異なるヘルプの仕組みが用意されるのは問題ありませんが、それぞれで一貫した位置にヘルプの仕組みを提供する必要があります。また、ヘルプの仕組みはアプリケーション全体で一貫しているとなお良いとされています。

### 例外

「ユーザーによって変更が開始された（"a change is initiated by the user."）」という例外については、例えば、ユーザーがページの拡大・縮小やビューポートを変更しページレイアウトが変化したようなケースを意図しています。この基準は、同じページのバリエーション内（モバイル用やデスクトップ用など）において、それぞれ一貫した位置にヘルプの仕組みを提供することを要求しています。

### 利点

一貫したヘルプの配置は、ヘルプを見つけることが困難な人にとって見つけやすくなる可能性があります。

特に、何かの障害を持つユーザーがWebサイト上で何かのタスクを行おうとした時に、問い合わせなどのサポートがなければ問題の解決が困難な場合があります。その時に、ヘルプが見つけやすい位置に配置されていることで、ユーザーがタスクを放棄することなく完了できる可能性が高まります。

## 具体例

### Understanding Docs

WAIが作成しているWCAG 2.2のUnderstanding Docsの場合、いずれのページにおいても一貫してページ最下部およびフッターに問い合わせ先の詳細が配置されています。

![WCAG 2.2のUnderstanding Docsの最下部およびフッターに配置されている、メールアドレスやソーシャルメディアへのリンクなどの問い合わせ先](https://storage.googleapis.com/zenn-user-upload/dbd53abccf47-20231124.png)

### マネーフォワード クラウド会計

マネーフォワード クラウド会計では、以下のようなヘルプの仕組みをアプリケーション全体で提供しています。

1. ヘッダーにあるクエスチョンマークのアイコンボタンから、サポートページやFAQページへのリンクがメニューとして展開される
2. ヘッダーに「このページのガイド」と書かれたリンクが配置されており、クリックするとサポートサイトの中にあるそのページの使い方ガイドページへ遷移する（該当するガイドが存在する場合のみリンクが表示される）
3. 画面右下にあるチャットウィジェットを展開すると、AIチャットボットのあかりさんによる対応が開始されて、有人オペレーターの営業時間を記載している

![マネーフォワード クラウド会計で提供している上記3つのヘルプの仕組みを示した、ホーム画面のスクリーンショット](https://storage.googleapis.com/zenn-user-upload/f2c9efa25f81-20231127.png)

## 達成方法を考える

この達成基準の具体的な達成方法についてはUnderstanding Docではあまり詳しく言及されていないため、各種ドキュメントを参考に、どうすれば達成できるのかを考えてみます。（参考：[How to test 3.2.6 Consistent Help - TPGi](https://www.tpgi.com/how-to-test-3-2-6-consistent-help/)）

### 「相対的に同じ順序」という表現について

この達成基準の本文やUnderstanding Docでも度々、ヘルプの仕組みが「相対的に同じ位置（the same order relative to .../the same relative order）」という表現が使われています。そして、この表現は、そのあとにかかる"other page content"の範囲がどの程度かによりますが、対象のページ群で共通するコンポーネント中でのヘルプの仕組みと他のコンテンツとの位置の前後関係が一貫していることを意味していると考えられます。例えば、ヘルプページへのリンクは常にヘッダーのロゴの後ろに配置される、メールアドレスは常にフッターの最後に配置される、といったイメージです。

### ページ間の共通部品にヘルプの仕組みを配置する

例えばグローバルなヘッダーやフッター、サイドバーなど、実装によっては `<Layout>` コンポーネントのように定義して複数のページで使い回す部品がある場合、そのコンポーネント内にヘルプの仕組みを配置することで、ページ間で一貫したヘルプの仕組みを提供しやすくなります。

また、相対的な位置については、共通コンポーネント内でのコンテンツとヘルプの位置関係のみを考慮すれば良くなるため、その一貫性を担保しやすくなります。

### ヘルプの仕組みをアプリケーション全体で一貫させる

この達成基準では、あくまで目的とバリエーションが同じページの集合において一貫したヘルプの仕組みを提供することを要求しています。同じひとつのWebアプリケーション内であっても、ページによって目的が大きく異なる場合は、それらは達成基準を満たすべきページのまとまり（set of Web pages）とはみなされません。しかし、ページの目的ごとに異なる仕組みを提供するよりも、アプリケーション全体で一貫したヘルプの仕組みを提供することで、設計時の迷いや実装のブレを少なくすることができ、ユーザーにとってもより使いやすいヘルプの仕組みを提供できると考えます。

ただ、ビューポートの変化などによるページの変化については、全てのバリエーションで一貫した配置でヘルプを提供することは難しいため、例えばブレークポイントなどレイアウトが変化するいくつかのケースでそれぞれ一貫した配置ができると良さそうです。

### テスト

この達成基準を自動テストによって担保することは不可能です。ヘルプの仕組みを提供しているページ群を、目視およびスクリーンリーダーなどの支援技術を使って一貫性を確認する必要があります。

ヘルプの仕組みと他のコンテンツとの相対的な位置関係については、視覚的な順序ではなく、DOM上の順序で一貫している必要があります。

## （余談）認知や学習への障害を持つ人へのサポート

この達成基準では要求されていませんが、障害者へのサポートとして知っておくと良いことについて、Understanding DocのIntentセクション内の「Support for people with cognitive and learning disabilities」の項目を日本語でまとめて紹介します。

まず、有人のサポート（human contact details）が受けられることは、認知障害を持つ人にとっては、人と話して自分の言葉で探しているものを表現することが問題の解決につながる最善策となる可能性があります。

そして、有人のサポートが受けられないページの場合は、障害のあるユーザーにとってはサポートが受けられるのかどうかも分からない場合があるため、ページ内に「有人のサポートが受けられない」旨を明記しておくと役立ちます。有人か無人かに関わらず、ヘルプは一貫した位置に提供することで、認知障害のあるユーザーがどんなヘルプが利用できるのか把握しやすくなります。

また、ユーザー自身が解決策を見つけるためのヘルプオプション（Self-help options）は、サイト内検索ができるだけでは不十分で、ユーザーが現在実行している機能に関する情報を提供できること（Contextual help, [context-sensitive help](https://www.w3.org/WAI/WCAG22/Understanding/help#dfn-context-sensitive-help)）が推奨されます（詳細は[Success Criterion 3.3.5 Help](https://www.w3.org/WAI/WCAG22/Understanding/help)を参照）。

## まとめ

- WCAG 2.2 Success Criterion 3.2.6 Consistent Helpとは、複数のWebページが何らかのヘルプの仕組みを提供している場合、その仕組みがページ内で相対的に同じ順序で提供されていることを要求するLevel Aの達成基準です
- この達成基準は、ユーザーがヘルプを見つけやすくすることを目的としており、問題の解決にサポートが必要なユーザーにとって役に立ちます
- この達成基準を満たすためには、グローバルなヘッダーやフッターのようなアプリケーション全体の共通コンポーネントにヘルプの仕組みを配置することで、ページ間での相対的な位置の一貫性が担保しやすくなると考えます
- この達成基準のチェックは自動テストで担保することは不可能なため、目視およびスクリーンリーダーなどの支援技術を使った手動チェックによって、提供されているヘルプの仕組みの相対位置の一貫性を確認する必要があります

---

また、WCAG 2.2 Level Aのもうひとつの新達成基準である「[3.3.7 Redundant Entry](https://www.w3.org/TR/WCAG22/#redundant-entry)」についても同様にまとめたので、ご興味がありましたら以下の記事もご覧ください。

https://zenn.dev/taigakiyokawa/articles/20231201-wcag22-337-redundant-entry

## 関連URL

- [Web Content Accessibility Guidelines (WCAG) 2.2](https://www.w3.org/TR/WCAG22/)
- [Success Criterion 3.2.6 Consistent Help - Web Content Accessibility Guidelines (WCAG) 2.2](https://www.w3.org/TR/WCAG22/#consistent-help)
- [Understanding Success Criterion 3.2.6: Consistent Help | WAI | W3C](https://www.w3.org/WAI/WCAG22/Understanding/consistent-help.html)
- [Understanding Success Criterion 3.3.5: Help | WAI | W3C](https://www.w3.org/WAI/WCAG22/Understanding/help)
- [How to test 3.2.6 Consistent Help - TPGi](https://www.tpgi.com/how-to-test-3-2-6-consistent-help/)
