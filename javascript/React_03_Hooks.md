# React_Hooks

## React Hooks

状態やライフサイクルを扱う。クラスコンポーネントと同等な機能を持つ関数コンポーネント（これにより、クラスコンポーネントの利用場面が減り、関数コンポーネントが主流となる）。
コンポーネント内の状態とロジックをフックとして切り出すことで、コンポーネントのコードを綺麗に保つことができ、また、コードの再利用性も高められる。

## 標準搭載Hook

- 状態を扱うためのフック
  - useState
  - useReducer
- メモ化用のフック
  - useCallback
  - useMemo
- 副作用のためのフック
  - useEffect
  - useLayoutEffect
- Context用のフック
  - useContext
- ref用のフック
  - useRef
  - useImperaTiveHandle
- デバッグ用のフック
  - useDebugValue

## 状態を扱うためのフック - useState, useReducer -

### State

コンポーネントの状態を表す値。例えば、以下のような画面の状態を保持する。

- エラーがあるか
- モーダルウィンドウを開いているか
- ボタンを押せるか
- テキストボックスに何を入力したか

### useState

状態を扱うためのフック。「state」と「stateを更新する関数」を作る関数。  
更新関数を呼び出すと状態が変化して、フックがあるコンポーネントは再描画される。  
更新関数を呼ぶには、引数に値を渡す方法と関数を渡す方法の2種類があり、  
値を渡した場合はその値が次の状態となり、関数を渡した場合は関数の戻り値が次の状態になる。  
**また、関数の場合は、引数に現在の状態が設定されている。**

```javascript
// const [状態, 更新関数] = useState(初期状態)
const [state, setState] = useState(initialState);
```

※State値ではなくProps値を変更すれば良いのでは？と思うが、仕様上Props値は呼び出し元から任意のタイミングで変更される可能性があるため、呼び出されるコンポーネント側で変更してはならない。

ex1.カウンター

```tsx
const Counter = (props: CounterProps) => {
    const { initialValue } = props
    const [count, setCount] = useState(initialValue)

    return (
        <>
            <p>Count: {count}</p>
            {/* 引数に値を渡す */}
            <button onClick={() => setCount(count - 1)}>-</button>
            {/* 引数に関数を渡す */}
            {/* prevには更新直前の値が渡される */}
            <button onClick={() => setCount(prev => prev + 1)}>+</button>
        </>
    )
}
```

ex2.子コンポーネントから親コンポーネントへの情報伝達（引数に関数を渡す方法）

```javascript StateParent.js
import { useState } from 'react';
import StateCounter from './StateCounter';

export default function StateParent() {
    const [count, setCount] = useState(0);
    const update = step => setCount(prev => prev + step);

    return (
        <>
            <p>総カウント：{count}</p>
            <StateCounter step={1} onUpdate={update} />
            <StateCounter step={-1} onUpdate={update} />
        </>
    );
}
```

```javascript StateCounter.js
export default function StateCounter({ step, onUpdate }) {
    const handleClick = () => onUpdate(step);

    return (
        <button onClick={handleClick}>
            <span>{step}</span>
        </button>
    )
}
```

### useReducer

状態を扱うためのフック。複雑な状態遷移をシンプルに記述できる。  
配列やオブジェクトなどの複数のデータをまとめたものを状態として扱う場合に用いる。  
useStateでは更新関数に次の状態を直接渡していたが、useReducerでは更新関数（dispatch）にactionと呼ばれるデータを渡す。  
現在の状態とactionを渡すと次の状態を返すreducerという関数を用いる。

```ts
reducer(現在の状態, action) {
    return 次の状態
}

const [現在の状態, dispatch] = useReducer(reducer, reducerに渡される初期状態)
```

ex.カウントを2倍にする機能、カウントをリセットする機能

```ts
import { useReducer } from 'react'

type Action = 'INCREMENT' | 'DECREMENT' | 'DOUBLE' | 'RESET'

const reducer = (currentCount: number, action: Action) => {
    switch(action) {}
        case 'INCREMENT':
            return currentCount + 1
        case 'DECREMENT':
            return currentCount - 1
        case 'DOUBLE':
            return currentCount * 2
        case 'RESET':
            return 0
        default:
            return currentCount
    }
}

type CounterProps = {
    initialValue: number
}

const Counter = (props: CounterProps) => {
    const { initialValue } = props
    const [count, dispatch] = useReducer(reducer, initialValue)

    return (
        <>
            <p>Count: {count}</p>
            <button onClick={() => dispatch('DECREMENT')}>-</button>
            <button onClick={() => dispatch('INCREMENT')}>+</button>
            <button onClick={() => dispatch('DOUBLE')}>*2</button>
            <button onClick={() => dispatch('RESET')}>Reset</button>
        </>
    )
}

export defaullt Counter
```

上記のように、setState()と比べて状態の更新を呼び出す側は具体的な状態に依存しないためコードをシンプルに保てる。  
また、状態を更新するロジックをコンポーネント外の関数に切り出しているため、テストが容易になる。

## メモ化用フック - useCallback、useMemo -

メモ化用のフック。値や関数を保持し、必要のない子要素のレンダリングや計算を抑制するために使用する。

### メモ化とは

メモ化とは、ある関数の計算結果を保持し、同一の呼出があった場合は保持しておいた結果を返すといった計算結果を再利用する最適化手法のこと。

- Reactコンポーネントの再描画タイミング
  - propsや内部状態が更新された時
  - コンポーネント内で参照しているContextの値が更新された時
  - 親コンポーネントが再描画された時

親コンポーネントが再描画されると無条件に子コンポーネントが描画される。このため、上位のコンポーネントが再描画されるとそれ以下の全てのコンポーネントが再描画されるためパフォーマンスに良くない。  
この再描画の伝搬を止めるためにメモ化コンポーネントを使用する。  
メモ化コンポーネントは、親コンポーネントで再描画が発生してもpropsやcontextの値が変化しない場合は親コンポーネントによる再描画が発生しない。

ex.
以下のコードでは、カウントが増加する度にParentとFizzが再描画される。Fizzの表示内容は、isFizzが変わる前後で変化するが、isFizzが前回と変わらない場合でも再描画される。  
一方、Buzzは、isBuzzが変化するタイミングでのみ再描画される。

```tsx
import React, { memo, useState } from 'react'

type FizzProps = {
    isFizz: boolean
}

// Fizzは通常の関数コンポーネント
// isFizzがtrueの場合はFizzと表示して、それ以外は何も表示しない
// isFizzの変化に関わらず、親が再描画されるとFizzも再描画される
const Fizz = (props: FizzProps) => {
    const { isFizz } = props
    console.log(`Fizzが再描画されました, isFizz=${isFizz}`)
    return <span>{isFizz ? 'Fizz' : ''}</span>
}

type BuzzProps = {
    isBuzz: boolean
}

// Buzzはメモ化した関数コンポーネント
// isBuzzがtrueの場合はBuzzと表示して、それ以外は何も表示しない
// 親コンポーネントが再描画されても、isBuzzが変化しない限りはBuzzは再描画しない
const Buzz = memo<BuzzProps>((props) => {
    const {isBuzz, onClick } = props
    console.log(`Buzzが再描画されました, isBuzz=${isBuzz}`)
    return (
        <span onClick={onClick}>{isBuzz ? 'Buzz' : ''}</span>
    )
})

// この形式でexportした時は、import { Parent } from ...で読み込む
export const Parent = () => {
    const [count, setCount ] = useState(1)
    const isFizz = count % 3 === 0
    const isBuzz = count % 5 === 0

    // この関数はParentの再描画の度に作成される
    // メモ化コンポーネントに関数やオブジェクトを渡すと、親の再描画によってコンポーネントの再描画が発生する
    const onBuzzClick = () => {
        console.log(`Buzzがクリックされました, isBuzz=${isBuzz}`)
    }
    console.log(`Parentが再描画されました, count=${count}`)
    return (
        <div>
            <button onClick={() => setCount((c) => c+1)}>+1</button>
            <p>{`現在のカウント: ${count}`}</p>
            <p>
                <Fizz isFizz={isFizz} />
                // 再描画の度にParentで新しく作られた関数がBuzzに渡されるため、再描画してしまう
                // この問題はuseCallbackやuseMemoで関数や値をメモ化することにより回避できる
                <Buzz isBuzz={isBuzz} onClick={onBuzzClick}/>
            </p>
        </div>
    )
}
```

しかし、メモ化コンポーネントに関数やオブジェクトを渡すと、また親の再描画によってコンポーネントの再描画が発生するようになる。
例えば、Buzzのpropsに新しくonClickというコールバック関数を追加すると、
再描画の度にParentで新しく作られた関数をBuzzに渡すことになり、Buzzも再描画されてしまう。
この場合に再描画を抑制するためには、同じ関数を渡す必要がある。
また、オブジェクトや配列などもコンポーネント内で作成すると、描画の度に新しいものが作られるため、再描画が発生する原因となる。

### useCallback

関数をメモ化するためのフック。

サンプルコード：子コンポーネントでボタンを定義して、ボタンには親からコールバック関数が与えられる。ボタンｗの押すとそれぞれの関数に応じてカウントを更新する。

```tsx
const f = useCallback(() => { 処理 }
```

```tsx
import React, { useState, useCallback } from "react";

type ButtonProps = {
  onClick: () => void;
};

// DecrementButtonは通常の関数コンポーネントでボタンを表示する
const DecrementButton = (props: ButtonProps) => {
  const { onClick } = props;
  console.log("DecrementButtonが再描画されました");
  return <button onClick={onClick}>Decrement</button>;
};

// IncrementButtonはメモ化した関数コンポーネントでボタンを表示する
const IncrementButton = React.memo((props: ButtonProps) => {
  const { onClick } = props;
  console.log("IncrementButtonが再描画されました");
  return <button onClick={onClick}>Increment</button>;
});

// DoubleButtonはメモ化した関数コンポーネントでボタンを表示する
const DoubleButton = React.memo((props: ButtonProps) => {
  const { onClick } = props;
  console.log("DoubleButtonが再描画されました");
  return <button onClick={onClick}>Double</button>;
});

export const UseCallback = () => {
  const [count, setCount] = useState(0);

  const decrement = () => {
    setCount((c) => c - 1);
  };
  const increment = () => {
    setCount((c) => c + 1);
  };
  // useCallbackを使って関数をメモ化する
  const double = useCallback(() => {
    setCount((c) => c * 2);
    // 第二引数はから配列なので、useCallbackは常に同じ関数を返す
  }, []);

  return (
    <div>
      <p>Count: {count}</p>
      {/* コンポーネントに関数を渡す */}
      <DecrementButton onClick={decrement} />
      {/* メモ化コンポーネントに関数を渡す */}
      <IncrementButton onClick={increment} />
      {/* メモ化コンポーネントにメモ化した関数を渡す */}
      <DoubleButton onClick={double} />
    </div>
  );
};

```

### useMemo

値のメモ化をするフック。第一引数に値を生成する関数を、第二引数に依存配列を渡す。
useCallbackと同様にuseMemoはコンポーネントの描画時に依存配列を比較する。  
依存配列の値が前回描画時と異なる場合は、関数を実行し、その結果を新しい値としてメモに保存する。
依存配列の値が全て同じであれば関数は実行されず、メモしている値を返す。

```tsx
import React, { useState, useMemo } form 'react'

export const UseMemoSample = () => {
    const [text, setText] = useState('')
    const [items, setItems] = useState<String[]>([])

    const onChangeInput = (e: React.ChangeEvent<HTMLInputElement>) => {
        setText(e.target.value)
    }

    const onClickButton = () => {
        setItems((prevItems) => {
            // 現在の入力値をitemsに追加する。この時、新しい配列を作成して保存する
            return [...prevItems, text]
        })
        // テキストボックスの入力値を空にする
        setText('')
    }

    // 再描画の度にitems.reduceを実行して結果を得る処理
    const numberOfCharacters1 = items.reduce((sub, item) => sub + item.length, 0)

    //useMemoを使いitemsが更新されるタイミングでitems.reduceを実行して結果を得る処理
    const numberOfCharacters2 = useMemo(() => {
        return items.reduce((sub, item) => sub + item.length, 0)
        // 第２引数の配列の中にitemsがあるため、itemsが新しくなった時だけ関数を実行してメモを更新する
    }, [items])

    return (
        <div>
            <p>UseMemoSsample</p>
            <div>
                <input value={text} onChange={onChangeInput} />
                <button onClick={onClickButton}>Add</button>
            </div>
            <div>
                {items.map((item, index) => (
                    <p key={index}>{item}</p>
                ))}
            </div>
            <div>
                <p>Total Number of Characers 1: {numberOfCharacters1}</p>
                <p>Total Number of Characers 2: {numberOfCharacters2}</p>
            </div>
        </div>
    )
}
```

## 副作用のためのフック - useEffect, useLayoutEffect -

- 副作用とは、以下のようなコンポーネントの描画とは直接関係ない処理のこと。  
  - 描画されたDOMを手動で変更
  - ログを出力
  - タイマーのセット
  - データの取得、等々・・・

### useEffect

副作用を実行するフック。「ある値が変わった時に限り、処理を実行する」関数。  

useEffectは副作用を実行するために使用するフック。上記の処理をそのまま関数コンポーネント内で実行すると処理の中で参照しているDOMが描画により置き換わったり状態を更新してまた再描画が発生したり、無限ループになる。

useEffectを使うと、propsやstateが更新され、再描画が終わった後に処理が実行される。  
依存配列を指定することで、特定のデータが変化した時だけ処理をおこなうように設定できる。

```javascript
// useEffect(実行する関数, [依存する値1, 依存する値2]);
// num の値が変化した時に alert() を実行する
useEffect(() => { alert() }, [num1, num2]);
```

```javascript
import { useState } from "react";
const Effect = () => {
    const [num, setNum] = useState(0);

    useEffect(() => {
        alert();
    }, [num]);
}
```

useEffectは、依存配列に指定している値が変わった時に加え、コンポーネントのマウント時（初回レンダリング時）にも実行される。  
そのため、userEffectの第二引数に[]を設定すると、「コンポーネントを表示した初回のみ実行するような処理」を記述できる。  
この挙動は、画面を表示して初期データを取得する時などによく用いられる。

なぜこの機能があるかというと、コンポーネントは再レンダリングを何度も繰り返すためStateの数が多くなると再レンダリングのコストが無駄にかかる。  
そのため、特定の値が変わった時にだけ再レンダリングするよう制御することによってコストを減らせる。

**サンプル**
タイマーを表示し、タイマー表記をUSとJPで動的に切り替えられるコンポーネント

```tsx
import React, { useState, useEffect } from 'react'

// タイマーが呼び出される周期(1秒)
const UPDATE_CYCLE = 1000

// localstorage で使用するキー
const KEY_LOCALE = 'KEY_LOCALE'

enum Locale {
    US = 'en-US',
    JP = 'ja-JP',
}

const getLocaleFromString = (text: string) => {
    swich (text) {
        case Locale.US:
            return Locale.US
        case Locale.JP:
            return Locale.JP
        default:
            return Locale.US
    }
}

export const Clock = () => {
    const [timestamp, setTimestamp] = useState(Date.now())
    const [locale, setLocale] = useState(Locale.US)

    // タイマーのセットをするための副作用
    useEffect(() => {
        // setInterval()に渡したコールバック関数を指定した周期で実行する
        // これにより1秒毎に再描画がおこなわれ、現在時刻の表示を更新する
        const timer = setInterval(() => {
            setTimestamp(Date.now())
        }, UPDATE_CYCLE)

        // クリーンアップ関数*を返すことで、コンポーネントがアンマウントされる時にタイマーをクリアする
        return () => {
            clearInterval(timer)
        }
    }, []) // 初期描画時のみ実行
    
    // localstorageからlocaleを取得するための副作用
    useEffect(() => {
        const storedLocale = localStorage.getItem(KEY_LOCALE)
        if (storedLocale) {
            setLocale(getLocaleFromString(storedLocale))
        }
    }, []) // 初期描画時のみ実行

    // localeが変化した時にlocalstorageに保存するための副作用
    useEffect(() => {
        localStorage.setItem(KEY_LOCALE, locale)
    }, [locale]) // localeが変化した時に実行

    return (
        <div>
            <span>現在の時刻: {new Date(timestamp).toLocaleString(locale)}</span>
            <select value={locale} onChange={(e) => setLocale(getLocaleFromString(e.target.value))}>
                <option value={Locale.US}>US</option>
                <option value={Locale.JP}>JP</option>
            </select>
        </div>
    )
}
```

- クリーンアップ関数とは
  - useEffectが実行される直前、または、コンポーネントがアンマウントされる時に実行される
  - useEffectの戻り値として返す関数
  - タイマーやイベントリスナーを解除するためなどに用いる

上記のように、クリーンアップ関数でアンマウント時にタイマーを解除しないと、親コンポーネントでClockコンポーネント呼び出しが無くなり表示されなくなった後でもタイマー動作し続けることになるため、バグやメモリリークの原因となる。

localstorageの関数は同期的に実行され、読み込む/書き込むデータが大きいほど時間がかかる。描画関数中に直接localstorageを使用すると、描画の遅延が発生する。そのため、useEffectの中でlocalstorageを使用する。

### useLayoutEffect

useEffectとほぼ同じだが、useLayoutEffectは、実行されるタイミングが異なる。  

- useEffect
  - 描画関数が実行し、DOMが更新され、画面に描画された後で実行する
- useLayoutEffect
  - DOMが更新された後、画面に実際に描画される前に実行する

上記のサンプルコードの2つ目のuseEffectでは、localstorageに保存されている値を読み込んでlocaleに保存する。localeはuseStateで初期値が渡されている。そのため初期描画では、デフォルト値のUS表記で表示され、その直後でぉｃあｌｓとらげい保存されていた表記に変化する。
そのため、リロードする度に一瞬だけUS表記で表示されてしまう。
このコードの箇所をuseEffectからuseLayoutEffectに変更すると、初期描画が反映される前にlocalstorafeからデータが読み込まれるため、チラツキをなくすことができる。
しかし、useLayoutEffectで実行する処理は同期的に実行されるため、重い処理を実行すると画面への描画が送れるため、注意が必要。

```typescript
useLayoutEffect(() => {
    const storedLocale = localStorage.getItem(KEY_LOCALE)
    if (storedLocale) {
        setLocale(getLocaleFromString(storedLocale))
    }
}, [])
```

**メモ**

React18以降、`<React.StrictMode>`以下のコンポーネント内で宣言されたuseEffect,useLayoutEffectは安全でない副作用を見つけるために、コンポーネントは2回描画されるようになった。  
そのため空配列を渡した場合において、マウント時にuseEffectやuseLayoutEffectが2回呼ばれ、クリーンアップ関数も1回呼ばれることになる。  
1回のみの実行を保証したい場合は、useRefなどを使って前に実行されたかどうかを保持することで対処できる。  
なお、本番環境や`<React.StrictMode>`で囲まれていないコンポーネントに関しては、この挙動は発生しない。

### 再レンダリング

イベントをきっかけに画面表示内容が変わるのは、コンポーネントが再レンダリングされているためである。

```javascript
export const App = () => {
    console.log("レンダリング");
    const [num, setNum] = useState(0);
    ・・・
}
```

上記のコードを実行すると、初回画面表示時に「レンダリング」と出力され、numをカウントアップする度に「レンダリング」が追加で出力される。  
つまり、Stateが更新される度に毎回関数コンポーネントは再び頭から処理が実行される。  
（毎回と言っても、初回レンダリング（マウント）時と再レンダリング時は処理が異なり、useStateの初期値はマウント時だけ適用され、毎回初期化される訳ではない）。

## Contextのためのフック - useContext -

```tsx
import React, { useContext } from 'react'

type User = {
    id: number
    name: string
}

// ユーザーデータを保持するContextを作成する
const UserContext = React.createContext<User | null>(null)

const GrandChild = () => {
    // useContextにContextを渡すことで、Contextから値を取得する
    const user = useContext(UserContext)
    return user !== null ? <p>Hello, {user.name}</p> : null
}

const Child = () => {
    const now = new Date()

    return (
        <div>
            <p>Current: {now.toLocaleString()}</p>
            <GrandChild />
        </div>
    )
}

const Parent = () => {
    const user: User = {
        id: 1,
        name: 'Alice'
    }
    return (
        // Contextに値を渡す
        <UserContext.provider value={user}>
            <Child />
        </UserContext.provider>
    )
}
```

## ref用のフック - useRef, useImperativeHandle -

### useRef

useRefは、書き換え可能なrefオブジェクトを作成する。refには、大きく分けて2つの使い方がある。

1. データの保持  
  関数コンポーネント内でデータを保持するためには、useStatやuseReducerがあるが、これらは状態を行員する度に再描画が発生する。  
  一方、refオブジェクトに保存された値は更新しても再描画が発生しない。  
  そのため、描画に関係ないデータを保持する際に用いる。

2. DOM要素の参照  
  refをコンポーネントに渡すと、この要素がマウントされた時、ref.currentにDOMの参照がセットされ、DOMの関数などを呼び出すことができる。

**サンプルコード**
画像アップローダー
「画像をアップロード」と書かれたテキストをクリックすると、input要素がクリックされ、ファイル選択ダイアログが表示される。  
画像を選択した後に「アップロードする」ボタンをクリックすると、一定時間後にアップロードされたメッセージが表示される。  

```tsx
import React, { useState, useRef } from 'react'

const sleep = (ms: number) => new Promise((resolve) => setTimeout(resolve, ms))

const ImageUploader = () => {
    // 隠されたinput要素にアクセスするためのref
    const inputImageRef = useRef<HTMLInputElement | null>(null)
    // 選択されたファイルデータを保持するref
    const fileRef = useRef<File | null>(null)
    const [message, setMessage] = useState<string | null>('')

    // 「画像をアップロード」というテキストがクリックされた時のコールバック
    const onClickText = () => {
        if (inputImageRef.current) {
            // inputのDOMにアクセスして、クリックイベントを発火する
            inputImageRef.current.click()
        }
    }

    // ファイルが選択された後に呼ばれるコールバック
    const onChangeFile = async (e: React.ChangeEvent<HTMLInputElement>) => {
        const files = e.target.files
        if (files && files.length > 0) {
            // 選択されたファイルをrefに保持する
            // fileRef.currentが変化しても再描画は発生しない
            fileRef.current = files[0]
        }
    }

    // アップロードボタンがクリックされた時に呼ばれるコールバック
    const onClickUpload = async () => {
        if (fileRef.current) {
            setMessage('アップロード中...')
            await sleep(2000) // 通常はここでAPIを呼んで、ファイルをサーバーにアップロードする
            setMessage('アップロード完了')
        } else {
            setMessage('ファイルが選択されていません')
        }
    }

    return (
        <div>
            <button onClick={onClickText}>画像をアップロード</button>
            <input
                type="file"
                accept="image/*"
                ref={inputImageRef}
                onChange={onChangeFile}
                style={{ display: 'none' }} // input要素は非表示にする
            />
            <br />
            <button onClick={onClickUpload}>アップロード</button>
            {message && <p>{message}</p>}
        </div>
    )
}
```

### useImperativeHandle

コンポーネントにrefが渡された時に、親のrefに代入される値を設定するのに使う。  
useImperativeHandleを使うことで、小コンポーネントが持つデータを参照したり、子コンポーネントで定義されている関数を親から呼んだりできる。

**サンプルコード**
Parentコンポーネントの中のボタンをクリックすると、Childコンポーネントのmessageが更新されてメッセージが表示される。

```tsx
import React, { useState, useRef, useImperativeHandle, } from 'react'

const Child = React.forwardRef((props, ref) => {
    const [message, setMessage] = useState<string | null>('')

    // useImperativeHandleを使って、親のrefから参照できる値を指定
    useImperativeHandle(ref, () => ({
        // 親から呼び出される関数を定義
        showMessage: () => {
            const date = new Date()
            const message = `Hello, it's ${date.toLocaleString()} now`
            setMessage(message)
        },
    }))

    return <div>{message ? <p>{message}</p> : null}</div>
})

const Parent = () => {
    const childRef = useRef<{ showMessage: () => void }>(null)

    const onClickButton = () => {
        if (childRef.current) {
            // 子コンポーネントのsuseImperativeHandleで指定した値を参照
            childRef.current.showMessage()
        }
    }

    return (
        <div>
            <button onClick={onClickButton}>Show Message</button>
            <Child ref={childRef} />
        </div>
    )
}
```

上記のように、useImperativeHandleを使うことで、子コンポーネントの関数を親から呼び出すことができる。  
しかし、親コンポーネントが小コンポーネントに依存することになるため、あまり頻繁には使われない。  
多くの場合はpropsで代用でき、この場合はmessageをChildが保持するのではなく、Parentが持つことで解消できる。

## カスタムフックとuseDebugValue

フックは、ループ、条件分岐、コールバック関数の中では呼び出せない。このような場所でフックを呼び出すとビルドエラーまたは実行時エラーが発生する。  
これは、描画毎に呼び出されるフックの数と順番を同じにするためで、フックを使うためのルール。
カスタムフックを作成することで、フックをもう少し柔軟に使えるようになる。

**サンプルコード**
テキストボックスに文字を入力して、入力された文字が表示されるコンポーネントのサンプル。  
関数コンポーネント外でuseInputというカスタムフックを定義。

```tsx
import React, { useState, useCallback, useDebugValue } from 'react'

// input向けにコールバックと現在の入力内容をまとめたフック
const useInput = () => {
    // 現在の入力値を保持するフック
    const [state, setState] = useState('')
    // inputが変化したら、フック内の状態を更新する
    const onChange = useCallback((e: React.ChangeEvent<HTMLInputElement>) => {
        setState(e.target.value)
    }, [])
    // useDebugValueを使って、カスタムフックの状態を表示する
    // 値は、開発者ツールのComponentタブで確認できる
    useDebugValue(`Input: ${state}`)

    // 現在の入力内容とコールバック関数だけ返す
    return [state, onChange] as const
}

export const Input = () => {
    // useInputを使って、現在の入力内容とコールバック関数を取得する
    const [text, onChangeText] = useInput()

    return (
        <div>
            <input type="text" value={text} onChange={onChangeText} />
            <p>{`現在の入力内容: ${value}`}</p>
        </div>
    )
}
```

useInputでは、input要素のonnChangeが呼ばれたら内部の状態を更新するためuseStateとuseCallbackを組みあわせている。  
そして、必要なデータや関数だけreturnで返している。  
このコードを動かすと入力されたテキストと同じ内容がテキストボックスの下に表示される。  
カスタムフックを定義することで、関数コンポーネント内でのフックの定義がまとまりコードがきれいになるのみならず、  
複数のコンポーネントで使われるようなロジックを共有化できる。
