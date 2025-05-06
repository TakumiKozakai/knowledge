# React_css

## Inline Styles

### 使用頻度

基本的には使わない。

### ルール

- プロパティ名をキャメルケースにする
- 値は文字列 or 数値

### 記述例

```javascript
const Sample = () => {
    const ContainerStyle = {
        width: "100%",
        padding: "16px",
    }

    const textStyle = {
        color: "blue",
        textAlign: "center",
    }

    return (
        <div style={ContainerStyle}>
            <p style={{ color: "red", textAlign: "left" }}>Inline Styles</p>
            <p style={textStyle}>Inline Styles</p>
        </div>
    )
}
```

## CSS Modules

### 使用頻度

使う。

### ルール

- .css や .scss ファイルを定義する
- コンポーネント毎にcssファイルを用意する
- .scss 形式を利用する場合は、「node-sass」をnpmからインストールする

### 記述例

```css : CssModules.module.scss
.container {
    border: solid 1px #aaa;
}
.title {
    margin: 0;
}
.button {
    background-color: #ddd;
}
```

```javascript
import classes from "./CssModules.module.scss";

export const Sample () => {
    return (
        <div className={classes.container}>
            <p className={classes.title}>CSS Modules</p>
            <button className={classes.button}>ボタン</p>
        </div>
    );
}
```

## Styled JSX

### 使用頻度

あまり使わない。

### ルール

- コンポーネントファイルにCSSの記述をする
- 「styled-jsx」をnpmからインストールする（Next.jsの場合は、標準で組み込まれているためインストール不要）

### 記述例

あまり使わないのと利用シーンが限られるため、割愛。

## Styled Components

### 使用頻度

使う。

### ルール

- スタイルを適用したコンポーネントを定義する
- コンポーネント名のprefixを「S」とする（styled componentsであることを明示）※プロジェクトに依る
- 「styled-components」をnpmからインストールする

### 記述例

```javascript
import styled from "styled-components";

export const StyledComponents = () => {
    return (
        <SContainer>
            <STitle>styled components</STitle>
            <SButton>ボタン</SButton>
        </SContainer>
    )
}

const SContainer = styled.div`
    border: solid 1px #aaa;
`;
const STitle = styled.p`
    margin: 0;
`;
const SButton = styled.button`
    background-color: #ddd;
`;
```

## Emotion

### 使用頻度

使うが、柔軟性が高いため、チームとしてのベストプラクティスが定まっていないとスパゲティになる。

### ルール

- Inline Styles、Styled JSX、styled components 全てに似ている
- 「@emotion/styled」をnpmからインストールする

### 記述例

```javascript
import { jsx, css } from "@emotion/react";
import styled from "@emotion/react";

export const Emotion = () => {
    // scssの書き方がそのまま可能な書き方
    const containerStyle = css`
        border: solid 1px #aaa;
    `;

    // インラインスタイルの書き方
    const titleStyle = css({
        margin: 0,
        color: "#aaa"
    });

    return (
        <div css={containerStyle}>
            <p css={titleStyle}>Emotion</p>
            <SButton>ボタン</SButton>
        </div>
    )
}

// styled-componentsの書き方
const SButton = styled.button`
    background-color: #ddd;
`;
```

## Tailwind CSS

### 使用頻度

人気のCSSフレームワーク。

### ルール

- classNameにTailwind CSSが提供するクラス名を指定する
- 「tailwindcss」をnpmからインストールする
- 「CRACO」をnpmからインストールする
- package.jsonにCRACOを使用して起動するように設定を記述する
- craco.config.jsonを作成する
- tailwind.config.jsを作成する
  - `npx tailwindcss init` コマンドを実行
- index.cssに設定を追記する

### 記述例

```javascript
export const TailwindCss = () => {
    return (
        <div className="border border-gray-400 rounded-2xl p-2 m-2 flex justify-around items-center">
        </div>
    )
}
```
