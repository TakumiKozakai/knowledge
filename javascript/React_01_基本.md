# React_基本

## コンポーネント

### 　コンポーネントとは

ページを構成するUI部品のこと。テンプレート（見た目）とそれに付随するロジックから構成される。  
コンポーネントファイルは、拡張子を.jxsとする。

```javascript
ReactDOM.render(<App />, document.getElementById("root"));
```

第一引数の`<App />`のように、関数名をHTMLのようにタグで囲むことによって、コンポーネントとして扱える。

### 　関数コンポーネントとクラスコンポーネント

初期の頃はClassで定義するクラスコンポーネントが用いられてきたが、**近年は関数で定義する関数コンポーネントが主流となってきている。**  
React Hooks の登場で、関数コンポーネントでも内部状態やライフサイクルを扱えるようになり、  
クラスコンポーネントでしか表現できなかったコードが関数コンポーネントでも記述できるようになった。  
関数コンポーネント＋フックと比べた時、クラスコンポーネントには以下の問題点がある。

- コールバック関数でpropsやstateを参照するためには、事前にthisコンテキストのバインドが必要
- ライフサイクルを扱うためのメソッドが多くて複雑
- 状態をセットにして、振る舞いを他のコンポーネントと共通化するのが困難

コンポーネントは、大文字で定義する。

```tsx
// src/component/Message.tsx
const Text = (props: {content: string}) => {
    const {content} = props
    return <p className="text">{content}</p>
}

const Message = (props : {}) => {
    const content1 = 'This is parent component'
    const content2 = 'Message uses Text component'

    return (
        <div>
            <Text content={content1} />
            <Text content={content2} />
        </div>
    )
}

export default Message
```

## Strict モード

`<React.StrictMode>`要素で`<App>`を括っているのは、ReactアプリをStrictモード（厳密モード）で実行するという意味。  
Strictモードでは、アプリに「べからず」なコード（下記例）が記述されている場合、警告を発してくれる。

- 安全でないライフサイクルの利用
- レガシーな ref API、context API の利用
- 非推奨な findDOMNode メソッドの利用

Strictモードは、開発モードでのみ作動する。本番ビルドには影響を与えないため、Reactアプリの最上位で無条件に有効にしておいて良い。  
※npm start で実行した場合、開発モードで実行となる。npm run build でビルドされたアプリは、本番モードとなる。

## JSX 記法

JavaScript/TypeScript内でHTMLタグを直接書き込める機能。  
HTMLタグを扱うファイルは、以下の拡張子を付ける。  
JavaScriptの場合は、.jsxでJSXと呼び、TypeScriptの場合は、.tsxでTSXと呼ぶ。

## JSX記法のルール

### return以降は、1つのタグで囲われている必要がある

```javascript
const App = () => {
    return (
        <div>
            <h1></h1>
            <p></p>
        </div>
    )
}

// もしくは、Fragmentで囲むか、空タグで囲む
const App = () => {
    return (
        <>
            <h1></h1>
            <p></p>
        </>
    )
}
```

## JSXの中でのイベントやスタイルの扱い方

### 属性やイベントはキャメルケースで記述する

```javascript
// CSSの文字サイズ指定
× font-size
○ fontSize

// ボタン押下時イベント
× onclick
○ onClick
```

### JavaScriptは{}で囲むことで記述できる

```javascript
export const App = () => {

    const onClickButton = () => {
        alert();
    };

    return (
        <>
            {console.log("test")}
            <h1>こんにちは</h1>
            <button onClick={onClickButton}>ボタン</button>
        </>
    );
};
```

### CSSを書くときは、style = {{css}} とする

{}でjs記法として、cssプロパティを持つ{}オブジェクトを与える書き方。

```javascript
export const App = () => {

    // CSSオブジェクト
    const contentStyle = {
        color : "blue",
        fontSize: "20px"
    };

    return (
        <>
            <h1 style={{ color: "red" }}>こんにちは</h1>
            <p style={contentStyle}>こんにちは</p>
        </>
    );
};
```

### 属性値の規定値はtrue

属性の値部分が省略された場合、その値はtrueと見なされる。つまり、以下のコードはいずれも等価となる。

```javascript
<a href="index.html" download>トップへ</a>
<a href="index.html" download={true}>トップへ</a>
```

## export の種類

### 通常の export

```javascript
// export側
export const Component = () => {};

// import側
import { Component } from "./Component";
```

### デフォルト export  

import側の{}の記述が不要になり、また、任意の名前を付けてimportできる。  

```javascript
// export側
const Component = () => {};

export default Component;

// import側
import OtherNameComponent from "./Component";
```

※ただし、通常 export でも、as を使用すれば、別名を付けられる。

```javascript
// import側
import { Component as OtherNameComponent } from "./Component";
```

どちらを利用するかはプロジェクトに依るが default export のほうは、名称の打ち間違いに気付けなかったり意図しないものをimportしたりする可能性があるため、通常 export が推奨される。

## Props、Children

### Props

コンポーネントを使用する親から渡されるデータ。コンポーネントに渡す引数のようなもの。  
Propsを受け取ったコンポーネントは、Propsに応じたスタイルや内容を表示する。  
**親から子へデータを一方通行で渡す。propsの中身を子から書き換えることはできない。**  
コンポーネント中で表示内容を変更したい場合は、親からコールバック関数を渡してイベントやデータを通知する。

### Children

Propsタグに囲まれた文字列のこと。テキスト部分をPropsとして定義するのではなく、タグで囲んだ文字を使用するように記述できる。

```javascript
return (
    <>
        <ColoredMessage color="pink" message="Text">Children</ColoredMessage>
    </>
)

// Childrenを利用する側
export const ColoredMessage = (props) => {
    const contentStyle = {
        color: props.color,
        fontSize: "20px"
    };

    return (
        <>
            <p style={contentStyle}>{props.message}</p>;  // Text
            <p style={contentStyle}>{props.children}</p>; // Children
        </>
    );
}
```

### Propsの分割代入・省略記法

```javascript
export const ColoredMessage = (props) => {
    // Propsを分割代入
    const { color, children } = props;

    const contentStyle = {
        color, // props.が不要、color： color　となり、省略記法でcolorだけにできる
        fontSize: "20px"
    }

    // Props.が不要
    return <p style={contentStyle}>{children}</p>;
}
```

### Propsを展開するパターン

```javascript
export const ColoredMessage = ({ color, children }) => {
    ・・・
```

※上記のテクニックを採用するかどうかは、プロジェクト方針に依る。

## 再レンダリングの仕組みと最適化

### 再レンダリングが起こるタイミングと対象

- Stateが更新されたコンポーネント
- Propsが変更されたコンポーネント
- 再レンダリングされたコンポーネント配下のコンポーネント全て

### レンダリング最適化1 - memo -

Reactでは、メモ化（前回の処理結果を保持する機能）を利用することで、コンポーネント・変数・関数の再レンダリング制御ができる。  
メモ化されたコンポーネントは、親のコンポーネントが再レンダリングしても、自身はPropsの変更がない限り再レンダリングしなくなる。  

```javascript
// Componentをメモ化
import { useState, memo } from "react";

const Component = memo(() => {
    // omitted
});
```

### レンダリング最適化2 - useCallback -

親コンポーネントから子コンポーネントにPropsとして関数を渡した場合、子コンポーネントをメモ化していても再レンダリングが発生してしまう。  
関数は再レンダリングでコードが実行される度、常に新しい関数が再生成されるため、  
関数をPropsとして受け取っている子コンポーネントは、Propsが変化したと判定してしまうから。  
この事象を回避するためには、**関数のメモ化**をおこなう必要がある。

```javascript
// 関数をメモ化
// 第一引数 : 関数, 第二引数 : 依存配列
const onClickButton = useCallback(() => {
    alert('ボタンが押されました！');
}, []);
```

上記のように、依存配列が空の場合、最初に作成された関数が使い回されるようになる。  
依存配列には、関数内で扱っていて変更の可能性のある変数を設定すればOK。

### レンダリング最適化3 - useMemo -

基本的には、コンポーネントと関数をメモ化しておけば不要な再レンダリングは大方抑制できるが、メモ化は変数にも適用できる。  
利用シーンとしては、変数設定のロジックが複雑だったり、膨大な数のループが実行される場合など。メモ化により、変数設定時の負荷を下げることができる。  
また、依存配列に設定されている値を見ることで、その変数を設定するのに影響を与えている外部の値を明示的に示すことができるため可読性の向上にも繋がる。

```javascript
// 変数をメモ化
const sum = useMemo(() => {
    return 1 + 3;
}, []);
```

## グローバルStateの管理

コンポーネント内で作成・参照するStateをローカルStateと表現する。アプリケーション規模が大きくなると、ローカルStateだけでなく、コンポーネントをまたいで利用するグローバルStateが必要になる。  
例えば、ルートコンポーネントから最下層コンポーネントまで5階層以上あり、5階層でルートコンポーネントのStateを利用したい場合、上の階層からPropsをバケツリレーで渡すのは保守性の観点でイケていない。  
そのため、どのコンポーネントからでもアクセスできるグローバルStateを用いて制御する方針を取らなければいけない。

- バケツリレーがイケてない理由
  - 階層が深い分それだけコードが複雑になる
  - 本来必要のないPropsを受け取ることにより、他の場所で再利用できないコンポーネントになる
  - Propsが変更されたら再レンダリングされるため、不要な再レンダリングを生む

### ContextでのグローバルState管理

propsでは親から子のコンポーネントへ任意のデータを渡せる。Contextは、データを親から直接でなくともコンポーネントが必要なデータを参照できる。  
例えば、ログインしているユーザーの情報などアプリ内の様々なコンポーネントで参照する可能性があるものなどに利用する。

### Contextの基本的な使い方

ex.管理者権限を持つ場合に編集ボタンを活性化する

1. React.createContextで管理者権限用のContextを保持するためのProviderコンポーネントを作成する。

2. 作成したProviderでグローバルState（＝Context値）を扱いたいコンポーネントを囲む。  
Context値を参照するためには、ProviderでContext値を参照したいコンポーネント群を囲む必要がある  
（大抵の場合、ルートコンポーネント）。  
Providerコンポーネントは、何でも囲めるようにPropsとしてchildrenを受け取るようにすることがポイント。  

```javascript
/* AdminFlagProvider.jsx */

import { createContext } from "react";

// createContext関数でコンテキストの器を作成
export const AdminFlagContext = createContext({});

export const AdminFlagProvider = props => {
    const { children } = props;

    // グローバル管理対象（＝Context値）を定義
    const [isAdmin, setIsAdmin] = useState(false);

    return (
        // valueにグローバル管理対象（＝Context値）を設定
        <AdminFlagContext.Provider value={{ isAdmin, setIsAdmin }}>
                                        // ↑オブジェクトの省略記法
            // AdminFlagContextの中にProviderがあるため、childrenを囲む
            {children}
        </AdminFlagContext.Provider>
    );
};
```

```javascript
/* 作成したProviderを使ってContext値を参照したい範囲のコンポーネントを囲む。
   今回は例として、アプリ全体で参照する考えとしてAppコンポーネントを囲む。 */

/* index.js */

import ReactDOM from "react-dom";
import { App } from "./App";
import { AdminFlagProvider } from "./providers/AdminFlagProvider";

ReactDOM.render(
    <AdminFlagProvider>
        <App />
    </AdminFlagProvider>
    document.getElementById("root");
);
```

3. Context値を参照したいコンポーネント内でReact.useContextを使ってContext値を参照する。

```javascript
/* EditButton.jsx */

import { useContext } from "react";

// 作成したContextをimport
import { AdminFlagContext } from "./providers/AdminFlagProvider";

export const EditButton = () => {
    // Context値を参照
    const { isAdmin } = useContext(AdminFlagContext);

    return (
        <button disabled={!isAdmin}>
            編集
        </button>
    )
}
```

### Context使用時の再レンダリングと最適化について

1. useContextで参照しているContextの値が更新された時、そのContextを参照しているコンポーネントは全て再レンダリングされる。  
先コードの例で言うと、「あるコンポーネントでは、setIsAdmin関数のみ使っている」場合でも、isAdminが更新されたタイミングでこのコンポーネントも再レンダリングされる。  
そのため、1つのContextに属性の異なるStateを詰め込むのは避けなければならないし、場合によっては更新関数は別のContextに分けるといったやり方もある。  
また、Providerはネストできるため、以下のように複数のProviderでコンポーネントを囲むことができる。

```javascript
    return (
        <AdminFlagProvider>
            <OtherProvider>
                <App />
            </OtherProvider>
        </AdminFlagProvider>
    );
```

### その他のグローバルStateを扱う方法

- Redux  
2015年から存在している状態管理ライブラリ。大規模なプロジェクトに適していて、多くのプロジェクトで採用されている。  
Facebookの提唱するFluxアーキテクチャに則って設計されており、単一方向にしかデータが流れないのが特徴。  

- Recoil  
2020年に公開されたFacebook社が提供する状態管理ライブラリ。導入と実装のハードルが低く、文法もReact Hooksと同じuse〜の形で馴染みやすい。  

## カスタムフック

カスタムフックとは、任意の処理をまとめて自作のHooksを作成する実装のこと。  
カスタムフックを使うことで、ロジックをコンポーネントから分離させたり、複数コンポーネントでのロジックの再利用をすることができる。

- Reactに標準で搭載されているHooks
  - useState
  - useEffect
  - useCallback
  - useMemo
  - useContext
  - useRef
  - useReducer
  - useLayoutEffect
  - useImperaTiveHandle
  - useDebugValue

カスタムフックは、標準で提供されているものだけでなくプロジェクト内で必要に応じて自作していく。  
自作したHooksは、use〜と命名する。

## テクニック

### 即時関数

reactで普通のif文を用いる場合、直接JSX式に埋め込むことはできない。
直接JSX式に埋め込みたい場合は、即時関数を利用することで擬似的に実現できる。

```javascript
{(() => {
    条件文
})()}
```

ex.

```javascript
export default function ForItem({ book }) {
    return (
    <>
        <dt><a>link</a></dt>
        {(() => {
            if (download) {
                return <dd>{summary}<Download isbn={isbn} /></dd>
            } else {
                return <dd>{summary}</dd>
            }
        })()}
    </>
    );
}
```

## Reactの設計思想

### Single Source of Truth

「データを保管・管理する場所は一つ」。つまり、データを保持するコンポーネントは一つで、もし他のコンポーネンｔのがそのデータを使う場合は、データを保持しているコンポーネントから渡してもらう必要がある。  
それぞれのコンポーネントがデータを持つのではなく、一つのコンポーネントがデータを一元的に管理することで、アプリの規模が拡大しても管理が容易になる。  

### Top-down Data Flow

データを上位から下位のコンポーネントへと渡すコンセプト。

## バージョンアップによる変遷

### FCとVFC

FCはchildrenがpropsの中で暗黙的に定義され、VFCでは定義されない。  
React18からVFCは非推奨となり、FCからchildrenが削除。  
しかし、FCも利便性等の観点から近年使用されない傾向気味。  
そのため、近年は以下のようにpropsに型を明示するのが主流。  

```tsx
const Container = (props: ContainerProps) => { //...
```
