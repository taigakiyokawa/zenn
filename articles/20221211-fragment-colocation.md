---
title: "GraphQL の Fragment Colocation を導入したら依存関係がスッキリしてクエリもコンポーネントも書きやすくなった"
emoji: "🐕"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["graphql", "apollo", "graphqlcodegen", "typescript", "frontend"]
published: true
publication_name: "moneyforward"
---

この記事は [Money Forward Engineering 1 Advent Calendar 2022](https://adventar.org/calendars/7397) 11 日目の投稿です 🎄
昨日 10 日目は [cabossoldir](https://zenn.dev/cabos) さんによる 『[コードレビューのとき、私は何をレビューしているのか？](https://zenn.dev/cabos/articles/a657c458e2f234)』 でした。

---

## 🙈 TL;DR

- [Fragment Colocation](https://www.apollographql.com/docs/react/data/fragments/#colocating-fragments) とは、**コンポーネントが必要とするデータを Fragment にまとめてコンポーネントと同じ場所に配置 (co-locate) すること**
- Fragment Colocation を導入することで、「Query や Mutation を実行するコンポーネント」と、「それらの結果を必要とするコンポーネント」との**関心の分離**ができる
- Query, Mutation, Fragment はそれを実行するあるいは必要とするコンポーネントと同じファイル内に宣言すると依存関係が見やすく、変更がしやすくなる
- Fragment を元に生成した型を Props の型として使用することで、Props の保守性を高く保てる
- Query, Mutation, Fragments を元に生成する型や hooks は、 GraphQL Code Generator の [`near-operation-file-preset`](https://the-guild.dev/graphql/codegen/plugins/presets/near-operation-file-preset) を使うことで、コンポーネントファイルと colocate でき、適切な単位にファイル分割することでバンドルサイズを減らすことが出来る

簡単な実装例はこちら 👇

https://github.com/taigakiyokawa/fragment-colocation-sample-app/pull/6

---

こんにちは、[マネーフォワード クラウド会計](https://biz.moneyforward.com/accounting/)のフロントエンドエンジニアの [taigakiyokawa](https://twitter.com/taigakiyokawa) です。

年の瀬を感じる今日この頃ですが、いよいよ来週は [M-1 グランプリ 2022](https://www.m-1gp.com/) 決勝戦ですね。今年もどの組が優勝するのか全く予想がつきません。自分は真空ジェシカ、ダイヤモンド、キュウ、ウエストランド、そしてヨネダ 2000 を応援しています。というかもはや全組優勝してほしいです。

さて、クラウド会計では現在、モノリシックな Rails アプリケーションからフロントエンドを分離するプロジェクトを進めており、フロントエンドに [Next.js](https://nextjs.org/) を使用し、バックエンドとのやり取りには [GraphQL](https://graphql.org/) を使用しています。そこで今回は、 GraphQL の **Fragment Colocation** というコンセプトを導入した背景やメリット、実装方針を簡単な例と合わせて紹介していきたいと思います。

## 🍵 前提

### 対象読者

- 下記使用技術の基礎的なことが分かる方
- サーバーサイドとのやり取りに GraphQL を使用している、あるいは使用したいと思っているフロントエンドエンジニアの方
- GraphQL はなんとなく扱えるけど、 Fragment をあまり使っていない、あるいは知らない方
- GraphQL の Query や Mutation と、それらを使用する React Component との依存関係が見通しづらくなってきた方

### 使用技術

:::details 主な使用技術のバージョン

- `node`: 18.12.1
- `typescript`: 4.9.4
- `react`: 18.2.0
- `next`: 13.0.6
- `@apollo/client`: 3.7.2
- `graphql`: 16.6.0
- GraphQL Code Generator:
  - `@graphql-codegen/cli`: 2.16.1
  - `@graphql-codegen/near-operation-file-preset`: 2.4.4
  - `@graphql-codegen/typescript`: 2.8.5
  - `@graphql-codegen/typescript-operations`: 2.5.10
  - `@graphql-codegen/typescript-react-apollo`: 3.3.7

:::

## 🌃 背景

Fragment Colocation の導入前は、 GraphQL の Query や Mutation を `src/graphql/` というディレクトリ以下に `queries/FetchFooPage.graphql` や `mutations/UpdateBar.graphql` のように、 React Component とは別のファイルを作成して宣言していました。

しかし、宣言している Query や Mutation が `src/components/` にある各コンポーネントと離れているため、各コンポーネントが必要とするデータと `src/graphql/` にある Query や Mutation との対応関係が見づらくなっていました。また、 Query や Mutation に変更を加えたい時にも、変更対象となるファイルが多く、無駄な工数が生じていました。

そこでより良い設計にリファクタリングしていく議論を進めていた中で、Fragment Colocation の提案があり、導入することにしました。

## 🐸 GraphQL の Fragment とは

GraphQL では、複数の Query や Mutation で共有したいフィールドをまとめて **Fragment** として宣言することができます。

https://graphql.org/learn/queries/#fragments

https://www.apollographql.com/docs/react/data/fragments

## 🦩 Fragment Colocation とは

Fragment とそのデータ使用するコンポーネントと同じ場所に配置 (co-locate) することを一般的に、 **"Fragment Colocation"** や [**"Colocating Fragments"**](https://www.apollographql.com/docs/react/data/fragments/#colocating-fragments) と呼びます。

### Fragment Colocation をすると何がうれしいのか

Fragment Colocation には主に以下のようなメリットがあると考えています。

- 「Query や Mutation を実行するコンポーネント」と、「それらの結果を必要とするコンポーネント」との**関心の分離**ができること
- Fragment のフィールドを編集することで、 Query を直接編集することなく取得したい値の更新ができること
- Fragment を元に生成した型を Props の型として使用することで、Props の保守性を高く保てること
- React Component と同じ場所に宣言することで、コンポーネントとクエリの依存関係が分かりやすくなること
- GraphQL Code Generator による型や hooks の生成ファイルを、colocate した単位で分割することで、巨大な単一の生成ファイルを読み込む場合と比べてバンドルサイズを減らすことができること
  - ref: [next.js でのファイルチャンク最適化の一例 - hiroppy's site](https://hiroppy.me/blog/nextjs-chunk-optimization)

詳しくは [Quramy](https://github.com/Quramy) さんの [GraphQL Workshop Chapter 3. コロケーション](https://github.com/Quramy/gql-study-workshop/blob/0362d8cbbbc6198ce5fc2c4675a9f293675e77ae/chapters/03_colocation.md) を読むとよく分かります。

https://github.com/Quramy/gql-study-workshop/tree/main/chapters

## 🧭 実装方針

チームで議論した結果、Fragment Colocation 導入後の実装方針として以下の事項を [ADR](https://adr.github.io/) にまとめました。

- GraphQL の Query, Mutation, そして Fragment は React Components と同じファイルに宣言すること
- Fragment の名前は `<ComponentName>_<TypeName>`[^1] で宣言すること
- GraphQL Code Generator の `near-operation-file` preset を使用して各コンポーネントディレクトリ下に型やカスタムフックを生成すること

[^1]: [Relay の命名規則](https://relay.dev/docs/guided-tour/rendering/fragments/#:~:text=Fragment%20names%20need,the%20same%20module.) を参考にしています。

## 🍛 具体例

以下のコンポーネントで構成される簡単なプロフィールページのようなものを使って、 Fragment Colocation 前後の比較をします。

- `ViewerPage`: Query を実行するページコンポーネント
- `Profile`: ユーザーの名前と自己紹介文を表示するコンポーネント
- `PostList`: ユーザーの投稿一覧を表示するコンポーネント

![Sample App Components Image](https://storage.googleapis.com/zenn-user-upload/8b2360c28970-20221210.png)

サンプルアプリケーションの全体像はこちら 👇
https://github.com/taigakiyokawa/fragment-colocation-sample-app

### Schema

```graphql:schema.graphql
type User {
  id: ID!
  name: String!
  bio: String
  posts: [Post!]!
}

type Post {
  id: ID!
  date: String!
  title: String!
}

type Query {
  viewer: User!
}
```

### Before Colocated Fragments

![Correspondence between a query and components before using fragments](https://storage.googleapis.com/zenn-user-upload/18c219c23013-20221210.png)

Before の状態の全体像はこちら 👇
https://github.com/taigakiyokawa/fragment-colocation-sample-app/tree/before-colocate-fragments

:::details graphql/FetchViewerPage.graphql

```graphql:FetchViewerPage.graphql
query FetchViewerPage {
  viewer {
    id
    name
    bio
    posts {
      id
      date
      title
    }
  }
}
```

:::

:::details components/ViewerPage.tsx

```tsx:ViewerPage.tsx
import { useFetchViewerPageQuery } from '../../graphql/__generated__/graphql-types'

export const ViewerPage: FC = () => {
  const { data } = useFetchViewerQuery()

  if (!data) {
    <p>Loading...</p>
  }

  const {
    viewer: { name, bio, posts },
  } = data

  return (
    <div>
      <Profile name={name} bio={bio} />
      <PostList posts={posts} />
    </div>
  )
}
```

:::

:::details components/Profile.tsx

```tsx:Profile.tsx
type Props = {
  name: string
  bio: string | null
}

export const Profile: FC<Props> = ({ name, bio }) => {
  return (
    <div>
      <h1>{name}</h1>
      <p>{bio}</p>
    </div>
  )
}
```

:::

:::details components/PostList.tsx

```tsx:PostList.tsx
type Props = {
  posts: {
    id: string
    title: string
  }[]
}

export const PostList: FC<Props> = ({ posts }) => {
  return (
    <div>
      <h2>Posts:</h2>
      <ul>
        {posts.map(({ id, title }) => {
          <li key={id}>{title}</li>;
        })}
      </ul>
    </div>
  )
}
```

:::

:::details codegen.ts

```ts:codegen.ts
import type { CodegenConfig } from '@graphql-codegen/cli'

const config: CodegenConfig = {
  schema: 'graphql/schema.graphql',
  generates: {
    'graphql/__generated__/graphql-types.ts': {
      documents: 'graphql/**/*.graphql',
      plugins: [
        'typescript',
        'typescript-operations',
        'typescript-react-apollo',
      ],
    },
  },
}
export default config
```

:::

### After Colocated Fragments

![Correspondence between a query and components after using fragments](https://storage.googleapis.com/zenn-user-upload/abea95699f04-20221210.png)

Before との差分はこちらの PR にまとめてあります 👇
https://github.com/taigakiyokawa/fragment-colocation-sample-app/pull/6

:::details components/ViewerPage.tsx

```tsx:ViewerPage.tsx
import { useFetchViewerPageQuery } from './__generated__'

gql`
  query FetchViewerPage {
    viewer {
      id
      ...Profile_User
      posts {
        ...PostList_Post
      }
    }
  }
`

export const ViewerPage: FC = () => {
  const { data } = useFetchViewerPageQuery()

  if (!data) {
    return <p>Loading...</p>
  }

  const {
    viewer: { name, bio, posts },
  } = data

  return (
    <div>
      <Profile name={name} bio={bio} />
      <PostList posts={posts} />
    </div>
  )
}

```

:::

:::details components/Profile.tsx

```tsx:Profile.tsx
type Props = {
  name: string
  bio: string | null
}

export const Profile: FC<Props> = ({ name, bio }) => {
  return (
    <div>
      <h1>{name}</h1>
      <p>{bio}</p>
    </div>
  )
}
```

:::

:::details components/PostList.tsx

```tsx:PostList.tsx
import { PostList_PostFragment } from './__generated__'

gql`
  fragment PostList_Post on Post {
    id
    date
    title
  }
`

type Props = {
  posts: PostList_PostFragment[]
}

export const PostList: FC<Props> = ({ posts }) => {
  return (
    <div>
      <h2>Posts</h2>
      <ul>
        {posts.map(({ id, date, title }) => {
          return (
            <li key={id}>
              {date}: {title}
            </li>
          )
        })}
      </ul>
    </div>
  )
}
```

:::

:::details codegen.ts

```ts:codegen.ts
import type { CodegenConfig } from '@graphql-codegen/cli'

const config: CodegenConfig = {
  schema: 'graphql/type-defs.ts',
  generates: {
    'graphql/__generated__/graphql-schema-types.ts': {
      plugins: ['typescript'],
    },
    'components/': {
      documents: 'components/**/index.tsx',
      preset: 'near-operation-file',
      plugins: ['typescript-operations', 'typescript-react-apollo'],
      presetConfig: {
        baseTypesPath: '../graphql/__generated__/graphql-schema-types.ts',
        folder: '__generated__',
        extension: '.ts',
        importTypesNamespace: 'SchemaTypes',
      },
    },
  },
}
export default config
```

:::

### Before/After の比較

|                                  | Before Colocated Fragments                       | After Colocated Fragments                                                          |
| -------------------------------- | ------------------------------------------------ | ---------------------------------------------------------------------------------- |
| Query                            | コンポーネントと別ファイルに宣言                 | **実行するコンポーネントと同じ場所に宣言**                                         |
| Fragment                         | 未使用                                           | **そのデータを必要とするコンポーネントと同じ場所に宣言し、Query の組み立てに使用** |
| Props                            | `graphql-codegen` で生成される型とは別で定義     | **`graphql-codegen` で Fragment を元に生成した型を使用**                           |
| 新しくフィールドを追加したい場合 | Query を直接更新して、 対応する Props も更新する | **対象の Fragment を更新するのみ**                                                 |

今回はとても簡単なコンポーネントでの例を紹介しましたが、より大規模で複雑な実装になっていくにつれて、この差による効果はとても大きなものになります。

## 🎯 まとめ

- [Fragment Colocation](https://www.apollographql.com/docs/react/data/fragments/#colocating-fragments) とは、**コンポーネントが必要とするデータを Fragment にまとめてコンポーネントと同じ場所に配置 (co-locate) すること**
- Fragment Colocation を導入することで、「Query や Mutation を実行するコンポーネント」と、「それらの結果を必要とするコンポーネント」との**関心の分離**ができる
- Query, Mutation, Fragment はそれを実行するあるいは必要とするコンポーネントと同じファイル内に宣言すると依存関係が見やすく、変更がしやすくなる
- Fragment を元に生成した型を Props の型として使用することで、Props の保守性を高く保てる
- Query, Mutation, Fragments を元に生成する型や hooks は、 GraphQL Code Generator の [`near-operation-file-preset`](https://the-guild.dev/graphql/codegen/plugins/presets/near-operation-file-preset) を使うことで、コンポーネントファイルと colocate でき、適切な単位にファイル分割することでバンドルサイズを減らすことが出来る

---

最後までお読みいただきありがとうございました。明日 12 日目は masami morita さんによる『Agile Testing Days 2022 の参加レポ』です！お楽しみに〜 👋🏻 🎅🏻
https://adventar.org/calendars/7397

---

## 🔗 参考リンク

- [Fragments - GraphQL](https://graphql.org/learn/queries/#fragments)
- [Fragments - Apollo GraphQL Docs](https://www.apollographql.com/docs/react/data/fragments/)
- [Colocating Fragments - Apollo GraphQL Docs](https://www.apollographql.com/docs/react/data/fragments/#colocating-fragments)
- [Generated files colocation – GraphQL Code Generator](https://the-guild.dev/graphql/codegen/docs/advanced/generated-files-colocation)
- [near-operation-file-preset – GraphQL Code Generator](https://the-guild.dev/graphql/codegen/plugins/presets/near-operation-file-preset)
- [Fragments - Relay](https://relay.dev/docs/guided-tour/rendering/fragments/)
- [GraphQL Workshop Chapter 3. コロケーション - Quramy/gql-study-workshop](https://github.com/Quramy/gql-study-workshop/blob/0362d8cbbbc6198ce5fc2c4675a9f293675e77ae/chapters/03_colocation.md)
- [コンポーネントと GraphQL クエリの管理に Fragment Colocaction を導入したら素晴らしかった件 - Uzabase for Engineers](https://tech.uzabase.com/entry/2022/06/30/164016)
- [GraphQL の Fragment でコンポーネントの見通しがよくなった話 - vivit engineering blog](https://vivit.hatenablog.com/entry/2020/05/22/193021)
- [next.js でのファイルチャンク最適化の一例 - hiroppy's site](https://hiroppy.me/blog/nextjs-chunk-optimization)
- [next.js/examples/api-routes-apollo-server-and-client](https://github.com/vercel/next.js/tree/9d97a1e34a8a6e09eb127292c730d1a8df63ebb6/examples/api-routes-apollo-server-and-client)
- [taigakiyokawa/fragment-colocation-sample-app](https://github.com/taigakiyokawa/fragment-colocation-sample-app)
