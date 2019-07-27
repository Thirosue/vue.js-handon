## HTMLモック適応

HTMLを取得し、アプリケーションに反映する

### モックを反映

以下を、`Chrome` で開き、右クリックで 「ページのソースを表示」 する

https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/list.html

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/mock_1.png "")

以下部分を `src/App.vue` の

```html
      <section class="hero">
        <div class="hero-body">
          <div class="container">
            <h1 class="title"> 担当者一覧 </h1>
          </div>
        </div>
      </section>
      <div class="container is-fullhd">
        <div class="notification">
          <div class="field sample-vue-app-input-firstName"> <label for="firstName" class="label">名前</label>
            <div class="control"> <input type="text" id="firstName" name="firstName" class="input"> </div>
          </div>
          <div class="field sample-vue-app-input-lastName"> <label for="lastName" class="label">苗字</label>
            <div class="control"> <input type="text" id="lastName" name="lastName" class="input"> </div>
          </div>
          <div class="field is-grouped is-grouped-right"> <button id="form-submit" type="submit" class="button is-link">Search</button> </div>
        </div>
        <hr>
        <section class="hero">
          <table class="table is-bordered is-striped is-narrow is-hoverable is-fullwidth">
            <thead>
              <tr>
                <th>#</th>
                <th>ID</th>
                <th>名前</th>
                <th>苗字</th>
                <th>Email</th>
                <th>Tel</th>
              </tr>
            </thead>
            <tbody>
              <tr>
                <td>1</td>
                <td><a href="#" class="">1</a></td>
                <td>john</td>
                <td>doe</td>
                <td>test@rexev.com</td>
                <td>09011112222</td>
              </tr>
              <tr>
                <td>2</td>
                <td><a href="#" class="">2</a></td>
                <td>john</td>
                <td>doe</td>
                <td>test@rexev.com</td>
                <td>09011112222</td>
              </tr>
              <tr>
                <td>3</td>
                <td><a href="#" class="">3</a></td>
                <td>john</td>
                <td>doe</td>
                <td>test@rexev.com</td>
                <td>09011112222</td>
              </tr>
            </tbody>
          </table>
        </section>
      </div>
```

`<div id="app">` の中のコンテンツと差し替える

* 変更前

```html
2  <div id="app">
3    <img alt="Vue logo" src="./assets/logo.png">  <---- 削除
4    <HelloWorld msg="Welcome to Sample App"/>  <---- 削除
5  </div>
```

* 変更後

```html
<template>
  <div id="app">
    <section class="hero">
      <div class="hero-body">
        <div class="container">
          <h1 class="title">担当者一覧</h1>
        </div>
      </div>
    </section>
    <div class="container is-fullhd">
      <div class="notification">
        <div class="field sample-vue-app-input-firstName">
          <label for="firstName" class="label">名前</label>
          <div class="control">
            <input type="text" id="firstName" name="firstName" class="input" />
          </div>
        </div>
        <div class="field sample-vue-app-input-lastName">
          <label for="lastName" class="label">苗字</label>
          <div class="control">
            <input type="text" id="lastName" name="lastName" class="input" />
          </div>
        </div>
        <div class="field is-grouped is-grouped-right">
          <button id="form-submit" type="submit" class="button is-link">Search</button>
        </div>
      </div>
      <hr />
      <section class="hero">
        <table class="table is-bordered is-striped is-narrow is-hoverable is-fullwidth">
          <thead>
            <tr>
              <th>#</th>
              <th>ID</th>
              <th>名前</th>
              <th>苗字</th>
              <th>Email</th>
              <th>Tel</th>
            </tr>
          </thead>
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
        </table>
      </section>
    </div>ÏÏ
  </div>
</template>
```

http://localhost:8080/ に即座に反映され、

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/mock_2.png "")

のとおり、スタイルが適応されていない状態で表示される

### CSSの読み込み

`public/index.html` を編集し、`CDN` より `CSS Framework` を読み込む

* 編集前

```
1 <!DOCTYPE html>
2 <html lang="en">
3   <head>
4     <meta charset="utf-8">
5     <meta http-equiv="X-UA-Compatible" content="IE=edge">
6     <meta name="viewport" content="width=device-width,initial-scale=1.0">
7     <link rel="icon" href="<%= BASE_URL %>favicon.ico">
8　   <title>sample-app</title>
9   </head>
10  <body>
11    <noscript>
12      <strong>We're sorry but sample-app doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
13    </noscript>
14    <div id="app"></div>
15    <!-- built files will be auto injected -->
16  </body>
17 </html>
```

*　編集後

`8` 行目に以下を追記する

```html
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bulma/0.7.5/css/bulma.min.css">
    <script defer src="https://use.fontawesome.com/releases/v5.3.1/js/all.js"></script>
```

変更後の全量は以下

```html
1 <!DOCTYPE html>
2 <html lang="en">
3   <head>
4     <meta charset="utf-8">
5     <meta http-equiv="X-UA-Compatible" content="IE=edge">
6     <meta name="viewport" content="width=device-width,initial-scale=1.0">
7     <link rel="icon" href="<%= BASE_URL %>favicon.ico">
8     <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bulma/0.7.5/css/bulma.min.css">  <------- 追加
9     <script defer src="https://use.fontawesome.com/releases/v5.3.1/js/all.js"></script>                  <------- 追加
10　  <title>sample-app</title>
11  </head>
12  <body>
13    <noscript>
14      <strong>We're sorry but sample-app doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
15    </noscript>
16    <div id="app"></div>
17    <!-- built files will be auto injected -->
18  </body>
19 </html>
```

http://localhost:8080/ にスタイルが適応される

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/mock_3.png "")

ラベルなどが `center` 寄せにになっているため、`src/App.vue` の `<style>` セレクションの `text-align: center;` を削除し、`center` 寄せを解消する

* 変更前

```css
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center; <--- 削除
  color: #2c3e50;
  margin-top: 60px;
```

* 変更後

```css
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
```