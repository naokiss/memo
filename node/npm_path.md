# `node` の `PATH` 確認

* `npm` の ディレクトリ グルーバル `bin` のパスを確認する方法

    ```
    $ npm bin -g
    /home/taro/.anyenv/envs/nodenv/versions/14.15.1/bin
    ```

* モジュールがグローバルインストールされているパス確認する方法

    ```
    $ npm root -g
    home/taro/.anyenv/envs/nodenv/versions/14.15.1/lib/node_modules
    ```

* `node_modules` の検索パスを確認するする方法

    ```
    $ node
    Welcome to Node.js v14.15.1
    Type ".help" for more information.
    > global.module.paths
    [
      '/home/taro/hoge/repl/node_modules',
      '/home/taro/hoge/node_modules',
      '/home/taro/node_modules',
      '/home/node_modules',
      '/node_modules',
      '/home/taro/.node_modules',
      '/home/taro/.node_libraries',
      '/home/taro/.anyenv/envs/nodenv/versions/14.15.1/lib/node'
    ]
    ```
* グローバルインストールしたモジュールが読み込めない場合

    `hello` パッケージをグローバルにインストールする
    ```
    $ npm install -g hello
    ```

    `hello` を呼び出すモジュールを作成する
    ```JavaScript
    const hell = require('hello');

    console.log(hello);    
    ```

    実行するも失敗する
    ```
    $ node index.js
    internal/modules/cjs/loader.js:883
    throw err;
    ^

    Error: Cannot find module 'hello'
    Require stack:
    - /home/taro/index.js
        at Function.Module._resolveFilename (internal/modules/cjs/loader.js:880:15)
        at Function.Module._load (internal/modules/cjs/loader.js:725:27)
        at Module.require (internal/modules/cjs/loader.js:952:19)
        at require (internal/modules/cjs/helpers.js:88:18)
        at Object.<anonymous> (/home/taro/index.js:1:15)
        at Module._compile (internal/modules/cjs/loader.js:1063:30)
        at Object.Module._extensions..js (internal/modules/cjs/loader.js:1092:10)
        at Module.load (internal/modules/cjs/loader.js:928:32)
        at Function.Module._load (internal/modules/cjs/loader.js:769:14)
        at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:72:12) {
    code: 'MODULE_NOT_FOUND',
    requireStack: [ '/home/taro/index.js' ]
    ```

    グローバルインストールのパスを確認
    ```
    $ npm root -g
    /home/taro/.anyenv/envs/nodenv/versions/14.15.1/lib/node_modules
    ```

    `node_modules` の検索パスを確認すると   
    グローバルインストールのパスが含まれていない
    ```
    $ node
    Welcome to Node.js v14.15.1.
    Type ".help" for more information.
    > global.module.paths
    [
      '/home/taro/repl/node_modules',
      '/home/taro/node_modules',
      '/home/node_modules',
      '/node_modules',
      '/home/taro/.node_modules',
      '/home/taro/.node_libraries',
      '/home/taro/.anyenv/envs/nodenv/versions/14.15.1/lib/node'
    ]
    ```

    環境変数 `NODE_PATH` にグローバルインストールパスを設定する
    ```
    $ export NODE_PATH=`npm root -g`
    ```
    
    VSCode の場合は `.vscode/launch.json` に `NODE_PATH` を追加
    ```
    ...... 中略 ......
    {
        "type": "node",
        "request": "launch",
        "name": "VSCodeでのデバッグ",
        "program": "${workspaceFolder}/hoge/index.js",
        "env": {
            "NODE_ENV": "development", 
            "NODE_PATH": "/home/taro/.anyenv/envs/nodenv/versions/14.15.1/lib/node_modules"
        }
    }
    ...... 中略 ......
    ```

    もう一度 `node_modules` の 検索パス を確認すると   
    グローバルインストールのパスが追加されたのを確認！
    ```
    $ node
    Welcome to Node.js v14.15.1.
    Type ".help" for more information.
    > global.module.paths
    [
      '/home/taro/repl/node_modules',
      '/home/taro/node_modules',
      '/home/node_modules',
      '/node_modules',
      '/home/taro/.anyenv/envs/nodenv/versions/14.15.1/lib/node_modules',
      '/home/taro/.node_modules',
      '/home/taro/.node_libraries',
      '/home/taro/.anyenv/envs/nodenv/versions/14.15.1/lib/node'
    ]
    ```

    実行すると、今度はうまくいった
    ```
    $ node index.js
    {
      App: [class App extends Application],
      Config: [class Config],
      Controller: [class Controller],
      Index: [Function: helloIndex],
      Model: [class HelloModel extends Model],
      Router: [class Router extends Router]
    }
    ```
