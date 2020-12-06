# Babelコマンド 

Babel を使って、es6の記法がes5の記法に変換する

## 環境

* Ubuntu 18.04
* node v14.15.1
* npm 6.14.8
* Babel 7.12.8

## 事前準備

* nodejs と npm をインストール

    [anyenv の導入](anyenv/Readme.md) を参考に `node` と `npm` をインストールする
    
    ```
    $ anyenv install nodenv
    $ exec $SHELL
    $ nodenv install 14.15.1
    $ nodenv rehash
    ```

* プロジェクトディレクトリの作成
    ```console
    $ mkdir sample-app
    $ npm init -y
    ```

 * Babel をインストール

    ```console
    $ npm install @babel/core @babel/cli @babel/preset-env --save-dev
    $ npx babel --version
    7.12.8 (@babel/core 7.12.9)
    ```

    `Babel 7` から スコープ付きパッケージ名に変更になった
    + `babel-cli` -> `@babel/cli`
    + `babel-core` -> `@babel/core`
    + `babel-preset-env` -> `@babel/preset-env`

* preset-env の設定

    ```console
    $ echo '{ "presets": ["@babel/preset-env"] }' > .babelrc
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
        cosole.log('function');
    };

    const data = {
        hoge: 'h',
        fuga: 'f',
        piyo: 'p',
        hogera: 'hr',
        hogehoge: 'hh'
    };

    const { hoge, fuga, piyo, ...other } = data;

    const foo = 'FOO';
    let bar = 'BAR';
    ```

## babel コマンド

* ファイルをコンパイルする

    標準出力に表示される
    ```console
    $ npx babel script.js
    ```

    es5の記法に変換された
    ```JavaScript
    "use strict";

    function _objectWithoutProperties(source, excluded) { if (source == null) return {}; var target = _objectWithoutPropertiesLoose(source, excluded); var key, i; if (Object.getOwnPropertySymbols) { var sourceSymbolKeys = Object.getOwnPropertySymbols(source); for (i = 0; i < sourceSymbolKeys.length; i++) { key = sourceSymbolKeys[i]; if (excluded.indexOf(key) >= 0) continue; if (!Object.prototype.propertyIsEnumerable.call(source, key)) continue; target[key] = source[key]; } } return target; }

    function _objectWithoutPropertiesLoose(source, excluded) { if (source == null) return {}; var target = {}; var sourceKeys = Object.keys(source); var key, i; for (i = 0; i < sourceKeys.length; i++) { key = sourceKeys[i]; if (excluded.indexOf(key) >= 0) continue; target[key] = source[key]; } return target; }

    function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }

    var App = function App() {
      _classCallCheck(this, App);

      console.log('constructor');
    };

    var func = function func() {
      cosole.log('function');
    };

    var data = {
      hoge: 'h',
      fuga: 'f',
      piyo: 'p',
      hogera: 'hr',
      hogehoge: 'hh'
    };

    var hoge = data.hoge,
        fuga = data.fuga,
        piyo = data.piyo,
        other = _objectWithoutProperties(data, ["hoge", "fuga", "piyo"]);

    var foo = 'FOO';
    var bar = 'BAR';
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

## その他のプリセット

* @babel/preset-env: ECMAScript用
* @babel/preset-flow: Flow用
* @babel/preset-react: React用
* @babel/preset-typescript: Typescript用
