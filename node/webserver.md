# 簡易 Web サーバーを作ってみる

1. `express` パッケージをインストールする

    ```console
    $ mkdir webserver wwwroot
    $ cd webserver
    $ npm init -y
    $ npm install express
    ```
1. Web サーバーを作成する

    server.js
    ```JavaScript
    'use strict';

    const express = require('express');
    const app = express();

    app.use(express.static('../wwwroot'));
    app.listen(8001, ()=> {
        console.log('Start Server');
    });
    ```

1. Web サーバーを起動する

    ```console
    $ node server.js
    Start Server
    ```

    ※ 停止する場合は、`Ctrl+C`

1. `html` ファイルを用意する

    wwwroot/index.html
    ```
    <html lang="ja">
    <head>
        <meta charset="utf-8">
        <title>Hello world !</title>
    </head>
    <body>
      Hello world !
    </body>
    </html>
    ```

1. ブラウザでアクセスする

    http://localhost:8001/index.html
