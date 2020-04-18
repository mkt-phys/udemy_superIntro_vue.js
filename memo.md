# memo

## 2020 年 4 月 5 日（日）

## セクション 2

### methods と computed の違い

- methods :画面が再描画されるたびに発動する
- computed :参照先が変わったときのみ起動される
  - computed の方がええ感じ

### watch

非同期処理で使う

### v-model

input と h1 が連動する。input の中身を開けると h1 も連動する

```
 <div id="app">
      <input type="text" v-model="message" />
      <h1>{{message}}</h1>
    </div>
```

## セクション 3

### v-if

```
  <div id="app">
      <p v-if="ok">OK!</p>
      <p v-else-if="maybeOk">maybe OK!</p>
      <p v-else>Not OK...</p>
    </div>

    <script type="text/javascript">
      new Vue({
        el: "#app",
        data: {
          ok: false,
          maybeOk: false
        }
      });
    </script>
```

<template>
</template>
でグループ化される

v-show="false"で display="none"がインライン形式で書かれる

- 頻繁に切り替えるときは v-show を使う。軽い。
- ほとんど切り替わらない場合は v-if を使う。重い。

## セクション 4

- 複数の vue インスタンスは作れる

```
var vm = new Vue({
data:{
    message:"hello"
}
})
```

として`vm.message="こんちは"`とかにしたら値にアクセスできる。逆に data の中にない変数はアクセスできない。

- Vue インスタンスを作成したら data:{}の中にある変数のゲッター、セッターができる

* console.log({vm})とかで\$data とかの中身が確認できる

* インスタンスの中からアクセス：`this`、
* インスタンスの外からアクセス：`var vm`の`vm.message`

* `vm.$mount("#app");`で`el`の部分を書き変えられる

#### ライフサイクル

-

#### コンポーネント

```
 Vue.component("hello", {
        template: "<p>こんにちは</p>"
      });
```

## セクション 5

### Vue CLI

- Vue CLI がいつまで経っても終わらへん現象がようやく解決できた。

https://qiita.com/saken649/items/ccabb2f34cdac784b383

```
$ vi ~/.vuerc
```

で

```
{
  "useTaobaoRegistry": false
}
```

とすればそっこーでいけた。
taobaoregistory は npm のミラーリポジトリらしい。中国のグレートファイアーウォールのための対策らしい。

## セクション 6(コンポーネント)

```
Vue.component("タグ名"){
data:function(){
return {
number:12
}
}
}
```

みたいに書く必要がある。data とかは object じゃなくて関数で書く必要がある。

```
template:
          "<p>いいね{{number}}<button @click='AudioParam'>+1</button></p>"
```

ダブルクォーテーションの中はシングルクオーテーションで書く！
これに 15 分悩むのはあほ！

\*.vue ファイルのなかは一つの要素しか無理

```
<template>
<p>aaa</p>
<p>bbb</p>
</template>
```

とかはむりで

```
<template>
<div>
<p>aaa</p>
<p>bbb</p>
</div>
</template>
```

とかなら OK

### .vue ファイルの local/global 登録

#### local 登録

適当な vue ファイルに次の漢字で書き込む
:App.vue

```
<script>
import likeHeader from "./likeHeader.vue";

export default {
  components: {
    // likeHeader: likeHeader
    likeHeader
  }
};
</script>
```

:likeHeader.vue

```
<template>
  <div>
    <p>いいね({{number}})</p>
    <button @click="number++">+1</button>
    <p>名前({{mori}})</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      number: 6
      //   mori: "makito"
    };
  }
};
</script>
```

#### global 登録

エントリーポイントの main.js に書く。

```
import likeNumber from "./likeNumber.vue";
Vue.component("likeNumber", likeNumber);
```

#### コンポーネントを使うときはパスカルかケバブ

- パスカル：LikeHeader
- ケバブ：like-header
- キャメル：likeHeader

#### コンポーネントの要素だけに CSS を当てたいとき

scoped を付ける

```
<style scoped>
div {
  border: 1px solid blue;
}
</style>
```

## セクション 7

```
  <likeNumber :number="number"></likeNumber>
  <likeNumber number="number"></likeNumber>
```

:number="number"
左辺：子コンポーネントの prop で定義されたもの
右辺：親コンポーネントで return されたもの

:は v-bind:と同じだがなぜこれを付ける必要があるのか
⇒ 動的に扱いたい場合は v-bind:を付ける
⇒⇒ 動的に扱いたい場合＝変化させたい場合？
:number="number"の

```
<likeNumber :number="number"></likeNumber>
<likeNumber number="number"></likeNumber>
<likeNumber :number="6"></likeNumber>
<likeNumber number="6"></likeNumber>
<likeNumber anumber="6"></likeNumber>
```

:をつけると正しく動く

```
<template>
  <div>
    <p>いいね({{abcd/2}})</p>
    <p>こっちもいいね({{mori/2}})</p>
    <button @click="increment">+1</button>
    <!-- <p>名前({{mori}})</p> -->
  </div>
</template>

<script>
export default {
  props: ["abcd", "mori"],
  methods: {
    increment() {
      // this.number += 1;
      this.abcd += 1;
      this.mori += 1;
    }
  }
};
```

### 91

```
props: {
    totalNumber: Number
  },
```

value に型を指定できる

object でも指定できる

```
  props: {
    totalNumber: {
      type: Number,
      required: true,
      default: 10
    }
  },
```

</script>

### 93

子コンポーネントから親への値の受け渡し
\$emit は親コンポーネントに指定してある js を実行する。

子が親に渡す書き方

```
$emit("＜イベント名＞","＜親に渡したい値＞")

```

親が子から受け取る書き方

```
<LikeNumber @＜イベント名＞="jsの処理（＜親に渡したい値＞が使える）"></LikeNumber>
```

### 94

親から子への値の受け渡しは楽
子から親は受け渡し辛い
⇒ 子は親に値を渡すだけ。渡された値は親で処理

## セクション 8

### 99

### 100

slot を使って html タグごと送れる
：親コンポーネント

```
    <likeHeader headerText="トータルああのいいね数!!
    ">
      <h1>トータルのいいね数</h1>
      <h2>{{number}}</h2>
      <p>{{headerText}}</p>
    </likeHeader>
```

：子コンポーネント

```
<slot></slot>
```

slot が likeHeader に置き換わる

### 102

子で
`<slot>デフォルトのタイトル</slot>`
としたときに
親で
`<likeHeader></likeHeader>`
とすればデフォルトのタイトルというのが表示される

### 103 名前付き slot

親：

```
<template v-slot:title>
        <h2>こんにちは</h2>
      </template>
```

子：name 属性で v-slot：～～～の～～～を指定する

```
<slot name="title"></slot>
```

### 104

```
<div>
<template v-slot:hogehoge></template>
<h2>sss</h2>
</div>
```

と書くと vue は

```
<div>
<template v-slot:hogehoge></template>
<template v-slot:default>
<h2>sss</h2>
</template>
</div>
```

と解釈される。受け取る側の vue ファイルは

```
<slot></slot>
```

として受け取れる。これは次の書き方と全く同じ意味を持つ

```
<slot name="default"></slot>
```

### 動的なスロット名

### 110

```
<template v-slot:[getTitle]="aaa">
<p>{{aaa}}</p>
</template>
```

は

```
<template #[getTitle]="aaa">
<p>{{aaa}}</p>
</template>
```

として v-slot:を#に置き換えられる

### 112

###

## セクション 9

###

## セクション 10

###
