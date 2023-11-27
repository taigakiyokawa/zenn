---
title: "WCAG2.2の新達成基準\"3.2.6 Consistent Help\"を理解する"
emoji: "❓"
published: false
publication_name: "moneyforward"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["accessibility", "アクセシビリティ", "wcag"]
---

この記事は、[Money Forward Engineering 1 Advent Calendar 2023](https://adventar.org/calendars/9141) 1日目の投稿です🎄

---

本記事は、2023年10月5日に正式な勧告（Recommendation）となった[Web Content Accessibility Guidelines (WCAG) 2.2](https://www.w3.org/TR/WCAG22/)において、新たに追加されたLevel Aの達成基準（Success Criterion）のひとつである「[3.2.6 Consistent Help](https://www.w3.org/TR/WCAG22/#consistent-help)」の概要と達成方法について、[Understanding Doc](https://www.w3.org/WAI/WCAG22/Understanding/consistent-help.html)をもとに私見をまとめたものです。より正確な詳細は参照元のドキュメントをご覧ください。

WCAG 2.2 Level Aのもう一つの新達成基準である「[3.3.7 Redundant Entry](https://www.w3.org/TR/WCAG22/#redundant-entry)」についても同様にまとめたので、よろしければ以下の記事も合わせてご覧ください。

https://zenn.dev/taigakiyokawa/articles/20231201-wcag22-337-redundant-entry

## まとめ

- WCAG 2.2 Success Criterion 3.2.6 Consistent Helpとは、Webページがヘルプの仕組みを提供している場合、ヘルプの位置や機能が同じ目的を持つページ間で一貫していることを要求する達成基準です
- この達成基準は、ユーザーがヘルプを見つけやすくすることを目的としており、特に認知障害や学習障害のあるユーザーにとって役に立ちます
- グローバルなヘッダーやフッターのようなアプリケーション全体の共通部品にヘルプの仕組みを配置することで、ページ間で一貫したヘルプの仕組みを提供することで、この達成基準を満たしやすくできると考えます
- この達成基準は自動テストによって担保できないため、目視およびスクリーンリーダーなどの支援技術を使った手動チェックによって一貫性を確認する必要があります

## 想定する読者

- アクセシビリティに興味がある方
- WCAG2.2で新しく追加された達成基準「3.2.6 Consistent Help」についておおまかに知りたい方
- WCAGを読んだことがない方、あるいは読んだけど理解が難しかった方
- アクセシビリティの専門家の方（ぜひご意見いただきたいです）

## 3.2.6 Consistent Help（一貫したヘルプ）とは

あるWebページのまとまりの中で、有人ないしは自動応答によるチャットサポートやヘルプページへの導線などが提供されている場合、そのページ群の中において一貫した順序、位置にヘルプの仕組み（help mechanism）を提供すること。

### ヘルプの仕組み（Help Mechanisms）の種類

- 人間への問い合わせ先の詳細：電話番号やemailアドレス、営業時間など
- 人間への問い合わせの仕組み：チャットクライアント、ソーシャルメディアなど
- ユーザー自身が探すヘルプオプション：FAQやサポートページなど
- 全自動の問い合わせの仕組み：チャットボットなど

### 意図

この達成基準の意図は、ヘルプが利用可能ならばユーザーがそれを見つけやすくすることです。

ヘルプ機構の設置や、常に人間が対応できることを要求するものではありません。目的の異なるウェブページの集合（set of Web pages）の中で、それぞれ異なるヘルプ導線が用意されるのは問題ありませんが、それぞれで一貫した仕組みを提供する必要があります。また、ヘルプの仕組みはアプリケーション全体で一貫しているとなお良いです。

「ユーザーによって変更が開始された（"a change is initiated by the user."）」という例外については、例えば、ユーザーがページの拡大・縮小やビューポートを変更しページレイアウトが変化したようなケースを意図しています。この基準は、同じページのバリエーション内において一貫した順序や配置でヘルプ導線を置くことを要求しています。

### メリット

一貫したヘルプの配置は、ヘルプを見つけることが難しかった人にとって見つけやすくなる可能性があります。

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

## 達成方法

具体的な達成方法については特に言及されていないため、各種ドキュメントを参考に自分の中で考えた達成方法をまとめます。

### ページ間の共通部品にヘルプの仕組みを配置する

例えばグローバルなヘッダーやフッター、サイドバーなど、実装によっては `<Layout>` コンポーネントのように定義して複数のページで使い回す部品がある場合、その部品にヘルプの仕組みを配置することで、ページ間で一貫したヘルプの仕組みを提供しやすくなります。

### ヘルプの仕組みをアプリケーション全体で一貫させる

この達成基準では、あくまで目的とバリエーションが同じページの集合において一貫したヘルプの仕組みを提供することを要求しています。しかし、ページの目的ごとに異なる仕組みを提供するよりも、アプリケーション全体で一貫したヘルプの仕組みを提供することで、設計時の迷いや実装のブレを少なくすることができ、ユーザーにとってもより使いやすいヘルプの仕組みを提供できると考えます。

### テスト

この達成基準を自動テストによって担保することは不可能です。ヘルプの仕組みを提供しているページ群を、目視およびスクリーンリーダーなどの支援技術を使って一貫性を確認する必要があります。（参考：[How to test 3.2.6 Consistent Help - TPGi](https://www.tpgi.com/how-to-test-3-2-6-consistent-help/)）

## （余談）認知や学習への障害を持つ人へのサポート

この達成基準では要求されていませんが、障害者へのサポートとして知っておくと良いことについて。

人間によるサポート（human contact details）が受けられる場合、認知障害を持つ人は、人と話せるということだけでも問題の解決につながる可能性があります。

人間によるサポートが受けられないページの場合は、障害のあるユーザーにとってはサポートが受けられるのかどうかも分からない場合があるため、ページ内に「人間によるサポートが受けられない」旨を記載しておくと役立ちます。ヘルプは単一の位置に提供することで、認知障害のあるユーザーがどんなヘルプが利用できるのか把握しやすくなります。

ユーザー自身が解決策を見つけるためのヘルプオプション（Self-help options）は、サイト内検索ができるだけでは不十分です。ユーザーが現在実行している機能に関する情報を提供できること（Contextual help, [context-sensitive help](https://www.w3.org/WAI/WCAG22/Understanding/help#dfn-context-sensitive-help)）が推奨されます（詳細は[Success Criterion 3.3.5 Help](https://www.w3.org/WAI/WCAG22/Understanding/help)を参照）。

## まとめ

- WCAG 2.2 Success Criterion 3.2.6 Consistent Helpとは、Webページがヘルプの仕組みを提供している場合、ヘルプの位置や機能が同じ目的を持つページ間で一貫していることを要求する達成基準です
- この達成基準は、ユーザーがヘルプを見つけやすくすることを目的としており、特に認知障害や学習障害のあるユーザーにとって役に立ちます
- グローバルなヘッダーやフッターのようなアプリケーション全体の共通部品にヘルプの仕組みを配置することで、ページ間で一貫したヘルプの仕組みを提供することで、この達成基準を満たしやすくできると考えます
- この達成基準は自動テストによって担保できないため、目視およびスクリーンリーダーなどの支援技術を使った手動チェックによって一貫性を確認する必要があります

---

WCAG 2.2 Level Aのもう一つの新達成基準である「[3.3.7 Redundant Entry](https://www.w3.org/TR/WCAG22/#redundant-entry)」についても同様にまとめたので、よろしければ以下の記事も合わせてご覧ください。

https://zenn.dev/taigakiyokawa/articles/20231201-wcag22-337-redundant-entry

## 関連URL

- [Web Content Accessibility Guidelines (WCAG) 2.2](https://www.w3.org/TR/WCAG22/)
- [Success Criterion 3.2.6 Consistent Help - Web Content Accessibility Guidelines (WCAG) 2.2](https://www.w3.org/TR/WCAG22/#consistent-help)
- [Understanding Success Criterion 3.2.6: Consistent Help | WAI | W3C](https://www.w3.org/WAI/WCAG22/Understanding/consistent-help.html)
- [Understanding Success Criterion 3.3.5: Help | WAI | W3C](https://www.w3.org/WAI/WCAG22/Understanding/help)
- [How to test 3.2.6 Consistent Help - TPGi](https://www.tpgi.com/how-to-test-3-2-6-consistent-help/)
