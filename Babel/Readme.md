# Babelコマンド 

Babel を使って、es6の記法がes5の記法に変換する

## 環境

* Ubuntu 18.04
* node v14.15.1
* npm 6.14.8

## 事前準備

* nodejs をインストール
    ```console
    $ sudo apt install nodejs
    ```
* npm をインストール
    ```console
    $ sudo apt install npm
    ```
* プロジェクトディレクトリの作成
    ```console
    $ mkdir sample-app
    $ npm init -y
    ```

 * Babel CLI のインストール

    ```console
    $ npm install --save-dev babel-cli
    $ npx babel --version
    6.26.0 (babel-core 6.26.3)
    ```

* babel-preset-env のインストール

    ```console
    $ npm install babel-preset-env --save-dev
    $ echo '{ "presets": ["env"] }' > .babelrc
    ```

* js ファイルの準備

    ```JavaScript
    # script.js

    class App {
        constructor() {
            console.log('constructor');
        }
    }

    const func = () => {
        console.log('function');
    };
    ```

## babel コマンド

* ファイルをコンパイルする

    標準出力に表示される
    ```console
    $ npx babel script.js
    ```

    es5の記法に変換された
    ```JavaScript
    'use strict';

    function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }

    var App = function App() {
        _classCallCheck(this, App);

        console.log('constructor');
    };

    var func = function func() {
        cosole.log('function');
    };
    ```
* コンパイル結果を指定したファイルに出力する

    `––out-file` または `-o` オプションをつける
    ```
    $ npx babel script.js --out-file script-compiled.js
    ```

* ファイルの変更を監視してコンパイルする

    `––watch` または `-w` オプションをつける
    ```
    $ npx babel script.js --watch --out-file script-compiled.js
    ```
* コンパイル時にソースマップも作成する

    `––source-maps` または `-s` オプションをつける
    ```
    $ npx babel script.js --out-file script-compiled.js --source-maps
    ```

    コンパイルしたjsファイルにソースマップを含める場合は   
    `––source-maps inline` オプションをつける
    ```
    $ npx babel script.js --out-file script-compiled.js --source-maps inline
    ```

* ディレクトリごとコンパイルする

    `––out-dir` または `-d` オプションをつけ、ディレクトリ名を指定する
    ```
    $ npx babel src --out-dir lib
    ```

    `––out-file` オプションをつけると、一つのファイルに結合して出力する
    ```
    $ npx babel src --out-dir lib
    ```

* コンパイル時にファイルを圧縮するオプション

    `––compact=true` オプションをつける
    ```
    $ npx babel script.js --compact=true -o script-compiled.js
    ```