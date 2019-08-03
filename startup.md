## アプリケーションの作成

vue-cliでアプリケーションを作成・起動します。

### vue cli でアプリケーションを作成する

> 以下コマンドでアプリケーションを作成する

```
$ vue create sample-app
```

> `Manually select features` を選択

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/startup_1_1.png "")

> `Unit Testing` を追加で選択する ※spaseキーで選択

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/startup_1_2.png "")

> 構文解析は `default` のまま

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/startup_1_3.png "")

> `Lint and fix on commit` を追加選択 ※自動的に定型的な構文エラーを修正してくれる

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/startup_1_4.png "")

> 単体テストツールは `Jest` を選択

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/startup_1_5.png "")

> 設定ファイルは `default` のまま

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/startup_1_6.png "")

> 今回作成したプロジェクト設定を保存するか?  `N` を選択

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/startup_1_7.png "")

> 以下のようにアプリケーションが作成される

```
（....中略....）

success Saved lockfile.
✨  Done in 7.59s.
⚓  Running completion hooks...

📄  Generating README.md...

🎉  Successfully created project sample-app.
👉  Get started with the following commands:

 $ cd sample-app
 $ yarn serve
```

### アプリケーションを起動する

> 作成時の最後のメッセージに従い、コマンドを実行

```
$ cd sample-app
$ yarn serve
```

> 以下のとおりアプリケーションが起動する

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/startup_2.png "")

http://localhost:8080/ へブラウザで接続すると、

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/startup_3.png "")

サンプルアプリケーションが表示される

+ 作業断面

対象ブランチ: `startup/1` 

https://github.com/Thirosue/vue.js-handon-app/tree/startup/1

### サンプルアプリの編集

`src/App.vue` を開いて、以下メッセージを編集

* 編集前

```
1 <template>
2   <div id="app">
3     <img alt="Vue logo" src="./assets/logo.png">
4     <HelloWorld msg="Welcome to Your Vue.js App"/>   <--- 4行目を編集
5   </div>
6 </template>
```

* 変更後

```
1 <template>
2   <div id="app">
3     <img alt="Vue logo" src="./assets/logo.png">
4     <HelloWorld msg="Welcome to Sample App"/>   <--- Sample App に変更
5   </div>
6 </template>
```

編集して、保存すると、画面に即座に反映される

![](https://s3-ap-northeast-1.amazonaws.com/vue.js-handon-2019/startup_4.png "")

+ 作業断面

対象ブランチ: `startup/2` 

https://github.com/Thirosue/vue.js-handon-app/tree/startup/2

