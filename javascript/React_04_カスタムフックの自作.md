# React_カスタムフックの自作

## カスタムフックとは

公式フックを組み合わせた独自のフックのこと。

## 以下のアプリケーションにてフックを自作する

- ボタン押下でユーザー情報を取得
- 取得中は「データ取得中です」を表示
- エラーが発生した場合では赤色で「エラーが発生しました」を表示
- 苗字と名前は半角空白を空けて結合して表示

## ロジックとUIを分離する

変更前のロジックに対してカスタムフックを自作することで疎結合にする。

## 変更前のアプリ

ユーザー情報

```
[
    {
        "id": 1,
        "firstName": "勉",
        "lastName": "主田",
        "age": 24
    }
]
```

ロジック

```javascript
/* App.jsx */
import { useState } from "react";
import axios from "axios";

export const App = () => {
    const [userList, setUserList] = useState([]);
    const [isLoading, setIsLoding] = useState(false);
    const [isError, setIsError] = useState(false);

    // ユーザー情報取得処理
    const onClickFetchUser = () => {
        setIsLoading(true);
        setIsError(false);

        // ユーザー情報取得
        axios
        .get("https://example.com/users")
        .then(res => {
            const users = res.data.map(user => ({
                id: user.id,
                name: `${user.lastName} ${user.firstName}`,
                age: user.age
            }));
            setUserList(users);
        })
        .catch(() => setIsError(true))
        .finally(() => setIsLoading(false));
    };

    return (
        <>
            <button onClick={onClickFetchUser}>ユーザー取得</button>
            {/* エラーの場合はエラ〜メッセージを表示 */}
            {isError && <p style={{ color: "red" }}>エラーが発生しました</p>}
            {/* ローディング中は表示を切り替える */}
            {isLoading ? (
                <p>データ取得中です</p>
            ): (
                userList.map(user => (
                    <p key={user,id}>{`${user,id}: ${user.name}(${user.age}歳)`}</p>
                ))
            )}
        </>
    );
};
```

## 変更後のアプリ

カスタムフック

```javascript
/* 
   userFetchUsers.js
   ユーザー情報を取得するカスタムフック
*/
import { useState } from "react";
import axios from "axios";

export const useFetchUsers = () => {
    const [userList, setUserList] = useState([]);
    const [isLoading, setIsLoding] = useState(false);
    const [isError, setIsError] = useState(false);

    const onClickFetchUser = () => {
        setIsLoading(true);
        setIsError(false);

        // ユーザー情報取得
        axios
        .get("https://example.com/users")
        .then(res => {
            const users = res.data.map(user => ({
                id: user.id,
                name: `${user.lastName} ${user.firstName}`,
                age: user.age
            }));
            setUserList(users);
        })
        .catch(() => setIsError(true))
        .finally(() => setIsLoading(false));
    };

    // まとめて返却するためにオブジェクトにする(省略記法)
    return {
        userList,
        isLoading,
        isError,
        onClickFetchUser
    };
}
```

ロジック

```javascript
/* App.jsx */
import { useState } from "react";
// import axios from "axios";
import { useFetchUsers } from "./hooks/useFetchUsers";

export const App = () => {
    // カスタムフックの使用
    // 関数を実行して返却値を分割代入で受け取る
    const { userList, isLoading, isError, onClickFetchUser } = useFetchUsers();
//    const [userList, setUserList] = useState([]);
//    const [isLoading, setIsLoding] = useState(false);
//    const [isError, setIsError] = useState(false);

    // ユーザー情報取得処理
//    const onClickFetchUser = () => {
//        setIsLoading(true);
//        setIsError(false);

//        // ユーザー情報取得
//        axios
//        .get("https://example.com/users")
//        .then(res => {
//            const users = res.data.map(user => ({
//                id: user.id,
//                name: `${user.lastName} ${user.firstName}`,
//                age: user.age
//            }));
//            setUserList(users);
//        })
//        .catch(() => setIsError(true))
//        .finally(() => setIsLoading(false));
//    };

    return (
        <>
            <button onClick={onClickFetchUser}>ユーザー取得</button>
            {/* エラーの場合はエラ〜メッセージを表示 */}
            {isError && <p style={{ color: "red" }}>エラーが発生しました</p>}
            {/* ローディング中は表示を切り替える */}
            {isLoading ? (
                <p>データ取得中です</p>
            ): (
                userList.map(user => (
                    <p key={user,id}>{`${user,id}: ${user.name}(${user.age}歳)`}</p>
                ))
            )}
        </>
    );
};
```
