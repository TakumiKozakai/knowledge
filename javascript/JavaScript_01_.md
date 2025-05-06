# JavaScript

## モジュールバンドラー・トランスパイラについて

### モジュールバンドラーとは

JSやCSS等のモジュールファイルたちをまとめてくれるツール。まとめることにより転送効率が向上し、処理のパフォーマンスが良くなる。  
2020年時点で主流のモジュールバンドラーは、**webpack**。
Reactで用いるCreate React Appでも、webpackを採用している。

### トランスパイラとは

JavaScriptの記法（例えば、Reactで用いられるJSXなどの拡張構文やTypeScriptなど）をピュアなJavaScriptに変換する（＝ブラウザで実行できる形にする）ツール。  
2020年時点で主流のトランスパイラは、Babel。

### ミニフィケーション

コメントや空白を除去したり、ローカル変数の名前を短縮化するなどして、コードそのもののサイズを最小化すること。  
バンドルが転送効率を改善するのに対して、ミニフィケーションはファイルサイズそのものを小さくすることで通信量を節約する。

### ダイジェスト付与

最終的に生成されるファイル名の末尾に、main.d0f9839a.jsのようにハッシュ値（ダイジェスト）を付与する。  
ハッシュ値によって、コードが変更された場合にはファイル名も変化するため、ブラザーの意図しないキャッシュを防げる  
（コードを変更したのに反映されない！が無くなる）。

<br>

## ES2015の書き方

### モダンJavaScript

- let、constを用いた変数宣言
- アローファンクション
- Class構文
- 分割代入
- テンプレート文字列（`${}`を使った書き方）
- スプレッド構文
- Promise

### var, let, const

- var・・・上書きや再宣言が可能
- let・・・上書きは可能、再宣言は不可
- const・・・上書きも再宣言も不可（ただし、オブジェクト型（オブジェクト・配列・関数等）は例外）

### テンプレート文字列

```javascript
const name = "Kozakai";
const age = 28;
const message = `私の名前は${name}です。
年齢は${age}歳です。`; // 改行も可

console.log(message); // 私の名前はKozakaiです。年齢は28です。
```

### 数値セパレート

アンダースコアで桁区切りする記法。可読性を高めるために用いる。

```
const value = 123_456_789;
```

### アロー関数

```javascript
// 従来の書き方1
function func(value) {
    return value;
}

// 従来の書き方2
const func = function(value) {
    return value;
};

// アロー関数
const func = value => {
    return value;
};

// 単一行処理ならreturnと{}を省略
const func = (value1, value2) => value1 + value2;

// 複数行でも単一行として扱う
const func = (value1, value2) => (
    {
        name: value1,
        age: value2,
    }
);
```

### 分割代入{}[]

・配列の分割代入

```javascript
const list = [10, 20, 30];

const [x, y, z] = list;
console.log(x, y, z); // 10 20 30

const [x, y, z, a] = list;
console.log(x, y, z, a); // 10 20 30 undefined

const [x, , z] = list;
console.log(x, z); // 10 30

const [one, ...rest] = list;
console.log(one, rest); // 10 [20, 30]
```

・オブジェクトの分割代入

```javascript
const myProfile = {
    name: "Kozakai",
    age: 28,
    sex: 'male',
};

const {name, age, memo = '---' } = myProfile;
// const {name:newName ,age:newAge} = myProfile; // プロパティに別名を付けたい場合は:で定義
console.log(`名前は${name}。年齢は${age}歳。${memo}`); // 名前はKozakai。年齢は28歳。---
```

配列と異なり、オブジェクトの場合は名前で割り当て先を決めるため、並びはプロパティの定義順と異なっていても構わない。  
割り当てないプロパティがあっても問題ない。  
また、目的のプロパティが存在しなかった場合に備えて、「変数名＝既定値」の形式で定義することも可能。

・入れ子のオブジェクトを分解する

```javascript
const member = {
    name: 'Kozakai',
    address: {
        prefecture: '静岡県',
        city: '藤枝市'
    }
};

const { address, address: {city}} = member;
console.log(address); // {prefecture: '静岡県', city: '藤枝市'}
console.log(city); // 藤枝市
```

### デフォルト値

```javascript
const sayHello = (name = "ゲスト") => console.log(`こんにちは！${name}さん！`);

sayHello(); // こんにちは！ゲストさん！
sayHello("Kozakai"); // こんにちは！Kozakaiさん！

// 分割代入などでも、同様にできる
const myProfile = {
    age: 24,
}

const { name = "ゲスト" } = myProfile;
```

### スプレッド構文

...を付与することで、配列内要素を順番に展開してくれる。

```javascript
const array = [1, 2];
console.log(...array); // 1 2

const sum = (num1, num2) => console.log(num1 + num2);

// 普通に要素を渡す場合
sum(array[0], array[1]);

// スプレッド構文
sum(...array);
```

スプレッド構文で要素のコピー、結合

```javascript
const arr1 = [1, 2];
const arr2 = [3, 4];

// コピー
const arr3 = [...arr1]; // [1, 2]

// 結合
const arr4 = [...arr1, ...arr2]; // [1, 2, 3, 4]
```

**=（イコール）でのコピーは、参照値も引き継がれるためしてはいけない。  
スプレッド構文は、新しい配列を生成するため元の配列に影響しない。**

```javascript
// NGのやり方
const arr1 = [1, 2];
const arr2 = arr1; // NG
arr2[0] = 100;

console.log(arr1); // [100, 2] // ！元のオブジェクト内容も更新される
console.log(arr2); // [100, 2]

// OKのやり方（スプレッド構文）
const arr2 = [...arr1];
```

使用具体例

```javascript
const attrs = {
    href: 'https://wings.msn/to/',
    download: false,
    target: '_blank',
    rel: 'help'
};

root.render(
    <a {...attrs}>サポートページへ</a>
)
```

以下のように展開される。

```html
<a href="https://wings.msn/to/" target="_blank" rel="help">サポートページへ</a>
```

### 分割代入によるオブジェクト引数の分解

```javascript
function greet({ name, age }) {
    console.log(`こんにちは、私は${name}、${age}歳です。`);
}

const my = { name: 'Kozakai', sex: 'male', age: 18 };
greet(my); // こんにちは、私はKozakai、28歳です。
```

### オブジェクトの省略記法(ショートハンド)

オブジェクトのプロパティ名と設定する変数名が同一の場合、:を省略できる。

```javascript
const name = "Kozakai";
const age = 28;

const user = {
    name,
    age,
};

console.log(user); // {name: "Kozakai", age: 28}
```

### map, filter

map・・・配列を順番に処理して、処理した結果を配列として返す

```javascript
list.map((value, index, array) => {
    ・・・statements・・・
})
-----------------------------------
list        : 元の配列
value       : 要素値
index       : インデックス値
array       : 元の配列
statements  : 要素に対する処理（戻り値は加工後の値）
```

```javascript
/* map */
const names = ["Kozakai", "れもん"];
const copy = names.map(name => {
    return name;
});

console.log(copy); // ["Kozakai", "れもん"]

names.map(name => console.log(name));
// Kozakai
// れもん

/*indexも扱える*/
names.map((name, index) => console.log(`${index + 1}番目は、${name}です`));
// 1番目はKozakaiです
// 2番目はれもんです
```

filter・・・条件に合致するものだけ返す

```javascript
list.filter(predicate)
-----------------------------------
predicate  : フィルター条件
```

```javascript
/*filter*/
const nums = [1, 2, 3, 4, 5];
console.log(nums.filter(num => {
    return num % 2 === 1;
}));
// [1, 3, 5]
```

### sort

```javascript
list.sort((m, n) => statements)
-----------------------------------
list        : 元の配列
m, n        : 比較する要素
statements  : 比較ルール

m > n : 整数
m = n : ゼロ
m < n : 負数
```

### 論理演算子 && || の本当の意味

- &&：左側がtrueなら右側を返す
- ||：左側がfalseなら右側を返す

```javascript
console.log(true && "trueです"); // trueです
console.log(false || "falseです"); // falseです
```

```javascript
true && expression      → expression
false && expression     → false
true || expression      → true
false || expression     → expression
```

### 論理演算子を使用する際の注意点

以下のようなコードは期待通り動作しない。

```javascript
<p>{books.length && `${books.length}件のデータがあります。`}</p>
```

配列booksに一つでも要素がある場合にデータ件数を表示するコード。
配列が空のときのbooks.lengthの値（ゼロ）はfalsyなため、一見動作するように見えるが動作しない。  
**データ件数がゼロの場合に「0」（lengthプロパティの戻り値）が表示されてしまう。**
評価対象がブール値でなく数値の場合は{}式として値を出力をしてしまうため、以下のようにしなければならない。

```javascript
<p>{books.length > 0 && `${books.length}件のデータがあります。`}</p>
```

### Optional Chaining 演算子

プロパティに「?」を付与することで、空値（undefined / null）を判定して、空値の場合は無条件にundefinedを返却する。

```javascript
const str = null;
console.log(str?.substring(1)); // undefined
```

### Null 合体演算子

式 ?? 値 とすることで、式が null / undefined の場合には「値」を、さもなければ「式」を評価した値を返却する。  
既定値を伴う「式」を表現したい場合によく利用する演算子。

```javascript
let value = null;
console.log(value ?? '既定値'); // 既定値

value ??= '既定値';
console.log(value); // 既定値
```

## モジュール

アプリを機能単位に分割するための仕組み。

### モジュールの定義

JavaScriptのモジュールは、ひとつのファイルとしてまとめるのが基本。  
モジュールの外からアクセスするには、exportキーワードを付与する必要がある。  
下記の場合、APP_TITLE は App モジュール内でしか利用できない。

App.js

```javascript

const APP_TITLE = 'Reactアプリ';

export function getTriangle(base, height) {
    return base * height / 2;
}

export class Article {
    getAppTitle() {
        return APP_TITLE;
    }
}
```

### モジュールの利用

定義済みの App モジュールには、以下のように記述すればアクセスできる。

index.html

```html
<script type="module" src="module_app.js"></script>
```

module_app.js

```javascript
import { Article, getTriangle } from './App.js';

console.log(getTriangle(10, 5)); // 25

const a = new Article();
console.log(a.getAppTitle()); // Reactアプリ
```

### モジュールを利用したファイルをnodeコマンドから実行する方法

package.jsonに記述する。

```
{
    "type": "module"
}
```

### import / export 命令の様々な記法

モジュールのメンバーに as 句で別名を付与

```javascript
import { getTriangle as tri } from './App.js';
console.log(tri(10, 2));
```

モジュール全体をまとめてインポート

```javascript
import * as app from './App.js';
console.log(app.getTriangle(10, 2));
```

既定のエクスポートをインポート

モジュール1つにつき、ひとつだけ既定のエクスポートを設定可能。

Util.js

```javascript
export default class Util {
    static getCircleArea(radius) {
        return (radius ** 2) * Math.PI;
    }
}
```

module_use_util.js

```javascript
import Util from './Util.js';

console.log(Util.getCircleArea(10)); // 314.1592・・・
```

### 宣言とエクスポートを分離

エクスポートされたメンバーがモジュールの最後にまとまるため、モジュールが提供する機能を一望しやすくなる。

```javascript
function getTriangle(base, height) {・・・}
class Article {・・・}

export (getTriangle, Article );
```

### 動的インポート

ここまで扱ってきたインポートは、「静的インポート」と呼ばれる。  
静的インポートが多いと、アプリの初期表示が遅延する。  
条件に応じて、初期表示に不要だったり利用しなかったりするモジュールは、「動的インポート」を用いたがほうが良い。

module_dynamic.js

```javascript
import('./App.js').then(app => {
    console.log(app.getTriangle(10, 5));

    const a = new app.Article();
    console.log(a.getAppTitle());
});
```
