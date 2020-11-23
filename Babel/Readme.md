# Babelコマンド 

## 環境

* Ubuntu 18.04
* node v8.10.0
* npm 3.5.2

## 事前準備

* nodejs をインストール
    ```
    $ sudo apt install nodejs
    ```
* npm をインストール
    ```
    $ sudo apt install npm
    ```
* プロジェクトディレクトリの作成
    ```
    $ mkdir sample-app
    $ npm init -y
    ```

 * Babel CLI のインストール

    ```
    $ npm install --save-dev babel-cli
    $ export PATH=$PATH:./node_modules/.bin
    $ babel --version
    6.26.0 (babel-core 6.26.3)
    ```

* babel-preset-es2015 のインストール

    ```
    $ npm install --save-dev babel-preset-es2015
    $ echo '{ "presets": ["es2015"] }' > .babelrc
    ```

* js ファイルの準備

    ```
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
    ```
    $ babel script.js
    ```

* コンパイル結果を指定したファイルに出力する

    `––out-file` または `-o` オプションをつける
    ```
    $ babel script.js --out-file script-compiled.js
    ```

* ファイルの変更を監視してコンパイルする

    `––watch` または `-w` オプションをつける
    ```
    $ babel script.js --watch --out-file script-compiled.js
    ```
* コンパイル時にソースマップも作成する

    `––source-maps` または `-s` オプションをつける
    ```
    $ babel script.js --out-file script-compiled.js --source-maps
    ```

    コンパイルしたjsファイルにソースマップを含める場合は   
    `––source-maps inline` オプションをつける
    ```
    $ babel script.js --out-file script-compiled.js --source-maps inline
    ```

* ディレクトリごとコンパイルする

    `––out-dir` または `-d` オプションをつけ、ディレクトリ名を指定する
    ```
    $ babel src --out-dir lib
    ```

    `––out-file` オプションをつけると、一つのファイルに結合して出力する
    ```
    $ babel src --out-dir lib
    ```

* コンパイル時にファイルを圧縮するオプション

    `––compact=true` オプションをつける
    ```
    $ babel script.js --compact=true -o script-compiled.js
    ```