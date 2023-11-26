---
title: "WCAG2.2の新達成基準\"3.3.7 Redundant Entry\"を理解する"
emoji: "🔁"
published: false
publication_name: "moneyforward"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["accessibility", "アクセシビリティ", "wcag"]
---

この記事は、[Money Forward Engineering 2 Advent Calendar 2023](https://adventar.org/calendars/9263) 1日目の投稿です🎄

---

本記事は、2023年10月5日に正式な勧告（Recommendation）となった[Web Content Accessibility Guidelines (WCAG) 2.2](https://www.w3.org/TR/WCAG22/)において、新たに追加されたLevel Aの達成基準（Success Criterion）のひとつである「[3.3.7 Redundant Entry](https://www.w3.org/TR/WCAG22/#redundant-entry)」の概要と達成方法について、[Understanding Doc](https://www.w3.org/WAI/WCAG22/Understanding/redundant-entry.html)をもとに私見をまとめたものです。より正確な詳細は参照元のドキュメントをご覧ください。

WCAG 2.2 Level Aのもう一つの新達成基準である「[3.2.6 Consistent Help](https://www.w3.org/TR/WCAG22/#consistent-help)」についても同様にまとめたので、よろしければ以下の記事も合わせてご覧ください。

https://zenn.dev/moneyforward/articles/20231201-wcag22-326-consistent-help

## まとめ

- WCAG 2.2 Success Criterion 3.3.7 Redundant Entryとは、ユーザーによって以前入力された情報を再入力する必要がある場合、一部の例外を除いて、その情報が自動入力できるか、ユーザーによって選択可能となっていることを要求する達成基準です
- この達成基準は、複数のステップを持つ一連の手順をユーザーが正しく完了できるようにすることを目的としており、認知や記憶が困難なユーザーにとってストレスの軽減やミスの可能性を減らすことにつながります
- この達成基準を満たすには、ブラウザに頼らずウェブサイト自体が再入力の必要な情報を特定し、それを保持して提供する方法を考える必要があります

## 想定する読者

- アクセシビリティに興味がある方
- WCAG2.2で新しく追加された達成基準「3.3.7 Redundant Entry」についておおまかに知りたい方
- WCAGを読んだことがない方、あるいは読んだけど理解が難しかった方
- アクセシビリティの専門家の方（ぜひご意見いただきたいです）

## 3.3.7 Redundant Entry（冗長な入力）とは

ユーザーによって以前に入力された情報や提供されたことがある情報を再入力する必要がある時、以下の例外を除いて、その情報が自動入力できるか、ユーザーによって選択可能となっていること。

### 例外

- 記憶を必要とするゲームなど、本質的に再入力が不可欠な場合
- パスワードの再入力など、セキュリティの確保に必要な場合
- 以前に入力された情報が有効でない場合

### 意図

この達成基準の意図は、ユーザーが複数ステップの手順を正しく完了することができるようにすることです。一連の手順の中で、同じ情報を複数回要求される場合の認知的努力を軽減することができます。

入力時に記憶を必要とする情報は、認知や記憶が困難なユーザーにとって大きな障壁となります。すべてのユーザーは、一連の手順のステップを進めるにつれて、自然と徐々に精神的な疲労を感じており、学習障害や認知障害を持つユーザーはそれを特に感じやすいです。

ブラウザの機能であるautocompleteでは不十分です。ウェブサイト自体が保存された情報を提供したり、再度同じ情報を入力することを避ける必要があります。

同じセッション内で同じ情報の入力を求めないようにすることであり、セッション間での情報の保持を要求するものではありません。手順は、あるアクティビティを完了するための一連のユーザーアクションのことを示し、セッションを閉じたり後から戻ってきた場合には適用されない。ただし、手順は異なるドメインで実行される可能性があります。

選択可能（"available to select"）とは、ユーザーが以下のような操作を行えることを含みます。

- ドロップダウンなどから選択する
- ページ上のテキストから選択し、コピーして入力する
- チェックボックスにチェックを入れて、以前入力した値と同じ値を入力する（例：請求先住所は届け先住所と同じです）

選択可能なデータは入力箇所と同じページ上にある必要があります。理想的には可視かつ入力箇所に近い場所にあると良いですが、表示/非表示に関わらずページ内のどこかにあれば良いです。

この達成基準は、文書のアップロードなどのように、データが（フォームへのテキスト入力などと）異なる方法で提供された場合には適用されません。

この達成基準はパスワードの自動入力などが要求される[Accessible Authentication (Minimum)](https://www.w3.org/WAI/WCAG22/Understanding/accessible-authentication-minimum)^[[Understanding Success Criterion 3.3.7: Redundant Entry | WAI | W3C](https://www.w3.org/WAI/WCAG22/Understanding/redundant-entry.html)の該当箇所はリンクが切れており、正しくは[WCAG 2.2 - "Accessible Authentication" link in the Understanding document of SC 3.3.7 Redundant Entry is broken - Editorial · Issue #3519 · w3c/wcag](https://github.com/w3c/wcag/issues/3519)に紐づくPRにて修正予定。]には影響しません。

冗長な入力（Redundant Entry）とは、以前に入力した情報を利用できるようにウェブサイトに要求することであり、セッション間やパスワード入力などでは要求しません。

### 利点

認知障害を持つユーザーのうち、短期記憶が困難な方にとって、ストレスの軽減やミスの可能性を減らすことができます。また、音声入力やスイッチ操作などを使用しているような運動障害を持つユーザーにとっては、文字入力を減らせること自体に利点があります。

## 具体例

### Amazon

Amazonでは、届け先住所と請求先住所が同じ場合、請求先住所の入力を省略することができます。

![Amazonの決済画面のスクリーンショット。「1 受取場所」のセクションに届け先住所が設定されていて「2 お支払い方法」のセクションにVISAカードが設定されており、その下に「お支払方法に紐づいた住所：お届け先と同じです。」と記載されている](https://storage.googleapis.com/zenn-user-upload/bd767515570a-20231126.png)

あるいは、アドレス帳から請求先住所を選択することもできます。

![Amazonの決済画面から請求先住所を選択というタイトルのモーダルダイアログを表示したスクリーンショット。アドレス帳に保存した住所の一覧と、それぞれの住所の隣にラジオボタンが表示されている](https://storage.googleapis.com/zenn-user-upload/24c11454e64d-20231126.png)

## 達成方法

具体的な達成方法については特に言及されていないため、各種ドキュメントを参考に自分の中で考えた達成方法をまとめます。

### 設計

設計としては、以下のようにサイト上でユーザーが行う可能性のある一連の手順の中で同じ情報の再入力が発生する場合を特定し、その情報の提供方法を決める必要があります。

1. サイト上でユーザーが行うある一連の手順の中で、[例外](#例外)を除いて同じ情報の入力を複数回要求することがある場合を特定する
   - 例：ユーザー情報の設定や更新を行う手順の中で、ユーザーの氏名や生年月日、住所などの情報の入力を複数回要求する場合
   - 例：自分の住所を入力した後に、同世帯の家族の住所を入力する手順がある場合
2. その情報の再入力時に、以下のいずれかの方法でその情報を提供する
   - 自動で入力する
   - 同じページ上にテキストで表示され、コピー可能にする
   - ドロップダウンやチェックボックスなどで選択可能にする


### 実装

実装については、再入力が必要な情報の提供はブラウザのautocompleteでは不十分という言及から、ブラウザのローカルストレージなどでもなくウェブサイト自体が保持して提供する必要があると解釈します。

あくまで同一のセッション内での情報の保持と提供を求めているため、この達成基準としては必ずしもデータベースなどに永続的に保存する必要はありませんが、ユーザーに再入力させる情報はアプリケーション内で保存されていると便利なケースが多いはずなので永続的な保存も考慮した方が良さそうです。

この達成基準を満たすための情報保持の方法としては、Reactの`useState`など、フロントエンドのフレームワークや状態管理のライブラリを利用すると、入力された情報を保持する実装がしやすそうです。

### テスト

この達成基準を自動テストによって担保することは不可能です。目視によって上記の設計が実現できているかどうかを確かめる必要があります。（参考：[How to test 3.3.7 Redundant Entry - TPGi](https://www.tpgi.com/how-to-test-3-3-7-redundant-entry/)）

## まとめ

- WCAG 2.2 Success Criterion 3.3.7 Redundant Entryとは、ユーザーによって以前入力された情報を再入力する必要がある場合、一部の例外を除いて、その情報が自動入力できるか、ユーザーによって選択可能となっていることを要求する達成基準です
- この達成基準は、複数のステップを持つ一連の手順をユーザーが正しく完了できるようにすることを目的としており、認知や記憶が困難なユーザーにとってストレスの軽減やミスの可能性を減らすことにつながります
- この達成基準を満たすには、ブラウザに頼らずウェブサイト自体が再入力の必要な情報を特定し、それを保持して提供する方法を考える必要があります

---

WCAG 2.2 Level Aのもう一つの新達成基準である「[3.2.6 Consistent Help](https://www.w3.org/TR/WCAG22/#consistent-help)」についても同様にまとめたので、よろしければ以下の記事も合わせてご覧ください。

https://zenn.dev/moneyforward/articles/20231201-wcag22-326-consistent-help

## 関連URL

- [Web Content Accessibility Guidelines (WCAG) 2.2](https://www.w3.org/TR/WCAG22/)
- [Success Criterion 3.3.7 Redundant Entry - Web Content Accessibility Guidelines (WCAG) 2.2](https://www.w3.org/TR/WCAG22/#redundant-entry)
- [Understanding Success Criterion 3.3.7: Redundant Entry | WAI | W3C](https://www.w3.org/WAI/WCAG22/Understanding/redundant-entry.html)
- [How to test 3.3.7 Redundant Entry - TPGi](https://www.tpgi.com/how-to-test-3-3-7-redundant-entry/)
