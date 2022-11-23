---
title: "UTC の値を任意のタイムゾーンの日時に変換したい時は dayjs.tz(date) ではなく dayjs(date).tz() を使おう"
emoji: "📆"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["javascript", "dayjs", "frontend"]
published: true
---

[Day.js の.tz.setDefault()が動かないと思ったけど使い方が間違ってただけだった | DevelopersIO](https://dev.classmethod.jp/articles/day-js-timezone-set-default/) をしっかり読んだ上で変なところでハマった可哀想な人の雑記です。

## TL; DR

- `dayjs.tz(date)` 👉 `date` の日時**そのまま** `tz.setDefault()` で設定したタイムゾーンの日時として返す。 ref: [Parsing in Zone · Day.js](https://day.js.org/docs/en/timezone/parsing-in-zone)
- `dayjs(date).tz()` 👉 `date` を `tz.setDefault()` で設定したタイムゾーンの日時に**変換して**返す。 ref: [Converting to Zone · Day.js](https://day.js.org/docs/en/timezone/converting-to-zone)
- 現在の日時のインスタンスを生成する時は `dayjs.tz(undefined)` でも `dayjs(undefined).tz()` でも同じ結果が返る。
- 公式のドキュメントをちゃんと読もう。

## `dayjs.tz()` と `dayjs().tz()` の使い方を間違えていた

`dayjs.tz.setDefault()` でデフォルトのタイムゾーンを設定している時、現在の日時のインスタンスを生成する時は `dayjs.tz()` でも `dayjs().tz()` でも同じ結果が返る。ただし、例えば ISO8601 拡張形式の UTC の日時の文字列 `date` を引数に渡す時は `dayjs.tz(date)` だと UTC の日時そのままデフォルトのタイムゾーンが設定される。なので、UTC をデフォルトのタイムゾーンの日時に変換したい時は `dayjs(date).tz()` を使う。

※ OS とブラウザのタイムゾーンも `"Asia/Tokyo"` (JST) だと `tz.setDefault()` が効いているのか分かりにくいので、 `"America/New_York"` (EST) に変更して検証しています。

```typescript
import dayjs from "dayjs";
import utc from "dayjs/plugin/utc";
import timezone from "dayjs/plugin/timezone";

dayjs.extend(utc);
dayjs.extend(timezone);
dayjs.tz.setDefault("Asia/Tokyo");

const date = "2022-11-22T00:00:00Z";
dayjs.tz(date).format(); // 2022-11-22T00:00:00+09:00 😇
dayjs(date).tz().format(); // 2022-11-22T09:00:00+09:00 👍

// Assume the current date and time is "Mon Nov 21 2022 19:00:00 GMT-0500 (Eastern Standard Time)"
dayjs.tz(undefined).format(); // 2022-11-22T09:00:00+09:00
dayjs(undefined).tz().format(); // 2022-11-22T09:00:00+09:00
```

それぞれの挙動の違いはちゃんと公式ドキュメントに書いてあった 🙈

- `dayjs.tz(date)` 👉 [Parsing in Zone · Day.js](https://day.js.org/docs/en/timezone/parsing-in-zone)
  > Parse date-time string in the given timezone and return a Day.js object instance.
- `dayjs(date).tz()` 👉 [Converting to Zone · Day.js](https://day.js.org/docs/en/timezone/converting-to-zone)
  > Change the time zone and update the offset and return a Day.js object instance.

---

## ついでにハマったポイント

### なんか OS のタイムゾーン変えて検証してみたら `tz.setDefault()` のタイムゾーンが効いてない気がしたけどそんなことはなかった

OS (とブラウザ) のタイムゾーンを EST にした状態で `dayjs(date).tz()` のインスタンスをそのまま `console.log` して確認すると、時間は JST になっているが `GMT-0500 (Eastern Standard Time)`と書かれているためパッと見 EST が適用されているように見えて焦る。

![](https://storage.googleapis.com/zenn-user-upload/2f8d69490a4f-20221123.png)

しかしオブジェクトを展開するとちゃんと `dayjs.tz.setDefault('Asia/Tokyo')` したタイムゾーンが設定されていて、なおかつ format の結果も JST になっていた。

![](https://storage.googleapis.com/zenn-user-upload/0c2bcc5e5f76-20221123.png)

### `dayjs(null)` はエラー、 現在の日時を引数ありで返したい時は `dayjs(undefined)` か `dayjs(new Date())`

`dayjs` を `timezone` プラグインで拡張した状態でラップした関数を定義した時に、 引数が必須の `dayjs.tz()` に引数なしで現在の日時を返すようにするためにデフォルトの引数を `null` にしていたらエラーになった。

```typescript
import type { ConfigType, Dayjs } from "dayjs";
import dayjs from "dayjs";
import utc from "dayjs/plugin/utc";
import timezone from "dayjs/plugin/timezone";

dayjs.extend(utc);
dayjs.extend(timezone);
dayjs.tz.setDefault("Asia/Tokyo");

// Will return the error "Invalid Date" when the argument is null 😇
export const customDayjs = (date: ConfigType = null): Dayjs => {
  return dayjs.tz(date);
};
```

引数が `undefined` だと現在の日時が返る。

```typescript
...

// Will return the current date and time when the argument is undefined 👍
export const customDayjs = (date: ConfigType = undefined): Dayjs => {
  return dayjs.tz(date)
}
```

[Day.js の公式ドキュメント](https://day.js.org/docs/en/parse/now)にも書いてあるのでちゃんと読もう。

> Day.js treats `dayjs(null)` as an invalid input.

## 参考リンク

- [Day.js の.tz.setDefault()が動かないと思ったけど使い方が間違ってただけだった | DevelopersIO](https://dev.classmethod.jp/articles/day-js-timezone-set-default/)
- [Timezone · Day.js](https://day.js.org/docs/en/plugin/timezone)
- [Parsing in Zone · Day.js](https://day.js.org/docs/en/timezone/parsing-in-zone)
- [Converting to Zone · Day.js](https://day.js.org/docs/en/timezone/converting-to-zone)
- [Set Default Timezone · Day.js](https://day.js.org/docs/en/timezone/set-default-timezone)
- [Now · Day.js](https://day.js.org/docs/en/parse/now)
