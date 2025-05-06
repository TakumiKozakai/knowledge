# TypeScript

## TypeScriptってなに

静的な型付け機能を搭載したAltJS*。  
静的型付けによりコンパイル時エラー検出や型によるコードの安全性向上を実現する。  

*AltJS：JavaScriptに変換できるプログラミング言語のこと。

## 　TypeScriptの実行環境準備

1. Node.jsをインストールする（同時に、パッケージ管理ツールのnpmもインストールされる）
2. TypeScriptコマンドラインツールをnpmを通してインストールする

    ```
    npm install -g typescript
    ```

3. TypeScriptのコンパイルを実行する

    ```
    tsc --strictNullChecks hoge.ts
    ```

4. 正常にコンパイルできると、hoge.jsファイルが同じディレクトリ上に生成される。

    補足）実際のWebアプリケーション開発では、Next.jsなどのフレームワークがコンパイルを実行してくれるため、tscコマンドを用いてファイルを個別にコンパイルする機会は通常ない。

## 型の定義

### 変数

プリミティブ

```typescript
let str: string = "A";
let num: number = 0;
let bool: boolean = true;
```

配列

```typescript
let arr1: number[] = [0, 1, 2];
arr1.push(3);

const arr2: Array<number> = [0, 1, 2];
```

オブジェクト型・・・キーとバリューによるデータ形式のインスタンス

```typescript
const obj: {str: string, num: number} = {
    str: "A",
    num: 0,
}

obj.str = "B";
obj.num = 1;
```

特殊型

```typescript
// nullのみ設定可能
let null1: null = null;

// undefinedのみ設定可能
let undefined1: undefined = undefined;

// anyは、どんな値でも設定可能（基本は使わない）
let any1: any;
```

union型・・・複数の型を許容。変数・引数・戻り値・型エイリアスにて指定可能。

```typescript
// string、もしくは、numberを受け付ける
let val1: string | number = "";
val1 = "A";
val1 = 0;

const mixedArrayU: (string | number)[] = ['foo', 1];
```

intersection型（複合型）・・・複数の型をマージ。変数・引数・戻り値・型エイリアスにて指定可能。

```typescript
type Identity = {
    id: number | string;
    name: string;
}

type Contact = {
    name: string;
    email: string;
    phone: string;
}

type Employee = Identity & Contact

const employee: Employee = {
    id: '111',
    name: 'Takuya',
    email: 'test@example.com',
    phone: '09012345678'
}
```

リテラル型・・・特定の値のみを指定

```typescript
変数: 許可するデータ1 | 許可するデータ2 | ・・・

// ex.1 ステータスを指定
let postStatus: 'draft' | 'published' | 'deleted'
postStatus = 'draft' // OK
postStatus = 'drafts' // コンパイルエラー

// ex.2 関数の戻り値を指定
function compare(a: string, b: string): -1 | 0 | 1 {
    return a === b ? 0 : a > b ? 1 : -1
}
```

never型・・・決して発生しない値の種類を示す。

```typescript
// エラーが常に起こるような関数で決して値が正常に返らない場合に指定
function error(message: string): never {
    throw new Error(message)
}

// ex.1 条件分岐に漏れがないことを保証
// 将来、PageTypeが新しく追加された際に分岐が漏れているとコンパイルエラーとなる
enum PageType {
    ViewProfile,
    EditProfile,
    ChangePassword,
}

const getTitleText = (type: PageType) => {
    switch (type) {
        case PageType.ViewProfile:
            return 'View Profile'
        case PageType.EditProfile:
            return 'Edit Profile'
        case PageType.ChangePassword:
            return 'Change Password'
        default:
            // 決して起きないことをコンパイラに伝えるnever型に代入
            // これによって仮に将来PageTypeのenum型に定数が新規追加された際に
            // コンパイルエラーが起きるためバグを未然に防げる
            const wrongType: never = type
            throw new Error(`${wrongType} is not in PageType`)
    }
}
```

タプル・・・複数の型を許容

```typescript
const mixedArrayT: [string, number] = ['foo', 1];
```

インデックス型・・・オブジェクトのキー名を明記せずに型エイリアスを定義できる。
キー名やキー数が事前に定まらないケースで用いる。

```typescript
{ [] : 型名 }

type Label = {
    [key: string] : string // キーの型と値の型をstringで指定
}

const labels: Label = {
    topTitle: 'トップページのタイトル',
    topSubTitle: 'トップページのサブタイトル'
}

const hoge: Label = {
    message: 100 // 値部分の型が合わないためエラー
}
```

Enum

```typescript
// ex.1
enum Direction {
    Up,
    Down,
    Left,
    Right
}

let direction: Direction = Direction.Up
console.log(direction) // 0

direction = 'Left' // エラー

// ex.2
enum Direction {
    Up = 'UP',
    Down = 'DOWN',
    Left = 'LEFT',
    Right = 'RIGHT'
}

// APIのパラメータとして文字列が渡されるケース
const value = 'DOWN'

// 文字列からEnum型に変換
const enumValue = value as Direction

if (enumValue === Direction.Down) {
    console.log('Down is selected')
}
```

### 関数

基本の書き方

```typescript
// func(引数: 引数の型名): 返却値の型名 => {}
function func2(name: string): string {
    return `Hello ${name}`;
}

func2('World')
```

アロー関数を用いた代入

```typescript
// ex.1
const func1 = (num: number): void => {
}

func1(1);

// ex.2
const sayHello = (name: string): string => `Hello ${name}`;

func1('Takumi');
```

関数の引数に関数を渡す (引数名： 引数の型) => 戻り値の型

```typescript
// ex.1
function printName(firstName: string, formatter: (name: string) => string) {
    formatter(firstName) // ここで引数のformatterという名称で渡された関数formatNameが実行される
}

function formatName(name: string): string {
    return `${name} san`
}

printName('Takumi', formatName) // "Takumi san"

// ex.2
function genBirdsInfo(name: string): string[] {
    return name.split(',')
}

function singBirds(birdInfo: (x: string) => string[]): string {
    return birdInfo('hato, kiji')[0] + ' piyo piyo'
}

singBirds(genBirdsInfo) // "hato piyo piyo"
```

## 基本的な型の機能

### 省略可能な型の定義

?を付けると省略可能なプロパティとなる。省略されていないプロパティを除いた生成をおこなうとエラーになる。

```typescript
function printName(obj: { firstName: string, lastName?: string}) {
    ...
}

// 以下、どちらでも動作する。
printName({firstName: 'Takuya' })
printName({firstName: 'Takuya', lastName: 'Tejima'})
```

### 型推論

TypeScriptでは、明示的な変数の初期化をおこなうと、型推論により自動的に型が決定される。

```typescript
const age = 10
console.log(age.length) // ageはnumber型になるため、lengthプロパティは使えない
```

関数の戻り値も同様。

```typescript
function getUser() {
    return {
        name: 'Takuya',
        age: 20
    }
}

const user = getUser()
console.log(user.age.length) // ageはnumber型になるため、lengthプロパティは使えない
```

配列の値も推論される。

```typescript
const names = ['Takuya', 'Yoshiki', 'Taketo']

names.forEach(name => {
    console.log(name.toUpperCase()) // 正しくは、toUpperCaseのため、コンパイルエラーになる
})
```

代入する値と型が一致しない場合にエラーにする推論機能もある。

```typescript
// window.confirm関数の返り型はbooelanを返すことをTypeScriptは知っているため
// 代入する関数の型が一致しない場合コンパイル時エラーになる
window.confirm = () => {
    // booleanをreturnしない限りエラーになる
    console.log('confirm関数')
}
```

### 型アサーション

以下のコードのように、main_canvasがcanvas以外（例えばdiv）だった場合、コンパイルエラーになる。  
もし開発者がmain_canvasがcanvasであることを事前に把握できている場合は、as で型指定する型アサーション機能を利用できる。

```typescript
const myCanvas = document.getElementById('main_canvas')
console.log(myCanvas.width) // main_canvasがcanvas型でない場合コンパイルエラー
```

↓

```typescript
const myCanvas = document.getElementById('main_canvas') as HTMLCanvasElement
```

### type構文（型エイリアス）

型のエイリアス。

```typescript
type TypeA = {
    str: string;
    num: number;
}

type TypeB = {
    str: string;
    bool: boolean;
}

type TypeC = TypeA & TypeB;

const obj: TypeC = {
    str: "A",
    num: 10,
    bool: false,
}
```

関数のエイリアス。

```typescript
type Formatter = (a: string) => string

function printName(firstName: string, formatter: Formatter) {
    console.log(formatter(firstName))
}
```

### インターフェース

型の拡張が可能。型エイリアスと似ているが、=がいらない・必ず{で定義が始まる等の違いがある。  
インターフェースは、クラスの振る舞いを型定義して、implementsによりクラスの実装を与えること（委譲）が可能。

```typescript
interface 型名 {
    プロパティ: 型;
}

// ex.
interface Point {
    x: number;
    y: number;
}

function printPoint(point: Point) {
    console.log(`x座標は${point.x}です`)
    console.log(`y座標は${point.y}です`)
    console.log(`z座標は${point.z}です`)
}

interface Point {
    z: number;
}

printPoint({x: 100, y: 100}) // zが存在しないためコンパイルエラー

printPoint({x: 100, y: 100, z:200})
```

implementsを使って委譲。

```typesrcipt
interface Point {
    x: number;
    y: number;
    z?: number;
}

class MyPoint implements Point {
    x: number;
    y: number;
}
```

インターフェースは、extendsを使って他のインターフェースを拡張可能。

```typescript
interface Colorful {
    color: string;
}

interface Circle {
    radius: number;
}

interface ColorfulCircle extends Colorful, Circle {}

const cc: ColorfulCircle = {
    color: '赤',
    radius: 10
}
```

**オブジェクトの型を定義する際にインターフェースと型エイリアスどちらも利用可能で、継承に関する細かな機能の違いはあるもののほぼ同等の機能を持つ。  
ただし、TypeScriptの設計思想としてはこの2機能は少し異なる点がある。**

- インターフェース
  - クラスやデータの一側面を定義した型。つまり、インターフェースにマッチする型でもその値以外に他のフィールドやメソッドがある前提となる。
- 型エイリアス
  - オブジェクトの型そのものを表す。

**そのため、オブジェクトそのものではなく、クラスやオブジェクトの一部のプロパティや関数含む一部の振る舞いを定義するものであれば、インターフェースを利用するのが適している。**

### クラス

```typescript
class クラス名 {
    フィールド: 型;
}

// ex.
interface IPoint {
    x: number;
    y: number;
    moveX: (n: number) => void;
    moveY: (n: number) => void;
}

// インターフェースからクラスを生成
class Point implements IPoint {
    x: number;
    y: number;

    constructor() {
        this.x = 0;
        this.y = 0;
    }

    constructor(x: number = 0, y: number = 0) {
        this.x = x;
        this.y = y;
    }

    moveX(n: number): void {
        this.x += n
    }

    moveY(n: number): void {
        this.y += n
    }
}

// 継承してクラスを生成
class Point3D extends Point {
    z: number;

    constructor(x: number = 0, y: number = 0, z: number = 0) {
        super(x, y)
        this.z = z
    }

    moveZ(n: number): void {
        this.z += n
    }
}

const point3D = new Point3D()
point3D.moveX(10)
point3D.moveZ(20)
console.log(`${point3D.x}, ${point3D.y}, ${point3D.z}`) // 10, 0, 20
```

### アクセス修飾子

```typescript
class BasePoint3D {
    public x: number;
    private y: number;
    protected z: number;
}

// インスタンス化をおこなった場合のアクセス制御
const basePoint = new BasePoint3D()
basePoint.x // OK
basePoint.y // コンパイルエラー
basePoint.z // コンパイルエラー

// クラスを継承した場合のアクセス制御
class ChildPoint extends BasePoint3D {
    constructor() {
        super()
        this.x // OK
        this.y // コンパイルエラー
        this.z // OK
    }
}
```

### ジェネリクス

```typescript
class Queue<T> {

    private array: T[] = []

    push(item: T) {
        this.array.push(item)
    }

    pop(): T | undefined {
        return this.array.shift()
    }
}

const queue = new Queue<number>()
queue.push(111)
queue.push(112)
queue.push('hoge') // number型でないため、コンパイルエラーになる

let str = 'fuga'
str = queue.pop() // strはnumber型でないため、コンパイルエラーになる
```

## 　TypeScriptのテクニック

### Optional Chaining

```typescript
interface User {
    name: string
    social?: {
        facebook: boolean
        twitter: boolean
    }
}

let user: User

user = {
    name: 'Takuya',
    social: {facebook: true, twitter: true }
    }
console.log(user.social?.facebook) // trueが出力される

user = { name: 'Takuya' }
console.log(user.social?.facebook) // socialが存在しないケースでも以下のコードは実行時エラーにならない
```

### Non-null Assertion Operator

```typescript
// userがnullの場合、実行時エラーになる可能性があるプロパティへのアクセスはコンパイルエラーとなるが、
// !を用いて明示的に指定することでコンパイルエラーを回避できる　
function processUser(user?: User) {
    let userName = user!.name
}
```

### 型ガード

型ガードを用いることで、実行時エラーを引き起こしやすいasを使用する型アサーションよりも安全に型を利用したコードを書ける。

```typescript
function addOne(value: number | string) {
    if (typeof value === 'string') {
        return Number(value) + 1
    }
    // 　上記でstring型であることのチェックをおこなっているため、以下からはnumber型であるとみなせる
    return value + 1
}

console.log(addOne('21')) // 21
console.log(addOne(10)) // 11
```

### keyofオペレーター

型の持つ各プロパティの型のUnion型を返す。

```typescript
interface User {
    name: string;
    age: number;
    email: string;
}
type UserKey = keyof User // 'name' | 'age' | 'email' というUnion型となる

const key1: UserKey = 'name'
const key2: UserKey = 'phone' // コンパイルエラーになる

// 第一引数に渡したオブジェクトの型のプロパティ名のUnion型と、第二引数で渡す値の型が一致しない場合、型エラーになる
// T[K]によりキーに対応する型が戻り値の型となる。
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
    return obj[key]
}

const user: User = {
    name: 'Takuya',
    age: 36,
    email: 'test@example.com'
}

// nameは型のキーに存在するため正しくstring型の値が返る
const userName = getProperty(user, 'name')

// genderはオブジェクトのキーに存在しないため、コンパイルエラーになる
const userGender = getProperty(user, 'gender')
```

### readonly

型エイリアス、インターフェース、クラスに変更不可を指定するreadonlyプロパティを定義できる。

```typescript
type User = {
    readonly name: string;
    readonly gender: string;
}

let user: user = { name: 'Takuya', gender: 'Male'}

// 以下の代入を行った際にコンパイルエラーが発生する
user.gender = 'Female'
```

constは変数の再代入を制限して、readonlyはプロパティの再代入を制限する。  
Readonly型に型エイリアスを指定すると、全てのプロパティが変更不可の型が作成される。

```typescript
type User = {
    name: string;
    gender: string;
}

type UserReadonly = Readonly<User>

let user: User = { name: 'Takuya', gender: 'Male' }
let userReadonly: UserReadonly = { name: 'Takuya', gender: 'Male' }

user.name = 'Yoshiki'
userReadonly.name = 'Yoshiki' // コンパイルエラー
```

### unknown

anyと同じくどのような値でも代入できる型。anyと異なるのは、代入値はそのままアクセスできず、typeofやinstanceofなどを利用して型安全な状況を作ることで変数の値にアクセスできるようになる。

```typescript
const x: unknown = 123

console.log(x.toFixed(1)) // コンパイルエラー

if (typeof x === 'number') {
    console.log(x.toFixed(1)) // 123.0
}
```

つまり、より不明な値を示すことを強調した機能であり、anyではできなかったコンパイルエラーを事前に検知できるため、anyを使用するよりも安全なコードを書くことができる。

### 非同期のAsync/Await

Async/Awaitとは、非同期処理API「Promise」の簡易的にした機能。

```typescript
// 非同期関数の定義
function fetchFromServer(id: string): Promise<{success: boolean}> {
    return new Promise(resolve => {
        setTimeout(() => {
            resolve({success: true})
        }, 100)
    })
}

// 非同期処理を含むasync functionの戻り値の型は、Promiseとなる
async function asyncFunc(): Promise<string> {
    // Promiseな値をawaitすると、中身が取り出せる（ように見える）
    const result = await fetchFromServer('111')
    return `The result: ${result.success}`
}

// await構文を使うためにasync functionの中で呼び出す必要がある
(async () => {
    const result = await asyncFunc()
    console.log(result)
})()

// Promiseとして扱う際は、以下のように記述する
asyncFunc().then(result => console.log(result))
```

### 型定義ファイル

## TypeScriptの開発時設定

### tsconfig.json

TypeScriptではコンパイルに必要なオプションやコンパイル対象となるファイルの情報などをtsconfig.jsonに記述する。  
IDE・エディターでも、この設定ファイルを元に補完やエラーが検知される。  
tsc --init コマンドを実行することでデフォルトのtsconfig.jsonファイルが作成される。  
**配置場所は、プロジェクトルート**

```bash
tsc --init 
```

### Prettier

スペースのインデントの数を合わせたり、ダブルクオートもしくはシングルクオートに揃えたりなど、コードのフォーマットを自動でおこなってくれる。  
複数人で同じファイルを変更する際には共通のコードフォーマットを利用してPrettierを実行することでコンフリクトの少ない開発が可能となる。  

```bash
npm install prettier --save-dev
```

フォーマット設定は、.prettierrcというファイルに記述して、プロジェクトのディレクトリ配下に配置する。

```
// .prettierrc
{
    //コードの末尾にセミコロンを入れるか
    "semi":false,
    //オブジェクト定義の最後のカンマを無しにするか
    "trailingComma":"none",
    //文字列の定義などクオートにシングルクオートを使用するか
    "singleQuote":true,
    //行を改行する際の文字数
    "printWidth":80
}
```

package.json内のscriptsの項目に以下のように追記する。

```
// package.json
{
    "scripts":{
        ...
        "prettier-format": "prettier --config .prettierrc 'src/**/*.ts' --write"
    }
}
```

以下のコマンドを実行することで、src以下の全てのTypeScriptコードにフォーマットが実行される。

```bash
npm run prettier-format
```

### ESLint

JavaScriptやTypeScriptのコードを解析し、問題がある箇所を指摘してくれるツール。

### コンパイルオプション

noImplicitAny  
strictNullChecks  
target

### スタイルガイド

JavaScriptStandardスタイルガイド  
　セミコロンを省略する書き方が特徴のモダンなスタイルガイド  
Airbnbスタイルガイド  
　Airbnbが公開しているスタイルガイド。多くはベストプラクティスとなっている  
Googleスタイルガイド  
　Googleが公開しているスタイルガイド。TypeScriptについても公開されている  
TypeScriptDeepDiveスタイルガイド  
　TypeScriptで人気の高いスタイルガイド

## ライブラリの型定義

外部ライブラリの型定義に関しては、そのライブラリの対応状況に応じて3パターンに分かれる。

- パターン1 : 型定義がない  
ライブラリが古かったりすると、そもそも型定義が存在しないライブラリもあるため、この場合は自分で作成が必要。

- パターン2 : ライブラリが型定義を包含している  
自分での作成は不要で、インストールすれば型が効いている状態で使用できる。  
「型定義を包含してくれているかどうか」は、GitHubのリポジトリで「~.d.tsファイル」があるかどうかで判断できる。  
例えば、axiosではindex.d.tsファイルがある。
- パターン3 : 型定義を別でインストールする  
DefinitelyTypedというリポジトリで様々なライブラリの型定義が管理されている。  
管理下のライブラリはnpm installでインストールできる。
