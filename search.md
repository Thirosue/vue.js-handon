## 一覧検索の実装

以下順序で一覧検索を実装していきます。

1. イベントの設定
2. 全件検索の実装
3. 検索条件の指定
  
### イベントの設定

検索ボタンにイベントを設定していきます。

#### 不要なロジックの整理

その前に `src/App.vue` から、不要な記載を削除しておきます。

`<script>` セクションの以下を削除します。

* 変更前

```javascript
import HelloWorld from "./components/HelloWorld.vue"; //<---削除

export default {
  name: "app",
  components: { //<---削除
    HelloWorld //<---削除
  } //<---削除
};
```

* 変更後

```javascript
export default {
  name: "app",
};
```

+ 作業断面

対象ブランチ: `search/1`

https://github.com/Thirosue/vue.js-handon-app/tree/search/1

#### イベントの設定

24行目あたりのボタンにイベントを設定します。
クリックイベントは `@click` で設定できます。

* 変更前

```html
24        <div class="field is-grouped is-grouped-right">
25          <button id="form-submit" type="submit" class="button is-link">Search</button>  <---変更する
26        </div>
```

* 変更後

`@click="search"` を追記する

```html
24        <div class="field is-grouped is-grouped-right">
25          <button @click="search" id="form-submit" type="submit" class="button is-link">Search</button>
26        </div>
```

ボタンにクリックイベントを追加したので、`<script>` エリアに処理を追加していきます。

*　変更前

```javascript
export default {
  name: "app",
};
```

*　変更後

```javascript
export default {
  name: "app",
  methods: {
    search() {
      window.alert('search');
    }
  }
};
```

検索ボタンを押し、`alert` が表示されるのを確認してください。

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/search_1.png "")

+ 作業断面

対象ブランチ: `search/2`

https://github.com/Thirosue/vue.js-handon-app/tree/search/2

#### 全件検索の実装

`featch`[https://developer.mozilla.org/ja/docs/Web/API/Fetch_API/Using_Fetch] 関数を利用して、全件検索を実装します。

利用するAPIは以下です。

http://localhost:18081/admin/api/staff

検索メソッドを以下の通り修正します。

* 変更前

```javascript
    search() {
      window.alert('search');
    }
```

* 変更後

```javascript
    search() {
      fetch('http://localhost:18081/admin/api/staff')
        .then(response => response.json())
        .then(myJson => {
          console.log(JSON.stringify(myJson));
        });
    }
```

`Chrome` の デベロッパーツールで `Console` タブを表示し、検索ボタンを押してみます。

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/search_2.png "")

`Console` に `json` が表示されていることを確認し、次に進みます。

+ 作業断面

対象ブランチ: `search/3`

https://github.com/Thirosue/vue.js-handon-app/tree/search/3

##### 結果を格納する変数を追加

`<script>` エリアに `data` セクションを追加し、検索結果を格納する変数を追加します。

* 変更前

```javascript
<script>
export default {
  name: "app", //<----この下に追加する
  methods: { 
（....中略....）
</script>
```

* 変更後

`results` を配列で追加します。

```javascript
<script>
export default {
  name: "app",
  data() {
    return {
      results: [],
    }
  },
  methods: {
（....中略....）
</script>
```

##### 用意した変数に検索結果を格納する

`results` に APIの返戻を格納します。

* 変更前

```javascript
      fetch("http://localhost:18081/admin/api/staff")
        .then(response => response.json())
        .then(myJson => {
          console.log(JSON.stringify(myJson)); //<--- 変更
        });
```

* 変更後

http://localhost:18081/admin/api/staff

を確認すると、検索結果リストは `data` に格納されているので、以下の通り記載します。

```javascript
      fetch("http://localhost:18081/admin/api/staff")
        .then(response => response.json())
        .then(myJson => {
          this.results = myJson.data;
          console.log(this.results);
        });
```

検索を再度クリックしてみると、`results` に正常に格納されているのが確認できます。

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/search_3.png "")

+ 作業断面

対象ブランチ: `search/4`

https://github.com/Thirosue/vue.js-handon-app/tree/search/4

##### 格納した結果をHTMLに展開する

`results` に格納できたので、格納した結果を HTML に反映していきます。

まず、 `results` をHTMLにそのまま表示してみましょう。

`<template>` セクションの 74行目 あたりに差し込んでみます。

* 変更前

```html
（....前略....）
      </section>
    </div>
  </div>
</template>
```

* 変更後

`{{` `}}` で囲んで記載する必要があります。

```html
（....前略....）
      </section>
      {{results}}
    </div>
  </div>
</template>
```

検索を再度クリックしてみると、`results` がHTMLに反映されました。

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/search_4.png "")

つぎはテーブルに反映してみましょう。

* 変更前

```html
          <tbody>
            <tr>
              <td>1</td>
              <td>
                <a href="#" class>1</a>
              </td>
              <td>john</td>
              <td>doe</td>
              <td>test@rexev.com</td>
              <td>09011112222</td>
            </tr>
            <tr>
              <td>2</td>
              <td>
                <a href="#" class>2</a>
              </td>
              <td>john</td>
              <td>doe</td>
              <td>test@rexev.com</td>
              <td>09011112222</td>
            </tr>
            <tr>
              <td>3</td>
              <td>
                <a href="#" class>3</a>
              </td>
              <td>john</td>
              <td>doe</td>
              <td>test@rexev.com</td>
              <td>09011112222</td>
            </tr>
          </tbody>
```

* 変更後

変更前はベタ書きで3行記載していましたが、`v-for` で繰り返し表示し、各項目を結果にあてはめてみます。

プロパティの各名称は、APIの戻り [http://localhost:18081/admin/api/staff] を確認して、記載します。

`index` は検索のインデックスで `0` から開始するため、`+1` しておきます。

```html
          <tbody>
            <tr v-for="(staff, index) in results" :key="staff.id">
              <td>{{index+1}}</td>
              <td>
                <a href="#" class>{{staff.id}}</a>
              </td>
              <td>{{staff.firstName}}</td>
              <td>{{staff.lastName}}</td>
              <td>{{staff.email}}</td>
              <td>{{staff.tel}}</td>
            </tr>
          </tbody>
```

検索を再度クリックしてみると、`results` がテーブルに反映されました。

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/search_5.png "")

+ 作業断面

対象ブランチ: `search/5`

https://github.com/Thirosue/vue.js-handon-app/tree/search/5

##### 検索前にテーブルを隠しておく

現在のままだと、検索前にテーブルが表示されてしまっているので、検索前はテーブルヘッダを隠しておきます。

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/search_6.png "")

* 変更前

29行目あたりを編集し、検索結果が空の場合は、表示しないようにします。

```html
      <section class="hero">
        <table class="table is-bordered is-striped is-narrow is-hoverable is-fullwidth">
```

* 変更後

`v-if` で条件に応じて、要素の 表示 / 非表示 を切り替えできます。

`v-if="0 < results.length"` を追加し、`results` が空の場合、テーブルを表示しないようにします。

```html
      <section class="hero">
        <table v-if="0 < results.length" class="table is-bordered is-striped is-narrow is-hoverable is-fullwidth">
```

検索前にテーブルが隠れるようになりました。

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/search_7.png "")

+ 作業断面

対象ブランチ: `search/6`

https://github.com/Thirosue/vue.js-handon-app/tree/search/6

#### 検索条件の指定

つぎに検索条件を指定して、一覧を絞り込めるようにします。

##### 検索条件を変数に追加する

`results` と同様に、検索条件の `firstName` を `data` セクションに追加します。

* 変更前

```javascript
  data() {
    return {
      results: [],
    }
  },
```

* 変更後

```javascript
  data() {
    return {
      firstName: '', //<-------- add
      results: [],
    }
  },
```

##### 検索条件と同期する

検索条件の「名前」に変数を同期させます。

* 変更前

```html
        <div class="field sample-vue-app-input-firstName">
          <label for="firstName" class="label">名前</label>
          <div class="control">
            <input type="text" id="firstName" name="firstName" class="input" />
          </div>
        </div>
```

* 変更後

`v-model` を指定すると、入力データと `javascript` の変数がリアルタイムに同期します。

`v-model="firstName"` を `input` 要素に追加してみましょう。

```html
        <div class="field sample-vue-app-input-firstName">
          <label for="firstName" class="label">名前</label>
          <div class="control">
            <input v-model="firstName" type="text" id="firstName" name="firstName" class="input" />
          </div>
        </div>
```

`search` メソッドに `firstName` のデバッグ表示を追加します。

```javascript
    search() {
      console.log(this.firstName);
      fetch("http://localhost:18081/admin/api/staff")
        .then(response => response.json())
        .then(myJson => {
          this.results = myJson.data;
          console.log(this.results);
        });
    }
```

テキストエリアに文字を入力し、検索を再度クリックしてみると、`firstName` に入力データが同期しているのが確認できます。

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/search_8.png "")

入力した条件をAPIに追加して、検索結果を絞ってみましょう。

http://localhost:18081/admin/api/staff?firstName=jo

上記のとおり、クエリストリングを追加 (`firstName=jo`) すると検索条件を絞り込むことができます。

* 変更前

```javascript
    search() {
      console.log(this.firstName);
      fetch("http://localhost:18081/admin/api/staff")
        .then(response => response.json())
        .then(myJson => {
          this.results = myJson.data;
          console.log(this.results);
        });
    }
```

* 変更後

```javascript
    search() {
      console.log(this.firstName);
      fetch("http://localhost:18081/admin/api/staff?firstName=" + this.firstName) //<----変更
        .then(response => response.json())
        .then(myJson => {
          this.results = myJson.data;
          console.log(this.results);
        });
    }
```

テキストエリアに文字を入力し、検索を再度クリックしてみると、検索結果が絞り込めました。

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/search_9.png "")

+ 作業断面

対象ブランチ: `search/7`

https://github.com/Thirosue/vue.js-handon-app/tree/search/7

##### Enter Keyで検索できるようにする

インプットフィールドを入力後、`Enter` キーで検索したいので、機能を追加します。

インプットフィールドにキーイベントを追加します。

* 変更前

```html
          <div class="control">
            <input v-model="firstName" type="text" id="firstName" name="firstName" class="input" /> <!-- //<---- 変更 -->
          </div>
```

* 変更後

`@keydown.enter` でイベントを追加します。

```html
          <div class="control">
            <input @keydown.enter="search" v-model="firstName" type="text" id="firstName" name="firstName" class="input" />
          </div>
```

検索ワードを追加して、`Enter` キーを入力してみましょう。

先ほどと同様に検索できるはずです。

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/search_9.png "")

+ 作業断面

対象ブランチ: `search/8`

https://github.com/Thirosue/vue.js-handon-app/tree/search/8

##### アニメーションさせる

`Vue.js` では強力なアニメーションを簡単に実現できます。

検索結果をシャッフルさせて、アニメーションさせてみましょう。

まず、シャッフルさせる関数を提供しているユーティリティライブラリの `lodash` をインストールします。

以下のコマンドを入力し、ライブラリを追加しましょう。

```bash
$ yarn add lodash
```

以下のとおりライブラリが追加され、

```bash
$ yarn add lodash
yarn add v1.17.3
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
warning Pattern ["lodash@^4.17.15"] is trying to unpack in the same destination "/Users/hirosue/Library/Caches/Yarn/v4/npm-lodash-4.17.15-b447f6670a0455bbfeedd11392eff330ea097548/node_modules/lodash" as pattern ["lodash@^4.17.13","lodash@^4.17.13","lodash@^4.17.13","lodash@^4.17.13","lodash@^4.17.13","lodash@^4.17.11","lodash@^4.17.13","lodash@^4.17.13","lodash@^4.17.13","lodash@^4.17.4","lodash@^4.3.0","lodash@^4.17.4","lodash@^4.17.4","lodash@^4.17.4","lodash@^4.17.4","lodash@^4.17.15","lodash@^4.17.4","lodash@^4.17.4","lodash@^4.17.14","lodash@^4.17.4","lodash@^4.17.4","lodash@^4.17.11","lodash@^4.17.3","lodash@^4.17.15","lodash@^4.17.5","lodash@^4.17.11","lodash@^4.17.4","lodash@^4.17.11","lodash@^4.17.12","lodash@^4.17.14","lodash@^4.17.11","lodash@^4.17.11","lodash@^4.17.11"]. This could result in non-deterministic behavior, skipping.
[3/4] 🔗  Linking dependencies...
warning " > babel-core@7.0.0-bridge.0" has unmet peer dependency "@babel/core@^7.0.0-0".
[4/4] 🔨  Building fresh packages...
success Saved 0 new dependencies.
✨  Done in 3.27s.
```

`package.json` の `dependencies` にライブラリが追加されたのが確認できます。

```json
  "dependencies": {
    "core-js": "^2.6.5",
    "lodash": "^4.17.15", <--- 追加
    "vue": "^2.6.10"
  },
```

つぎにシャッフルボタンを検索ボタンの横に追加します。

* 変更前

```html
        <div class="field is-grouped is-grouped-right">
          <button @click="search" id="form-submit" type="submit" class="button is-link">Search</button>
        </div>
```

* 変更後

```html
        <div class="field is-grouped is-grouped-right">
          <p class="control">
            <button @click="search" id="form-submit" type="submit" class="button is-link">Search</button>
          </p>
          <p class="control">
            <button @click="shuffle" type="submit" class="button is-link">Shuffle</button>
          </p>
        </div>
```

つづいて `shuffle` メソッドを追加します。

まず、追加したユーティリティの `lodash` を `<script>` タグ直下に追加 (`import`) します。

* 変更前

```html
<script>
export default {
（....後略....）
```

* 変更後

```html
<script>
import _ from 'lodash';

export default {
（....後略....）
```

さらに `shuffle` を実装しましょう。

* 変更前

```javascript
  methods: {
    search() {
（....後略....）
```

* 変更後

```javascript
  methods: {
    shuffle() {
      this.results = _.shuffle(this.results);
    },
    search() {
（....後略....）
```

検索後に `Shuffle` ボタンを押してみましょう。

ボタン押すごとに検索結果がシャッフルされることが確認できます。

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/search_10.png "")

+ 作業断面

対象ブランチ: `search/9`

https://github.com/Thirosue/vue.js-handon-app/tree/search/9

##### アニメーションさせる 2

最後に `html` と `css` を修正して、アニメーションさせます。

`<tbody>` タグを以下のとおり修正してください。

* 変更前

```html
          <tbody>
```

* 変更後

```html
          <tbody is="transition-group">
```

また `<style>` エリアに アニメーション用の設定を追加します。

* 変更前

```css
<style>
#app {
```

* 変更後

```css
<style>
.v-move {
  transition: transform 1s;
}
#app {
```

修正後に `Shuffle` ボタンを押してみましょう。

ボタン押すごとに検索結果がアニメーションを伴いシャッフルされることが確認できます。

+ 作業断面

対象ブランチ: `search/10`

https://github.com/Thirosue/vue.js-handon-app/tree/search/10