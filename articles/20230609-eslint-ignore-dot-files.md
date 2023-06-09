---
title: "ESLint は暗黙的に dot-files や dot-folders を無視する"
emoji: "🦌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["eslint", "frontend"]
published: true
publication_name: "moneyforward"
---

タイトルの通り、 ESLint はピリオドで始まるファイルやフォルダ (dot-files や dot-folders と呼ぶらしい) を暗黙的に無視します。
社内で定期的に行っている技術顧問の[古川陽介さん](https://twitter.com/yosuke_furukawa)への相談会にて、「ESLint の対象にしている `.js` ファイルがあるのに実行時に無視される」という相談があり、古川さんからの指摘を元に公式ドキュメントを読んでみたらそういう挙動でした。

## 関連する技術のバージョン

- `node`: 18.16.0
- `npm`: 9.5.1
- `eslint`: 8.41.0

## 問題

あるプロジェクトで以下のような npm script で eslint を実行していたところ、 glob パターンにマッチする `.js` のうち、 lint 対象のファイルがないという warning が発生していました。

```json
{
  "scripts": {
    "lint": "eslint \"./**/*.{js,ts,tsx}\""
  }
}
```

```zsh
npm run lint
> eslint "./**/*.{js,ts,tsx}"

Oops! Something went wrong! :(

ESLint: 8.41.0.

You're linting "./**/*.js", but all of the files matching the glob pattern "./**/*.js" are ignored.
```

このプロジェクトでは `.eslintrc` の `ignorePatterns` にいくつかの JS ファイルを指定していますが、そこに指定していない JS ファイル = ESLint が効いてほしいファイルがいくつかありました。
例えば、 `.storybook/` 以下の `main.js` や `preview.js` がそれに相当します。しかし、これらは ESLint に無視されています。

## 原因

`.eslintrc` の `ignorePatterns` や `.eslintignore` などで設定したファイルに加えて、 ESLint はデフォルトで暗黙的に `node_modules/` 以下のファイルと `.eslintrc.*` 以外の dot-files や dot-folders 以下のファイルを無視する挙動でした。

[Ignore Files - ESLint - Pluggable JavaScript Linter](https://eslint.org/docs/latest/use/configure/ignore) より

> In addition to any patterns in the .eslintignore file, ESLint always follows a couple of implicit ignore rules even if the --no-ignore flag is passed. The implicit rules are as follows:
>
> - `node_modules/` is ignored.
> - dot-files (except for `.eslintrc.*`) as well as dot-folders and their contents are ignored.

この挙動により、上記の例で挙げたような `.storybook/` 以下の JS ファイルが無視されていたということでした。

## 解決策

`.eslintrc.*` の `ignorePatterns` に以下のように、対象の glob パターンに `!` プレフィックスを付与して dot-folders を無視しないようにすることで ESLint が有効になります。

```js:.eslintrc.js
module.exports = {
  // ...
  ignorePatterns: [
    '!**/.storybook/*'
  ],
  // ...
}
```

## 余談

今回の問題の対象となった Storybook の設定ファイルですが、 v7 より `StorybookConfig` という型が提供されるようになったので TypeScript での記述が可能になっています。

https://storybook.js.org/docs/react/configure/overview

## 関連 URL

- [Ignore Files - ESLint - Pluggable JavaScript Linter](https://eslint.org/docs/latest/use/configure/ignore)
- [Configure Storybook](https://storybook.js.org/docs/react/configure/overview)
- [Yosuke Furukawa（@yosuke_furukawa）さん / Twitter](https://twitter.com/yosuke_furukawa)
