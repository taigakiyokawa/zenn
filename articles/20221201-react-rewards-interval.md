---
title: "react-rewards の紙吹雪を setInterval で繰り返す"
emoji: "🎉"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["react", "javascript", "frontend"]
published: true
---

Next.js で作っている静的サイトで紙吹雪をいい感じに散らしたくなったので、[`react-rewards`](https://github.com/thedevelobear/react-rewards) で色々試した記録です。
アドベントカレンダーが始まり各所が賑わう中、丁度いい感じの小ネタを全然関係ないところで放出して 2 週間後くらいに会社のアドベントカレンダーを担当する自分を追い込んでいくスタイルです。

## 結論

`setInterval` に渡す callback 関数を `useRef` 経由で渡すカスタムフックを使いつつ、 `useReward` が返す `isAnimating` で `reward()` の実行を制御するといい感じに繰り返せる。

@[codesandbox](https://codesandbox.io/embed/react-rewards-set-interval-bqlpps?fontsize=14&hidenavigation=1&module=%2Fsrc%2FApp.tsx&theme=dark&view=preview)

GitHub にも上げています 👇
https://github.com/taigakiyokawa/react-rewards-set-interval

```tsx:App.tsx
import { FC, useEffect } from "react";
import { useReward } from "react-rewards";
import { setInterval } from "timers";

const useInterval = (callback: () => void) => {
  const callbackRef = useRef(() => {});

  useEffect(() => {
    callbackRef.current = callback;
  }, [callback]);

  useEffect(() => {
    const timerId = setInterval(() => callbackRef.current(), 1000);
    return () => clearInterval(timerId);
  }, []);
};

export const App: FC = () => {
  const { reward, isAnimating } = useReward("rewardId", "confetti");

  useInterval(() => {
    if (!isAnimating) {
      reward();
    }
  });

  return (
    <div>
      <span id="rewardId" />
    </div>
  );
};
```

## 概要

Next.js で作っている静的サイトで紙吹雪をいい感じに散らしたくなった。

- トップページのキービジュアル領域内で使いたい。
- クラッカーみたいにパァンってしたい。
- **勝手に繰り返されるようにしたい。**
- 紙吹雪の色とか大きさをサイトのデザインに合わせてカスタマイズしたい。

[`react-confetti`](https://github.com/alampros/react-confetti) や [`react-tsparticles`](https://github.com/matteobruni/tsparticles) なども軽く試したが、最終的に以下でも紹介されている [`react-rewards`](https://github.com/thedevelobear/react-rewards) が圧倒的に使いやすかった。
https://blog.ojisan.io/react-rewards-osusume/

しかし、 react-rewards はボタンを押した時に紙吹雪が飛び出すといった能動的なユースケースのみを想定している。でもこれをいい感じに繰り返したい。

## まず `react-rewards` の使い方から

以下のように `useReward` というカスタムフックを使うだけで、ボタンクリックで簡単にいい感じの紙吹雪を出すことが出来る。

[README](https://github.com/thedevelobear/react-rewards) より引用

![](https://storage.googleapis.com/zenn-user-upload/13b4a20e224c-20221201.gif)

```tsx:App.tsx
import { FC } from "react";
import { useReward } from "react-rewards";

export const App: FC = () => {
  const { reward, isAnimating } = useReward("rewardId", "confetti");
  return (
    <button disabled={isAnimating} onClick={reward}>
      <span id="rewardId" />
      🎉
    </button>
  );
};
```

この `reward()` という関数をループで実行する環境さえ整えれば一定間隔で紙吹雪が出せそう。

## 試行錯誤

### Step 1. `useEffect` 内で使ってみる 🤔

App のレンダリングが更新されるタイミングで呼ばれるだけ。さみしい。

![](https://storage.googleapis.com/zenn-user-upload/be9fb6c018f4-20221202.gif)

```tsx:App.tsx
import { FC, useEffect } from "react";
import { useReward } from "react-rewards";

export const App: FC = () => {
  const { reward, isAnimating } = useReward("rewardId", "confetti");

  useEffect(() => {
    reward();
  }, [reward]);

  return (
    <div>
      <span id="rewardId" />
    </div>
  );
};
```

### Step 2. `setInterval` にそのまま `reward()` を渡してみる 🤔

`reward()` 実行中に次の `reward()` が実行されるので放置していると処理が重複してきてめっちゃ詰まってくる。

タブ開いたまましばらくして戻ってきたらブワァってなる。

![](https://storage.googleapis.com/zenn-user-upload/ce2fa59ce138-20221201.gif)

```diff tsx:App.tsx
+ import { setInterval } from "timers";
...
    useEffect(() => {
+     const timerId = setInterval(() => {
        reward();
+     }, 1000);
+     return () => clearInterval(timerId);
    }, [reward]);
...
```

:::details この時点のコードの全体像

```tsx:App.tsx
import { FC, useEffect } from "react";
import { useReward } from "react-rewards";
import { setInterval } from "timers";

export const App: FC = () => {
  const { reward, isAnimating } = useReward("rewardId", "confetti");

  useEffect(() => {
    const timerId = setInterval(() => {
      reward();
    }, 1000);
    return () => clearInterval(timerId);
  }, [reward]);

  return (
    <div>
      <span id="rewardId" />
    </div>
  );
};
```

:::

### Step 3. `reward()` を useRef 経由で `setInterval` に渡すカスタムフックを用意する 🤔

以下で言及されているように、 useRef を使って callback 関数の更新を参照しながら使うと良さそう。
https://zenn.dev/akhr_s/articles/065e18ab3c4883

しかし結局処理は詰まる。

```diff tsx:App.tsx

- import { FC, useEffect } from "react";
+ import { FC, useEffect, useRef } from "react";
...
+ const useInterval = (callback: () => void) => {
+   const callbackRef = useRef(() => {});
+
+   useEffect(() => {
+     callbackRef.current = callback;
+   }, [callback]);
+
+   useEffect(() => {
+     const timerId = setInterval(() => callbackRef.current(), 1000);
+     return () => clearInterval(timerId);
+   }, []);
+ };
...

-   useEffect(() => {
-     const timerId = setInterval(() => {
+   useInterval(() => {
-       reward();
+     reward();
-      }, 1000);
-     return () => clearInterval(timerId);
-   }, [reward]);
+   });
...
```

:::details この時点のコードの全体像

```tsx:App.tsx
import { FC, useEffect, useRef } from "react";
import { useReward } from "react-rewards";
import { setInterval } from "timers";

const useInterval = (callback: () => void) => {
  const callbackRef = useRef(() => {});

  useEffect(() => {
    callbackRef.current = callback;
  }, [callback]);

  useEffect(() => {
    const timerId = setInterval(() => callbackRef.current(), 1000);
    return () => clearInterval(timerId);
  }, []);
};

export const App: FC = () => {
  const { reward, isAnimating } = useReward("rewardId", "confetti");

  useInterval(() => {
    reward();
  });

  return (
    <div>
      <span id="rewardId" />
    </div>
  );
};
```

:::

### Step 4. `isAnimating` で実行を制御する 🎉

`useReward` が返す真偽値 `isAnimating` が `false` の時は `reward()` を実行しないようにする。これで処理が詰まることなく安全に一定間隔で実行される。最初からこれを使うべきだった。

```diff tsx:App.tsx
...
   useInterval(() => {
+    if (!isAnimating) {
      reward();
+    }
   });
...
```

:::details この時点のコードの全体像

```tsx:App.tsx
import { FC, useEffect } from "react";
import { useReward } from "react-rewards";
import { setInterval } from "timers";

const useInterval = (callback: () => void) => {
  const callbackRef = useRef(() => {});

  useEffect(() => {
    callbackRef.current = callback;
  }, [callback]);

  useEffect(() => {
    const timerId = setInterval(() => callbackRef.current(), 1000);
    return () => clearInterval(timerId);
  }, []);
};

export const App: FC = () => {
  const { reward, isAnimating } = useReward("rewardId", "confetti");

  useInterval(() => {
    if (!isAnimating) {
      reward();
    }
  });

  return (
    <div>
      <span id="rewardId" />
    </div>
  );
};
```

:::

あとは諸々仕上げていい感じしたら完成 🎉

@[codesandbox](https://codesandbox.io/embed/react-rewards-set-interval-bqlpps?fontsize=14&hidenavigation=1&module=%2Fsrc%2FApp.tsx&theme=dark&view=preview)

GitHub にも上げています 👇
https://github.com/taigakiyokawa/react-rewards-set-interval

## `react-rewards` を繰り返し使う時の注意点

### デフォルトで `position: fixed` なのでスクロールしてもついてくるぞ！

![](https://storage.googleapis.com/zenn-user-upload/2a73cf55fdea-20221201.gif)

#### 解決策:

ある表示領域内に留めたい時は `useReward` の第 3 引数に position 指定の変更などができる [config オブジェクト](https://github.com/thedevelobear/react-rewards#props--config) を渡せるので、そこで `position: "absolute"` など `fixed` 以外を指定してあげよう！

```tsx:App.tsx
...
const { reward, isAnimating } = useReward("rewardId", "confetti", {
  position: "absolute",
});
...
```

### config に `position: "absolute"` を渡している時に `viewport` からはみ出るように紙吹雪を飛ばすとレイアウトが崩れるぞ！

デフォルトの `position: fixed` なら問題ない。
繰り返し実行しなくても注意。

![](https://storage.googleapis.com/zenn-user-upload/2d0fa9182dc0-20221201.gif)

### 解決策:

親要素に `position: relative` と `overflow: hidden` を付与しよう！

```css:styles.css
.parent {
  position: relative;
  overflow: hidden;
}
```

```tsx:App.tsx
...
const { reward, isAnimating } = useReward("rewardId", "confetti", {
  position: "absolute",
});
...
return (
  <div className="parent">
    <span id="rewardId" />
  </div>;
)

```

## 参考リンク

- [2021 年の紙吹雪ライブラリは react-rewards がオススメ | blog.ojisan.io](https://blog.ojisan.io/react-rewards-osusume/)
- [React で setInterval を安全に使うためのカスタムフックを考える](https://zenn.dev/akhr_s/articles/065e18ab3c4883)
- [thedevelobear/react-rewards](https://github.com/thedevelobear/react-rewards)
- [alampros/react-confetti](https://github.com/alampros/react-confetti)
- [taigakiyokawa/react-rewards-set-interval](https://github.com/taigakiyokawa/react-rewards-set-interval)
