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






