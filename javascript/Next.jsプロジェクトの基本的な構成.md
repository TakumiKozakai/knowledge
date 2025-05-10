# Next.jsプロジェクトの基本的な構成

## プロジェクト構成

```
- node_modules
- pages
  - _app.tsx
  - api
    - hello.ts
  - index.tsx
- public
    - favicon.ico
    - vercel.svg
- styles
  - globals.css
  - Home.module.css
- README.md
- next-env.d.ts
- next.config.js
- package.json
- package-lock.json
- tsconfig.json
- .gitignore
- .eslintrc.json
- .prettierrc
- .prettierignore
```

*.module.css・・・コンポーネントを定義するファイルから読み込まれ、クラス名やIDが他のファイルで定義したものと衝突することを防ぐために、ファイル毎に、クラス名やIDへ窃盗時や設備時がビルド時に自動で付与される。
