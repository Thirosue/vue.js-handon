## ユニットテストの実装

一覧画面のテストを実装していきます。

### ベースファイルを作成する

`tests/unit/example.spec.js` をコピーして、 `tests/unit/app.spec.js` を作成してください。

`yarn test:unit` コマンドを実行し、新しく作成したファイルのテストがパスすることを確認します。

```bash
$ yarn test:unit
yarn run v1.17.3
$ vue-cli-service test:unit
 PASS  tests/unit/app.spec.js  <-------- 追加される
 PASS  tests/unit/example.spec.js

Test Suites: 2 passed, 2 total
Tests:       2 passed, 2 total
Snapshots:   0 total
Time:        1.104s
Ran all test suites.
✨  Done in 2.08s.
```

正常終了したら、 `tests/unit/example.spec.js` は不要なため、`tests/unit/example.spec.js_` にリネームしてください。

### コンテンツの確認

`tests/unit/app.spec.js` は、元々存在した `HelloWorld.vue` をテストしているため、同様に以下のとおり変更します。

* 変更前

```javascript
import { shallowMount } from '@vue/test-utils'
import HelloWorld from '@/components/HelloWorld.vue'

describe('HelloWorld.vue', () => {
  it('renders props.msg when passed', () => {
    const msg = 'new message'
    const wrapper = shallowMount(HelloWorld, {
      propsData: { msg }
    })
    expect(wrapper.text()).toMatch(msg)
  })
})
```

* 変更後

タイトルが正常に表示されること (＝コンテンツに含まれること) を確認してみます。

```javascript
import { shallowMount } from '@vue/test-utils'
import App from '@/App.vue' //<----　変更

describe('App.vue', () => { //<----　変更
  it('タイトルが表示されること', () => { //<----　変更
    const wrapper = shallowMount(App) //<----　変更
    console.log(wrapper.text()) //<----　変更
    expect(wrapper.text()).toContain('担当者一覧') //<----　変更
  })
})
```

`yarn test:unit` コマンドを実行し、テストが正常終了することを確認します。

```bash
$ yarn test:unit
yarn run v1.17.3
$ vue-cli-service test:unit
 PASS  tests/unit/app.spec.js
  App.vue
    ✓ タイトルが表示されること (24ms)

  console.log tests/unit/app.spec.js:7
    担当者一覧 名前  苗字  Search
        []

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        0.815s, estimated 1s
Ran all test suites.
✨  Done in 1.69s.
```

### 全件検索のテスト

実際のプロジェクトでは、DataBaseの状態に応じて、検索結果は変わります。

検索結果が変化するとテストの検証結果も変化するため、検索の結果を一定に保つように準備します。

#### (事前準備) 検索結果を特定しやすくするためのid属性の追加

`src/App.vue` の `<template>` セクションの検索結果に `id` を付与します。

* 変更前

```html
      <section class="hero"> <!-- <--- 変更  -->
        <table
          v-if="0 < results.length"
          class="table is-bordered is-striped is-narrow is-hoverable is-fullwidth"
        >
```

* 変更後

```html
      <section id="result-list" class="hero"> <!-- <--- id追加  -->
        <table
          v-if="0 < results.length"
          class="table is-bordered is-striped is-narrow is-hoverable is-fullwidth"
        >
```

#### fetch関数のモック化

`Jest` では `mock` [https://jestjs.io/docs/ja/mock-functions] をサポートしているため、`fetch` 関数をモックにします。

APIの結果や事前に準備した以下より結果をコピーして、`result.json` の名前で `tests/unit/result.json` に保存します。

http://localhost:18081/admin/api/staff

https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/result.json

これでAPIの一定に保つ準備はできたため、`fetch` 関数を以下のとおりモックにします。

* 変更前

```javascript
import { shallowMount } from '@vue/test-utils'
import App from '@/App.vue'

（....後略....）
```

* 変更後

用意した `json` を読み込んで、`window.fetch API` を `mock` 実装します。

以下により、APIは毎回同じ結果を返すようになります。

```javascript
import { shallowMount } from '@vue/test-utils'
import App from '@/App.vue'

//fetchのモック化
const results = require('./result.json')
window.fetch = jest.fn().mockImplementation(() =>
  Promise.resolve({
    status: 200,
    ok: true,
    json: () => results
  })
)

（....後略....）
```

#### 検索のテスト

検索ボタンをクリックして、検索結果一覧が正常に取得できることを確認します。

以下のとおりテストコードを修正しましょう。

* 変更前

```javascript
（....前略....）

  it('タイトルが表示されること', () => {
    const wrapper = shallowMount(App)
    console.log(wrapper.text())
    expect(wrapper.text()).toContain('担当者一覧')
  })
})
```

* 変更後

全件検索のテストケースを追加します。

```javascript
（....前略....）

  it('タイトルが表示されること', () => { //<----　変更
    const wrapper = shallowMount(App) //<----　変更
    console.log(wrapper.text()) //<----　変更
    expect(wrapper.text()).toContain('担当者一覧') //<----　変更
  })

//↓↓↓↓↓↓↓↓↓↓↓↓↓↓　追加
  it('全件検索を実行したとき、検索結果が0件以上となること', () => {
    const wrapper = shallowMount(App)

    //検索ボタンクリック
    wrapper.find('#form-submit').trigger('click')

    const resultList = wrapper.findAll('#result-list tr')
    expect(resultList.length).not.toEqual(0) //0 以上
    console.log(resultList.at(2).text())
  })
//↑↑↑↑↑↑↑↑↑↑↑↑↑↑　追加
})
```

`yarn test:unit` コマンドを実行し、テスト結果を確認してみます。

```bash
$ yarn test:unit
yarn run v1.17.3
$ vue-cli-service test:unit
 FAIL  tests/unit/app.spec.js
  App.vue
    ✓ タイトルが表示されること (22ms)
    ✕ 全件検索を実行したとき、検索結果が0件以上となること (25ms)

  ● App.vue › 全件検索を実行したとき、検索結果が0件以上となること

    expect(received).not.toEqual(expected)

    Expected value to not equal:
      0
    Received:
      0

      27 |
      28 |     const resultList = wrapper.findAll('#result-list tr')
    > 29 |     expect(resultList.length).not.toEqual(0) //0 以上
```

```bash
    Expected value to not equal:
      0
    Received:
      0
```

検索結果が空（＝0以上を期待しているが、0が返っている）と怒られました。

理由は、APIコールは非同期処理のため、mockを `Promise` [https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Promise] で実装したためです。

-> 結果検証時に、APIの結果が返っていない（＝非同期処理が終了していない）していないためです。

この問題を解決するため、非同期処理を解決するためのライブラリ `flush-promises` を追加します。

`yarn add flush-promises --dev` コマンドを実行し、ライブラリを追加しましょう。

```bash
$ yarn add flush-promises --dev
yarn add v1.17.3
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
warning " > babel-core@7.0.0-bridge.0" has unmet peer dependency "@babel/core@^7.0.0-0".
[4/4] 🔨  Building fresh packages...
success Saved 1 new dependency.
info Direct dependencies
└─ flush-promises@1.0.2
info All dependencies
└─ flush-promises@1.0.2
✨  Done in 8.93s.
```

非同期処理をテスト結果検証前に終了させるようにテストコードを修正します。

* 変更前 (import)

```javascript
import { shallowMount } from '@vue/test-utils'
import App from '@/App.vue'
```

* 変更後 (import)

```javascript
import { shallowMount } from '@vue/test-utils'
import App from '@/App.vue'
import flushPromises from 'flush-promises'
```

* 変更前 (テストコード)

```javascript
  it('全件検索を実行したとき、検索結果が0件以上となること', () => {
    const wrapper = shallowMount(App)

    //検索ボタンクリック
    wrapper.find('#form-submit').trigger('click')

    const resultList = wrapper.findAll('#result-list tr')
    expect(resultList.length).not.toEqual(0) //0 以上
    console.log(resultList.at(2).text())
  })
```

* 変更後 (テストコード)

```javascript
  it('全件検索を実行したとき、検索結果が0件以上となること', async () => { //<---- async 追加
    const wrapper = shallowMount(App)

    //検索ボタンクリック
    wrapper.find('#form-submit').trigger('click')

    await flushPromises() //非同期処理を終了させる <---- 追加

    const resultList = wrapper.findAll('#result-list tr')
    expect(resultList.length).not.toEqual(0) //0 以上
    console.log(resultList.at(2).text())
  })
```

再度 `yarn test:unit` コマンドを実行し、テスト結果を確認してみます。

以下のとおりテストがパスしました！

また、検索結果`2`行目の情報もログで出力されているのが確認できるはずです。

```bash
$ yarn test:unit
yarn run v1.17.3
$ vue-cli-service test:unit
 PASS  tests/unit/app.spec.js
  App.vue
    ✓ タイトルが表示されること (21ms)
    ✓ 全件検索を実行したとき、検索結果が0件以上となること (23ms)

（....中略....）

  console.log tests/unit/app.spec.js:32
    2 2 robert de niro sample@sample.com 09012345678   <---　APIの結果の2行目がテーブルに展開されている

Test Suites: 1 passed, 1 total
Tests:       2 passed, 2 total
Snapshots:   0 total
Time:        1.088s
Ran all test suites.
✨  Done in 2.06s.
```

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/test_1.png "")


### スナップショット（＝HTMLダンプ）の付き合わせ

画面の内部ロジックを修正した場合、最終的に出力されるHTMLの結果が変更していないことを確認したいです。

スナップショット（＝HTMLダンプ）[https://jestjs.io/docs/ja/snapshot-testing] を取得し、結果が変化しないことを確認するテストケースを追加します。

以下のテストケースを追加してみましょう。

```javascript
  it("snapshot", () => {
    const wrapper = shallowMount(App)
    expect(wrapper.vm.$el).toMatchSnapshot()
  })
```

`yarn test:unit` コマンドを実行してみます。

```bash
$ yarn test:unit
yarn run v1.17.3
$ vue-cli-service test:unit
 PASS  tests/unit/app.spec.js
  App.vue
    ✓ タイトルが表示されること (22ms)
    ✓ 全件検索を実行したとき、検索結果が0件以上となること (23ms)
    ✓ snapshot (7ms)

（....中略....）

Snapshot Summary
 › 1 snapshot written from 1 test suite.

Test Suites: 1 passed, 1 total
Tests:       3 passed, 3 total
Snapshots:   1 written, 1 total <---- スナップショットが追加される
Time:        1.103s
Ran all test suites.
✨  Done in 2.01s.
```

全てのテストがパスし、以下のようなスナップショット（＝HTMLダンプ）が生成されたことが確認できるでしょう。

```
$ cat tests/unit/__snapshots__/app.spec.js.snap
// Jest Snapshot v1, https://goo.gl/fbAQLP

exports[`App.vue snapshot 1`] = `
<div
  id="app"
>
  <section
    class="hero"
  >
    <div
      class="hero-body"
    >
      <div
        class="container"
      >
        <h1
          class="title"
        >
          担当者一覧
        </h1>
      </div>
    </div>
  </section>

（....後略....）
```

アプリケーションのタイトルを以下のとおり修正し、

* 変更前（タイトル）

```html
    <section class="hero">
      <div class="hero-body">
        <div class="container">
          <h1 class="title">担当者一覧</h1>
        </div>
      </div>
    </section>
```

* 変更後（タイトル）

```html
    <section class="hero">
      <div class="hero-body">
        <div class="container">
          <h1 class="title">担当者一覧 変更</h1> <!-- <--- 修正 -->
        </div>
      </div>
    </section>
```

`yarn test:unit` コマンドを実行してみます。

```bash
$ yarn test:unit
yarn run v1.17.3
$ vue-cli-service test:unit
 FAIL  tests/unit/app.spec.js
  App.vue
    ✓ タイトルが表示されること (26ms)
    ✓ 全件検索を実行したとき、検索結果が0件以上となること (24ms)
    ✕ snapshot (12ms)

  ● App.vue › snapshot

    expect(value).toMatchSnapshot()

    Received value does not match stored snapshot "App.vue snapshot 1".

    - Snapshot
    + Received

    @@ -11,11 +11,11 @@
              class="container"
            >
              <h1
                class="title"
              >
    -           担当者一覧
    +           担当者一覧 変更
              </h1>
            </div>
          </div>
        </section>


      35 |   it("snapshot", () => {
      36 |     const wrapper = shallowMount(App)
    > 37 |     expect(wrapper.vm.$el).toMatchSnapshot()
         |                            ^
      38 |   })
      39 | })
```

出力されるHTMLの変更を検知し、テストが失敗することを確認できたでしょう。

CSSによるデザイン崩れは検知できませんが、内部ロジックの変更によるデグレ検知に役立つ機能なのでプロジェクト適応を検討してみてください。

#### スナップショット更新

仕様変更などで意図した変更の場合、以下コマンドで作成されたスナップショットを全て更新することができます。

```
$ ./node_modules/.bin/jest --updateSnapshot // <---- windows用に読み替え
```
