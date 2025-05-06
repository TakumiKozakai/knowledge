# React Context について

Reactでは、データの受け渡しをprops/childrenでおこなう。
しかし、コンポーネントが親>子だけなく、親>子>孫とある場合、
propsをバケツリレーしなくてはならない。
この場合に、親の持つデータを孫が直接参照できるようにする仕組みとして、
Contextというアーキテクチャがある。
例えば、ログインユーザー情報をログイン後の各種画面で利用したい場合やサイトタイトルを各画面で表示したい場合などに用いる。

## Contextの利用方法

1. createContext()でContextを作成。第一引数は、デフォルト値。
2. データを渡す時は、Context.Provider を使う
3. データを参照する時は、Context.Consumer を使う

```tsx
// Titleを渡すためのContextを作成
const TitleContext = React.createContext('')

// Titleコンポーネントの中でContextの値を参照
const Title = () => {
    // Consumerを使って、Contextの値を参照
    return (
        <TitleContext.Consumer>
            {/* Consumer直下に関数を置いて、Contextの値を参照 */}
            {(title) => {
                return <h1>{title}</h1>
            }}
        </TitleContext.Consumer>
    )
}

const Header = () => {
    return (
        <div>
            {/* HeaderからTitleへは何もデータを渡さない */}
            <Title />
        </div>
    )
}

// Pageコンポーネントの中でContextに値を渡す
const Page = () => {
    const title = 'React Book'

    // Providerを使い、Contextに値をセット
    return (
        <TitleContext.Provider value={title}>
            <Header />
        </TitleContext.Provider>
    )
}

export defalut Page
```
