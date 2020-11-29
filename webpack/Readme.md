# webpack  

## 環境

* Ubuntu 18.04
* node v14.15.1
* npm 6.14.8

## 事前準備

* nodejs と npm をインストール

    [anyenv の導入](anyenv/Readme.md) を参考に `node` と `npm` をインストールする
    
    ※ `Ubuntu 18.04` で `apt` コマンドでインストールするとかなり古いバージョンがインストールされるため
    ```
    $ anyenv install nodenv
    $ exec $SHELL
    $ nodenv install 14.15.1
    $ nodenv rehash
    ```

* プロジェクトディレクトリの作成
    ```
    $ mkdir sample-app
    $ npm init -y
    ```

* webpack のインストール

    ```
    $ npm install webpack webpack-cli --save-dev
    $ npx webpack --version

    webpack-cli 4.2.0

    webpack 5.6.0
    ```

## ファイルを準備

* ディレクトリ構成

    ```
    /
    ├── package.json
    ├── public
    │   ├── index.html
    │   └── js
    │       └── bundle.js
    ├── src
    │   └── js
    │       ├── index.js
    │       └── modules
    │           ├── hello.js
    │           └── tax.js
    └── webpack.config.js
    ```

* webpack.config.js

    ```JavaScript
    const path = require('path');

    module.exports = {
        // モード( development/production/none )
        mode: 'development',
        // エントリーポイント
        entry: './src/js/index.js',
        // 出力先
        output: {
            // 出力ファイル名
            filename: 'bundle.js',
            // 出力ディレクトリ名
            path: path.join(__dirname, 'public/js')
        }
    }
    ```

* index.js

    ```JavaScript
    ```




