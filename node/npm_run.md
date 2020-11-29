# `node` モジュールの実行

* `node` モジュールを実行

    * パスを指定してモジュールを実行する
        ```
        $ ./node_modules/.bin/パッケージ名
        ```
    
    * `npm` コマンドからPATHを取得
        ```
        $ $(npm bin)/パッケージ名
        ```

    * PATH を通す
        ```
        $ export PATH=$PATH:./node_modules/.bin
        $ パッケージ名
        ```

    * `package.json` 内の `npm-scripts` で定義したタスク
        ```
        $ npm run タスク名 
        ```

    * `npx` コマンド で実行

        npm 5.2.0 以降で利用可能
        ```
        $ npx パッケージ名
        ```

        `npx` コマンドをたたくと次の順番でコマンドを探す
        1. ローカルパッケージ（`node_modules/.bin`）
        1. 環境変数 `PATH`
        1. `npm` レジストリ