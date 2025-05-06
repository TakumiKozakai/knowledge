# React_テクニック

# コンポーネントの型定義

## FC

関数コンポーネント自体の型を定義するもの。

```typescript
import type { FC } from "react";

export const ListItem: FC<User> = props => {
    return ...
}
```
